---
layout: post
title: '[Spring] Spring Framework'
date: 2021-10-18 18:00:00 +0900
category: spring
---

# 1. Spring Framework

> Spring 프레임워크의 등장배경, 구조, 특징



### 1-1. 스프링이란?

- Enterprise Application을 만들기 위한 모든 기능을 종합적으로 제공하는 경량화 된 솔루션
- 개발자가 실수하기 쉬운 복잡하고 반복적인 것들에 신경쓰지 않고, Business Logic 개발에 전념할 수 있도록 해주는 Framework이다.



### 1-2. 스프링의 등장 배경

- 기존에 사용하던 EJB(Enterprise Java Beans) 모델은 복잡한 스펙으로 개발의 효율성이 떨어졌다
  - 스펙에 정의된 인터페이스에 따라 코드를 작성해야 함
  - 반드시 EJB 서버(컨테이너)에 배포를 해야 테스트가 가능해 개발 속도가 저하됨
  - 배우기 어렵고 설정해야 할 부분이 많았다
- POJO + Framework를 사용하기 시작
  - POJO(Plain Old Java Object) 
    - 특정 환경이나 기술에 종속적이지 않은 객체지향 원리에 충실한 자바 객체
  - 오픈소스 프레임워크라 사용이 무료
  - 각종 기업용 어플리케이션 개발에 필요한 상당히 많은 라이브러리가 존재



### 1-3. 스프링의 구조

![asdf](/assets/img/structure.png)

1. POJO (Plain Old Java Object)
2. PSA (Portable Service Abstraction)
   - 환경과 세부 기술의 변경과 관계없이 일관된 방식으로 기술에 접근할 수 있게 해주는 설계 원칙
3. IoC / DI
4. AOP 



### 1-4. 스프링의 특징

- 경량 컨테이너
  - 스프링은 자바 객체를 담고 있는 컨테이너이다
  - 스프링 컨테이너는 자바 객체들의 생성과 소멸과 같은 life cycle을 관리한다
  - 언제든지 스프링 컨테이너로부터 필요한 객체를 가져와 사용할 수 있다
- DI(Dependency Injection) 
  - 설정 파일(.xml)이나 Annotation(@)을 통해 객체간의 의존 관계를 설정할 수 있다
  - 객체는 의존하고 있는 객체를 직접 생성하거나 검색할 필요가 없다
- AOP(Aspect Oriented Programming)
  - 문제를 바라보는 관점을 기준으로 하는 프로그래밍 기법
  - 문제를 해결하기 위한 <u>핵심 관심사항</u>과 전체에 적용되는 <u>공통 관심사항</u>을 기준으로 프로그래밍하여 공통 모듈을 여러 코드에 쉽게 적용할 수 있도록 한다
  - 스프링은 자체적으로 프록시 기반의 AOP를 지원
  - 로깅, 보안과 같이 여러 모듈에서 공통으로 필요하지만 실제 모듈의 핵심이 아닌 기능들을 분리하여 각 모듈에 적용할 수 있다
- POJO(Plain Old Java Object)
  - 스프링 컨테이너에 저장되는 자바 객체는 특정 인터페이스를 구현하거나 클래스 상속 없이도 사용 가능 
- IoC(Inversion of Control)
  - 자바의 객체 생성 및 의존관계에 있어 개발자에게 있던 제어권의 일부가 Container로 넘어감
  - Container는 객체의 생명 주기를 관리



### Spring Framework Module

![img](/assets/img/spring.png)

