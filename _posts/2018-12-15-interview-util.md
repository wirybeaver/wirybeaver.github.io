---
layout:     post
title:      Practical Programming Utils in Interviews
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Interview
    - Java
---

[HttpClient Basic](http://www.vogella.com/tutorials/ApacheHttpClient/article.html)
[HttpClient Senior](https://blog.csdn.net/frankcheng5143/article/details/50070591)

```java
import org.json.simple.parser.JSONParser;
import org.json.simple.parser.ParseException;
import org.json.simple.*;

import java.io.*;
import java.util.*;
import java.util.regex.*;
import java.net.URL;
import java.nio.file.Paths;

public class Solution {

    public static void main(String[] args){

        // io
        String url = "https://shanelxy.top/assets/json.txt";
        String dir = Paths.get(".").toAbsolutePath().normalize().toString();
        String infile = Paths.get(dir,"input.txt").toString();
        String outfile = Paths.get(dir, "output.txt").toString();
        List<String> ans = new ArrayList<>();
        try (BufferedReader rd = new BufferedReader(new FileReader(infile))){
            String line;
            while((line = rd.readLine())!=null){
                System.out.println(line);
                ans.add(line);
            }

        } catch (IOException e){
            e.printStackTrace();
        }

        try(BufferedReader rd = new BufferedReader(new InputStreamReader(new URL(url).openStream()))){
            StringBuilder sb = new StringBuilder();
            String line;
            while((line = rd.readLine())!=null){
                sb.append(line);
            }
        }
        catch (Exception e){
            e.printStackTrace();
        }

        try (BufferedWriter wt = new BufferedWriter(new FileWriter(outfile))){
            for(String l : ans){
                wt.write(l);
                wt.newLine();
            }
        } catch (IOException e){
            e.printStackTrace();
        }

        // json , address:{city: columbus, state:ohio}, birthdate:[1995, 1]
        String js = "{ \"name\": \"shane\", \"address\":{\"city\": \"columbus\", \"state\":\"ohio\"}, \"age\":[23, 23.5]}";
        JSONParser parser = new JSONParser();
        try{
            Object obj = parser.parse(js);
            JSONObject person = new JSONObject();
            if(obj instanceof Map && obj instanceof JSONObject){
                person = (JSONObject) obj;
            }
            JSONObject addr = (JSONObject) person.get("address");
            JSONArray birthdate = (JSONArray) person.get("age");
            System.out.println(addr.get("city"));
            if(birthdate.get(0) instanceof Long){
                System.out.println((Long) birthdate.get(0));
            }
            if(birthdate.get(1) instanceof Double){
                System.out.println((Double) birthdate.get(1));
            }
            person.put("university", "ZJU");
            System.out.println(person.toString());
            System.out.println(person.toJSONString());
        }
        catch (ParseException e){
            e.printStackTrace();
        }

        // regular expression
        String sentence = " abcdef34 ghi";
        Pattern p = Pattern.compile("\\s+([a-z]+)(\\d*)");
        Matcher m = p.matcher(sentence);
        int start = 0;
        while(m.find(start)){
            // exclusive
            int end = m.end();

            System.out.println(m.groupCount());
            System.out.println(m.group());
            // the same as m.group(0)
            System.out.println(m.group(0));
            System.out.println(m.group(1));
            System.out.println(m.group(2));
            start = end;
        }
    }

}
```





