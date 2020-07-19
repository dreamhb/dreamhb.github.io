---
layout: default
title:  "Generics: Invariant vs Covariant vs Contravariant"
date:   2019-12-05 09:50:00
comments: true
---

Java generics is implemented by **erasure**. So at runtime, List<String> and List<Integer> are the same.
## Invariant  

Generics are invariant and erasure.

## Covariant  

Arrays are covariant and reified, covariant means if type A is subtype of type B, A[] is the subtype of B[]

## Contravariant


