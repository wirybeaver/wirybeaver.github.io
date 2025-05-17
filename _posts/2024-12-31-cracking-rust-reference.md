---
layout:     post
title:      De-sugaring Rust's Reference
excerpt:    Consolidate the obscured tricks of Rust's Reference, including but not limited to, fat-pointer, reborrow, deref coercion, interior mutability and pattern
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Rust
---

Although there are many Rust tutorials available, I have yet to come across any material that clearly and organically presents the various syntax and underlying mechanisms of Rust's *reference* as a cohesive article. Therefore, I wrote this article to document the pitfalls I’ve encountered and the insights I’ve gained. The source code can find in my [gist](https://gist.github.com/wirybeaver/6c87948e4131e25cab1fd8e26addae31).

## Reference-Related Operator
Rust has three types of pointers: (1) raw pointer (unsafe), (2) smart pointer (with ownership), (3) reference (also called non-owning pointer or borrow pointer). Creating a reference in Rust is called borrowing a reference. In Rust, references are explicitly created with the `&` operator and explicitly dereferenced with the `*` operator. The only exception is the `.` operator, which implicitly dereferences its left operand or borrows a reference to its left operand. Auto-referencing only occurs with the dot operator. Auto-dereferencing is applied by Rust as many times as needed when passing an argument into a function to match the parameter’s type.

```rust
struct MyBox<T>(T);

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}
fn main() {
    // Topic: Reference Operators
    // Dot operator is the only exception that doesn't need to explicitly use & and * for borrowing and dereferencing.
    let bx  = &MyBox::new(5);
    assert_eq!(5, bx.0);
    // Equivalent to the above, but with the dereference written out
    assert_eq!(5, (*bx).0);
    let v  = vec![1, 2, 3];
    v.len(); // implicitly borrows a reference to v
    (&v).len(); // equivalent, but more verbose
}
```

## Memory Model
If the referent is of fixed size, its reference is an ordinary pointer (a single word). Otherwise, the reference is a *fat pointer* (a double word), and the pointed-to object’s type T must satisfy `T:? Sized`. For example, references to slices and trait objects are fat pointers.

Since a slice ([T]) has no boundaries, it cannot be used on its own and must be paired with a reference &. For convenience, a reference to a slice (&[T]) is simply referred to as a slice. Similarly, str can be considered a special type of slice, i.e., a slice of Unicode representing boundary-less UTF-8 text. For simplicity, a reference to a string slice (&str) is referred to as a string slice. A str can either be allocated on the heap (as the "text" behind a String) or exist as a string literal (pre-allocated alongside the code in read-only memory).

As shown below, ss1 is an ordinary pointer, while ss2 is a fat pointer:

```rust
    let ss = String::from("hello");
    // ss1 type: &String
    let ss1 =&ss;
    // deref coercion: conceptually equivalent to ss.deref(); 
    // the call ss.deref() is effectively treated as (&ss).deref() where ss is implicitly borrowed
    let ss2: &str = &ss;
```

ss1 points to a String, which is a struct allocated on the stack containing: a raw pointer to the heap-allocated text, capacity, and length.
ss2 is a fat pointer allocated on the stack containing: a raw pointer to the heap-allocated text and its length.

<img src="https://i.imgur.com/8TR2y8X.png" width="60%" border="5">

## Deref Coercion
In the code snippet of Memory model, *Deref Coercion* implicitly occurs in the initialization statement of ss2. Deref Coercion converts a reference to a type that implements the Deref trait into a reference to another type. Even though the type of &ss is &String, since the String type implements the Deref trait `(&String → &str)` and ss2 is explicitly declared as `&str`, the compiler automatically invokes the deref call. 

If we don’t want to explicitly declare the type as `&str` or manually call `deref()`, is there another way to convert a `String` into a `&str`? Yes, we can leverage a key property: Deref is an overload of the `*` operator. When an instance `y` of type `T` implements the Deref trait `(&T → &Target)`, `*y` is equivalent to `*(y.deref())`. The type of `*y` is `*(&Target)`, which is effectively `Target`.

