---
layout: post
title: '[Spring] AOP'
date: 2021-10-19 18:00:00 +0900
category: spring
---

# 4. AOP

> 스프링의 주요 기능 중 하나인 AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)



### 4-1. AOP

프로그램을 핵심 관심 사항과 공통 관심 사항으로 나눈다
- 핵심 관심 사항 (Core concern)
- 공통 관심 사항 (Cross-cutting concern)
- 중복된 코드를 줄이고 공통 모듈을 관리하기 쉽게 함



### 4-2. 적용

- 간단한 메소드의 성능 검사
  - System.currentTimeMillis()나 StopWatch 코드를 각각의 메소드에 적용하는 번거로운 일을 외부에서 AOP를 이용해 처리
- 트랜잭션 처리
  - 매번 사용되는 try~catch~finally 부분 코드는 반복되고 번거로워 소스를 복잡하게 하는데, AOP를 이용해 외부에서 처리
- 예외 반환
  - throw 되는 예외를 AOP를 이용해 가로채서 원하는 곳으로 보내거나 처리할 수 있다
- 그 외, 멀티쓰레드에서 메소드에 일괄적으로 락을 걸거나, 데드락 등으로 인한 예외를 만났을때 처리하거나, 로깅, 인증, 권한과 같은 것들을 AOP를 통해 처리할 수 있다



### 4-3. 용어

![image-20211022003832895](/assets/img/image-20211022003832895.png)

##### Aspect

- 분리된 공통 관심 사항(부가 기능)을 칭한다.  

##### Advice

- 타겟의 어느 시점에 Aspect를 적용할 지 정의
- Before, After, After-returning, After-throwing, Around

##### Pointcut

- Aspect가 적용될 타겟을 선별하는 표현식
- `execution(<expression>)` 형식으로 표현. 아래는 표현식의 예시
  - `execution(public * *(...))` : public 메서드 실행
  - `execution(* set *(..))` : 이름이 set으로 시작하는 모든 메서드 실행
  - `execution(* com.test.service.AccountService.*(..))` : AccountService 인터페이스의 모든 메서드
  - `execution(* com.test.service.*.*(..))` : service 패키지의 모든 메서드 실행
  - `execution(* com.test.service..*.*(..))` : service 패키지와 하위 패키지의 모든 메서드 실행

##### Weaving

- Pointcut에 의해 결정된 타겟의 JoinPoint에 부가 기능을 삽입하는 과정

##### JoinPoint

- Aspect가 적용될 수 있는 지점 (메서드)



### 4-4. Spring AOP의 특징

- 스프링은 Proxy 기반 AOP를 지원
  - 타겟 객체에 대한 Proxy를 만들어 제공
  - Proxy는 Advice를 Target 객체에 적용하며 생성되는 객체
- Proxy가 호출을 가로챈다
  - Target에 대한 호출을 가로채 Advice의 부가기능 로직을 수행하고 Target의 핵심 기능을 호출한다.
  - Target의 핵심 기능을 호출한 후에 부가 기능을 수행하는 경우도 있다.
- 스프링 AOP는 method JoinPoint만 지원한다
  - 즉, Target의 메서드가 호출되는 런타임 시점에만 부가기능을 적용할 수 있다.