---
layout: post
title: Coroutines, Generators and Continuations
---

## Coroutines
### a routine stores its state, and pass control to another routine, but still can return to the state

## Generators
### generates a value one time, and move to next
### use case: streams

## Continuations
### we can store the execution point into a variable, and then restore it
### call/cc
```
    (define saved-continuation #f)

    (define (test x) 
        (call/cc (k) 
            (set! saved-continuation k)))
```