```rust
    // conceptually equivalent to &*(ss.deref()); ss3 type: &str
    let ss3 = &*ss;
    // another way to get the string slice from the String. ss4 type: &str
    let ss4 = &ss[..];
```

If we implement the `Deref` trait for `MyBox`, then `assert_eq!(5, bx.0)` can be written without exposing the internal `.0` structure.

```rust
impl<T> Deref for MyBox<T> {
    type Target = T;
    fn deref(&self) -> &Self::Target {
        &self.0
    }
}

fn main() {
    ...
    // bx: &MyBox<i32>; *bx: MyBox<i32>; **bx: *MyBox<i32> = *(&Target) = i32
    assert_eq!(5, **bx);
}
```

## Mutability

| **Reference Type**            | **Referent Type**      | **Validity** | **Explanation**                                                                                                                                       |
|-------------------------------|------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| Shared reference           | Mutable referent       | Valid        | The value of the referent cannot be modified until the reference's lifetime expires.                                                                  |
| Mutable reference             | Immutable referent     | Invalid      |                                                                                                                                                       |
| Shared reference           | Immutable referent     | Valid        | The referent can be read by multiple shared references.                                                                                               |
| Mutable reference             | Mutable referent       | Valid        | The referent is exclusively accessed: (1) No shared references exist during the mutable reference's lifetime; (2) Can be "stolen" by other mutable references following the re-borrowing rule, the creation and drop of those mutable ref is sort of LIFO |

The shared/mutable reference and the mutability of reference itself are totally orthogonal. 
```rust
    // let mut b = 1; let x = &b; x is an immutable reference; x itself is immutable, i.e. x cannot refer to other objects.
    // let mut b = 1; let mut x = &b; x is an immutable reference; x itself is mutable, i.e. x can refer to other objects.
    // let x = &mut b; x is a mutable reference; x itself is immutable.
    // let mut x = &mut b; x is a mutable reference; x itself is mutable.e.g.
    let mut b = 1_000_i32;
    let mut c = 3_000_i32;
    let mut x = &mut b;
    *x = 2_000;
    x = &mut c;
    *x = 4_000;
    // 2000, 4000
    println!("{}, {}", b, c);
```

