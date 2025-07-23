---
layout:     post
title:      Runtime Dependency Conflict
excerpt:    Debug Runtime Dependency Conflict in monorepo + microrepo
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Java
---

I recently tackled a tricky `java.lang.NoSuchMethodError` caused by a transitive dependency conflict. It's a classic problem in a large monorepo + microrepo, and debugging it can be a journey. Here’s a breakdown of how I diagnosed and resolved the issue. For context here, I am doing a version upgrade for an internal microrepo of Pinot. The in-house pinot packages are released to company's internal maven central. In the monorepo, we write customized java main starters that import both pinot packages and company internal libs. The starters will be finally released as a managed database service.

## The Problem: A Wild `NoSuchMethodError` Appears

It all started with a failing unit test after a library upgrade in our main service.

```bash
# Unit Test Runtime Error
bazel test //services/my-service:test_main 
FAILED  com.my-company.my-service.broker.MyAuthFactoryTest.testInitWithAuthEnabled (0.1s)
````

The error was a `NoSuchMethodError`, which usually points to a binary incompatibility between JARs. At compile time, everything looks fine, but at runtime, the JVM can't find the specific method signature it expects.

```java
// Detailed Error
java.lang.NoSuchMethodError: 'org.some.vendor.expression.eval.pb.ProtoRegistry org.some.vendor.expression.eval.pb.ProtoRegistry.newRegistry(com.google.protobuf.Message[])'
```

The stack trace shows that our internal `auth-lib` was calling a method in an `expression-eval-lib`, but that method wasn't found at runtime.

-----

## The Investigation: Hunting for the Conflict

My first step was to find out where the `ProtoRegistry` class was coming from. Our internal dependency analysis tool showed two sources:

1.  A **shaded JAR** from our internal `security-lib`.
2.  A standard, non-shaded JAR from the `expression-eval-lib` itself.

<!-- end list -->

```bash
./find-deps --klass pb/ProtoRegistry
--- :building_construction: Building //tooling/ci/builder:builder binary :bazel: ...
Using built binary: //tooling/ci/builder:builder

Class: org/some/vendor/expression/eval/pb/ProtoRegistry.class
Maven Coords: com.my-company.security:security-lib-core-shaded:1.0.0
Visibility: //visibility:public
  ╰─ //3rdparty/jvm/com/my-company/security:security-lib-core-shaded-1.0.0.jar

Below are the matching external dependencies which cannot be directly used.
Follow http://internal-docs/java-deps if you want your project to depend on it directly.

Class: org/some/vendor/expression/eval/pb/ProtoRegistry.class
Maven Coords: org.some.vendor.expression:expression-eval-lib-core:0.3.2
Visibility: //:__pkg__, //3rdparty/jvm:__subpackages__
  ╰─ //3rdparty/jvm/org/some/vendor/expression:expression-eval-lib-core-0.3.2.jar
```

This is the classic diamond dependency problem. My `my-service` project depends on two different libraries (`auth-lib` and `security-lib`) which in turn bring in two different versions or variants of the same class (`ProtoRegistry`).

The dependency graph looked something like this:

  * `my-service` → `auth-lib` → `expression-eval-lib-core-0.3.2.jar`
  * `my-service` → `security-lib-core-shaded-1.0.0.jar` (which contains a repackaged, older version of `expression-eval-lib`)

At runtime, the class loader was picking up the version of `ProtoRegistry` from the shaded `security-lib` JAR, which was missing the `newRegistry(com.google.protobuf.Message[])` method signature that `auth-lib` expected.

-----

## The Confirmation: Disassembling the Bytecode 🧐

To be absolutely sure, I decided to look at the bytecode of the `ProtoRegistry` class from both JARs.

First, I found the runtime path to the shaded JAR and listed its contents.

```bash
# 1. Get the runtime classpath for the shaded JAR
bazel build //3rdparty/jvm/com/my-company/security:security-lib-core-shaded-1.0.0.jar --config classpath

# 2. Find the JAR path from the classpath file
cat bazel-bin/3rdparty/jvm/com/my-company/security/redacted_runtime_classpath.txt
# Output:
# bazel-out/k8-fastbuild/bin/3rdparty/jvm/com/my-company/security/_/jvm/v1/redacted.jar

# 3. Find the class path within the JAR
JAR_PATH=$(cat bazel-bin/3rdparty/jvm/com/my-company/security/redacted_runtime_classpath.txt | head -n 1)
jar -tf $JAR_PATH | grep 'ProtoRegistry'
# Output:
# org/some/vendor/expression/eval/pb/ProtoRegistry.class
```

Next, I used `javap` to disassemble the class from the **shaded `security-lib` JAR**. Notice the method signature: it expects a `securitylib.shaded.com.google.protobuf.Message` parameter because its own dependencies were repackaged (shaded).

```bash
javap -classpath $JAR_PATH org.some.vendor.expression.eval.pb.ProtoRegistry

# Output from the shaded JAR
public final class org.some.vendor.expression.eval.pb.ProtoRegistry ... {
  public static org.some.vendor.expression.eval.pb.ProtoRegistry newRegistry(securitylib.shaded.com.google.protobuf.Message...);
}
```

Then, I did the same for the **`expression-eval-lib` JAR** that `auth-lib` directly depends on. The method signature here is different; it uses the standard `com.google.protobuf.Message`.

```bash
# Disassembling the class from the direct dependency JAR
javap -classpath bazel-out/.../expression-eval-lib-core-0.3.2.jar org.some.vendor.expression.eval.pb.ProtoRegistry

# Output from the direct dependency JAR
public final class org.some.vendor.expression.eval.pb.ProtoRegistry ... {
  public static org.some.vendor.expression.eval.pb.ProtoRegistry newRegistry(com.google.protobuf.Message...);
}
```

This confirmed the conflict. The `auth-lib` was compiled against the second version but, at runtime, the JVM loaded the first one.

-----

## The Root Cause and The Fix 🛠️

The core issue was that the shading rule (`jarjar_rule`) for the `security-lib` was far too aggressive. It was repackaging (shading) dependencies like `com.google.protobuf` that it shouldn't have.

```
# The problematic jarjar_rules.txt
# This rule repacks any class under com.google.**
rule com.google.** securitylib.shaded.@0
...
```

This meant that any service using both our `security-lib` and `auth-lib` would hit this runtime error. The `security-lib` was effectively corrupting the classpath for `auth-lib`.

The solution was to make the shading rules more specific, so they only repackage the libraries they are intended to, without affecting common dependencies like Protobuf. By tightening the scope of the `jarjar_rule` in the `security-lib`'s `BUILD.bazel` file, we ensured it no longer shaded classes that other libraries, like `auth-lib`, depended on directly. After this fix, both libraries could coexist peacefully with their respective dependencies.

```
```
