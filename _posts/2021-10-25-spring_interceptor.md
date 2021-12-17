---
layout: post
title: '[Spring] Interceptor'
date: 2021-10-25 18:00:00 +0900
category: spring
---

# 7. Spring Interceptor

> 스프링 인터셉터 생성 및 설정



### 7-1. HandlerInterceptor Interface

> 인터셉터는 HandlerInterceptor 인터페이스를 구현하여 사용할 수 있다

##### 메서드

```java
boolean preHandle((HttpServletRequest request, HttpServletResponse response, Object handler)
```

- false를 반환하면 request를 바로 종료

```java
void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)
```

- controller 수행 후 호출

```java
void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
```

- view를 통해 클라이언트에 응답을 전송한 뒤 실행
- 예외가 발생하여도 실행



##### 호출 순서

- 여러 인터셉터가 있을 때 호출 순서는 아래와 같다

![interceptor](/assets/img/interceptor.png)





### 7-2. servlet-context.xml 설정

```xml
<mvc:interceptors>
	<mvc:interceptor>
    	<mvc:mapping path="/*.html" />
        <bean class="com.sungil.guestbook.interceptor.ConfirmInterceptor" />
    </mvc:interceptor>
</mvc:interceptors>
```



### 7-3. 예시

```java
public class ConfirmInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        MemberDto memberDto = (MemberDto) session.getAttribute("userinfo");
        if (memberDto == null) {
            response.sendRedirect(request.getContextPath() + "/user/login");
            return false;
        }
        return true;
    }
}
```

- 유저가 로그인이 되어있지 않으면 로그인 페이지로 이동시키는 preHandle 인터셉터

```xml
<beans:bean id="confirm" class="com.ssafy.interceptor.ConfirmInterceptor"/>

<interceptors>
		<interceptor>
			<mapping path="/guestbook/register"/>
			<mapping path="/guestbook/modify"/>
			<mapping path="/guestbook/delete"/>
			<mapping path="/guestbook/download"/>
			<beans:ref bean="confirm"/>
		</interceptor>
	</interceptors>
```

- servlet-context.xml에서 인터셉터 빈을 생성하고 인터셉터가 작용할 경로를 설정한다