## Reborrowing
As the mutable reference doesn’t implement the Copy Trait, does that indicate the ownership is always moved out when passing the mutable reference variable into the function? Actually Rust would try to insert reborrowing in any location where the `&mut T` is moved to a variable whose type is known as `&mut _` to avoid the ownership transferring. [link](https://users.rust-lang.org/t/questions-about-mut-t-and-move-semantics-mut-t-is-move-only/37484/15)

```rust
    // Topic: Reborrow
    let mut w = 32;
    // x, y, z type is the same: &mut i32
    let x = &mut w;
    // Reborrowing: equivalent to let y = &mut *x; let z = &mut *x;
    // On the surface, the safety is suspectible because both x, y and z hold exclusive access to w.
    // It's safe because you can't use the original borrow until the reborrow is over.
    // Explicitly specify the type otherwise let y=x will move x to y as mutable reference is not a Copy.
    let y: &mut _ = x;
    let z: &mut i32 = y;
    // The code below won't compile: cannot borrow `*y` as mutable more than once at a time.
    // println!("{}", (&mut *y));
    // The code below won't compile, demonstrating that z's reborrowing is safe.
    // *y = 2; println!("{}", y);
    *z = 2;
    // z is over. Thus we can either use y e.g., *y=4 or reborrow again;
    println!("{}", (&mut *y));
    // y is over. Thus we can use the original borrow x again;
    *x = 4;
    // c type: FnMut
    // the closure c's x is a reborrow of outer x; 
    // The `mut` is required on `c` because a `&mut` is stored inside.
    let mut c = || {
        *x = 8;
    };
    c();
    // d type: FnOnce
    // the closure d move the mutable reference x
    let d = || {
        let u = x;
    };
    d();
    // The code below won't compile because both x and d are moved.
    // *x = 2;
    // d();
```

## Interior Mutability
RefCell allows you to mutate data even when there are immutable references to that data. The RefCell's rule check happens at runtime meanwhile the normal mutable reference occurs at compile time. The program cannot foresee what happens in the future at the runtime. Thus, the ownership checker of RefCell needs to be more rigid. In other words, borrow() and borrow_mut() cannot appear in the same block unless the output drops immediately afte a statement.
```rust
    // Topic: Smart Pointer
    // sort of C++ unique_ptr
    let mut r = Box::new(String::from("hello"));
    r.push_str("world");
    println!("{}", r);

    // sort of C++ shared_ptr
    // Rc is immutable: let r = Rc:new("hello".to_string()); r.push_str() is invalid
    // Interior Mutability: RefCell is immutable too but allows to change the internal data
    let r = Rc::new(RefCell::new(String::from("hello")));
    let r2 = Rc::clone(&r);
    {
        // rf type: RefMut<'_, String>; need to add the modifier mut for rf to invoke write methods, which is a little bit different than the normal mutable reference.
        let mut rf = r.borrow_mut();
        rf.push_str(" world");
        // rf2 type: &mut String; reborrowing
        let rf2 = &mut *rf;
        rf2.push_str(" world2");
        println!("{}", rf)
    }
    // let rf3 = r.borrow_mut(); rf3.push_str(" world3"); Will panic at runtime as the rf3 is still alive. However, the separated statements of creation and modification work for the normal mutable reference in terms of the reborrowing rule. 
    // Such nuance is caused by the inherent difference of the rule check mechanism between RefCell and the normal mutable reference. 
    // That's the reason why we add curly brackets around the previous block for rf and rf2 manipulation.
    // If we borrow_mut and push_str in the same statement, it's safe because the temporal output of borrow_mut is over after the statement.
    r.borrow_mut().push_str(" world3");
    println!("{}", r.borrow());
    println!("{}", r2.borrow());
```

## Pattern
`ref` and `ref mut` patterns borrow parts of a matched value. `&` and `&mut` patterns match references. When matching a reference, we can't move a value out of a reference, even a mut reference. Note: in the expression `&` is used to create a reference, but in the pattern `& `is used to match a reference.

Iterator Background Knowledge: the for loop applies `into_iterator` to its operand. The iterator type depends on the type operand modifer (`&`, `&mut` or pass by value). 

| **Syntax**                  | **x Type**                        | **The underlying invoke**         | **Equivalance**                                            |
|-----------------------------|-----------------------------------|-----------------------------------|------------------------------------------------------------|
| `for x in &collection`      | Shared reference to the element   | `(&collection).into_iterator()`   | `collection.iter()` produce the same type of iterator.     |
| `for x in &mut collection`  | Mutable reference to the element  | `(&mut collection).into_iterator` | `collection.iter_mut()` produce the same type of iterator. |
| `for x in collection`       | The item is moved to x            | `collection.into_iterator()`      |                                                            |

```rust
    // &x won't work because cannot move out of non-copiable element
    // Expression let &e = &String::from("hello") is invalid with the same reason
    // x type: &String;
    for x in &a {
        println!("Iterator Produce shared reference; {}", x)
    }

    // x type: &&String
    for ref x in a.iter() {
        let s = x;
        // auto deref in succesion.
        println!("borrow a reference to the match value whose type is a shared reference: {}", s)
    }

    let mut vect = vec![1, 2, 3];
    // item type: i32, which is the copy of element.
    // A valid example with same essense: let &c = &5; the type of variable c is i32. Copy Trait
    for &item in &vect {
        println!("Match the type of mutable reference: {}", item);
    }
    // when matching a reference, you can't move a value out of a reference, even a mut reference
    // use a ref pattern to borrow a reference to a part. Actually equivalent to for (_, item) in ...
    // item type: &mut i32
    for &mut ref mut item in &mut vect {
        *item = 2 * (*item);
    }
    // the enumerate() produce the tuple (index, &T|&mut T)
    // item type: &i32
    for (i, item) in vect.iter().enumerate() {
        println!("Change item via mutable refereance; {} {}", i, *item)
    }
```

## Reference
- [Programming Rust](https://www.oreilly.com/library/view/programming-rust-2nd/9781492052586/)
- [The Rust Programming Language](https://doc.rust-lang.org/book/)
