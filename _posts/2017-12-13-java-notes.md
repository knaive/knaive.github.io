---
layout: post
title: Java Notes
category: Coding
tags:
    - Coding
---


## java notes
- string compare

```
String a = "hello";

if ("hello".equals(a)) {
	System.out.println("equal");
}
```

- *Throwable* is superclass of *Error* and *Exception*

- Every class has a *Object* as a superclass

## StringBuffer vs StringBuilder
- equivalent
- StringBuffer: thread-safe
- StringBuilder: Not thread-safe. No synchronization and faster. Prefered in single-thread code.


## create a maven project

    mvn archetype:generate -DgroupId=com.sonatype.mavenbook -DartifactId=simple -Dpackage=org.sonatype.mavenbook -Dversion=1.0-SNAPSHOT

    mvn archetype:generate -DgroupId="com.sonatype.mavenbook" -DartifactId="simple" -Dpackage="org.sonatype.mavenbook" -Dversion="1.0-SNAPSHOT"

    mvn archetype:generate -DgroupId="org.sonatype.mavenbook.custom" -DartifactId="simple-weather" -Dversion="1.0"

    mvn archetype:generate -DgroupId="org.fuguang" -DartifactId="aopdemo" -Dpackage="org.fuguang" -Dversion="1.0-SNAPSHOT"

    mvn exec:java "-Dexec.mainClass=org.fuguang.App"
