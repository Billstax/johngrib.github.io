---
layout  : wiki
title   : 작성중 - (요약) Spring Core Technologies
summary : Version 5.3.7
date    : 2021-06-06 15:56:22 +0900
updated : 2021-06-19 23:13:50 +0900
tag     : java spring
toc     : true
public  : false
parent  : [[spring-documents]]
latex   : false
---
* TOC
{:toc}

## Core Technologies

>
This part of the reference documentation covers all the technologies that are absolutely integral to the Spring Framework.
>
Foremost amongst these is the Spring Framework’s Inversion of Control (IoC) container. A thorough treatment of the Spring Framework’s IoC container is closely followed by comprehensive coverage of Spring’s Aspect-Oriented Programming (AOP) technologies. The Spring Framework has its own AOP framework, which is conceptually easy to understand and which successfully addresses the 80% sweet spot of AOP requirements in Java enterprise programming.
>
Coverage of Spring’s integration with AspectJ (currently the richest — in terms of features — and certainly most mature AOP implementation in the Java enterprise space) is also provided.

이 레퍼런스 문서는 스프링 프레임워크에 절대적으로 필수적인 모든 기술을 다룹니다.

- 가장 중요한 것은 스프링 프레임워크의 IoC 컨테이너입니다.
    - 스프링 프레임워크의 IoC 컨테이너를 다룬 이후에는 스프링의 AOP 기술에 대한 포괄적인 설명이 이어집니다.
- 스프링 프레임워크에는 자체적인 AOP 프레임워크가 포함되어 있습니다.
    - 이 AOP 프레임워크는 개념적으로 이해하기 쉽고 Java 엔터프라이즈 프로그래밍에서 AOP에 대해 필요로 하는 스윗 스팟의 80% 가량을 해결합니다.
- 이 문서에서는 스프링의 AspectJ 통합(기능 측면에서 현재 가장 풍부하고 성숙한 AOP 구현)에 대한 내용도 제공합니다.

## 1. The IoC Container

**1. IoC 컨테이너** [원문]( https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html#beans )

>
This chapter covers Spring’s Inversion of Control (IoC) container.

이 챕터는 스프링의 IoC 컨테이너를 다룹니다.

### 1.1. Introduction to the Spring IoC Container and Beans

- [[spring-documents-core-1-1-introduction]]

### 1.2. Container Overview

- [[spring-documents-core-1-2-container-overview]]

### 1.3. Bean Overview

- [[spring-documents-core-1-3-bean-overview]]

### 1.4. Dependencies

- [[spring-documents-core-1-4-dependencies]]

### 1.5. Bean Scopes

- [[spring-documents-core-1-5-bean-scopes]]

### 1.6. Customizing the Nature of a Bean

- [[spring-documents-core-1-6-customizing-the-nature-of-a-bean]]

## 함께 읽기

- [[inversion-of-control]]

## 참고문헌

- [Core Technologies (docs.spring.io)][5-3-7-core] - 5.3.7 버전

[5-3-7-core]: https://docs.spring.io/spring-framework/docs/5.3.7/reference/html/core.html