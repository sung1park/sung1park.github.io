---
layout: post
title: '[Spring] Spring Web MVC'
date: 2021-10-20 18:00:00 +0900
category: spring
---

# 5. Spring Web MVC

> 스프링이 지원하는 웹 MVC 프레임워크
>
> MVC 패턴, 스프링 웹 MVC, Controller, View, Model 설정 및 사용 방법



### 5-1. MVC 패턴 (Model 2)

![mvc](/assets/img/mvc.png)

**MVC 패턴 이용의 장점**

- 화면과 비즈니스 로직을 분리해서 작업할 수 있다.
- 컴포넌트 간의 결합성이 낮아 프로그램 수정하기가 용이하다.(확장성)
- 컴포넌트의 변경이 다른 영역 컴포넌트에 영향을 미치지 않는다.(유지보수)

**MVC 패턴 이용의 단점**

- 개발 과정이 복잡해 초기 개발속도가 늦다.
- 모든 웹사이트에 MVC 패턴을 적용하지는 않는다. 경우에 따라 Model1패턴을 사용하기도 한다.

**어플리케이션을 Model, View, Controller 영역으로 분리**

- Model 
  - 어플리케이션이 '무엇'을 할 것인지 정의
  - 내부 비즈니스 로직을 처리
- View
  - 화면에 '무엇'을 보여주기 위한 역할
  - 사용자의 입력을 컨트롤러에게 전달
- Controller
  - 모델이 '어떻게' 처리할 지를 알려주는 역할
  - 클라이언트의 요청을 실제로 처리



### 5-2. 스프링 MVC

![mvc](/assets/img/mvc-16357549811621.png)

**DispatcherServlet**

- 중앙에 위치해 모든 연결을 담당
- Front Controller



##### 실행 순서

1. 클라이언트로부터 DispatcherServlet에 요청이 들어온다.
2. DispatcherServlet은 요청을 처리하기 위한 컨트롤러 객체를 HandlerMapping을 통해 검색한다.
3. DispatcherServlet은 요청을 할당된 컨트롤러에 전송한다.
4. 컨트롤러는 비즈니스 로직을 처리하고 그 결과를 DispatcherServlet에 반환한다.
5. DispatcherServlet은 ViewResolver를 통해 결과를 보여줄 뷰를 찾는다.
6. DispatcherServlet은 할당된 뷰 객체에게 결과 생성을 요청한다. JSP를 사용하는 경우 뷰 객체는 JSP를 실행하여 클라이언트에 전달할 화면을 표현한다.



### 5-3. DispatcherServlet 설정 (web.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee https://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

	<!-- The definition of the Root Spring Container shared by all Servlets and Filters -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/spring/root-context.xml</param-value>
	</context-param>
	
	<!-- Creates the Spring Container shared by all Servlets and Filters -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	
	<!-- POST 방식의 한글 처리. -->
	 <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
          <param-name>encoding</param-name>
          <param-value>UTF-8</param-value>
        </init-param>
     </filter>
     
     <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
     </filter-mapping>

	<!-- Processes application requests -->
	<servlet>
		<servlet-name>appServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/spring/appServlet/servlet-context.xml</param-value>
		</init-param>
		<!-- DispatcherServlet이 해당 mapping을 찾지 못할 경우 NoHandlerFoundException를 throw하게 설정 -->
		<init-param>
			<param-name>throwExceptionIfNoHandlerFound</param-name>
			<param-value>true</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
		
	<servlet-mapping>
		<servlet-name>appServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

</web-app>
```

web.xml에서 DispatcherServlet에 대한 설정을 한다

- `<context-param>` : root-context.xml을 읽어들이는 역할
- `<listener>` : 클라이언트의 요청을 감지하는 역할
- `<servlet>`에서 listener가 요청을 듣고 어디로 보낼지 정해주는 역할
  - 기본적으로 servlet-context.xml 지정
  - ExceptionControllerAdvice를 통해 page not found 에러 발생 시 지정해둔 페이지로 이동시키기 위해서 DispatcherServlet이 해당 매핑을 찾지 못할 경우 NoHandlerFoundException을 throw 하도록 설정
- `<filter>` : POST 방식의 한글 처리를 위해 필터를 설정



### 5-4. Spring MVC - Controller

##### Controller 클래스 

```java
package com.sungil.guestbook.controller;

import ...

@Controller
@RequestMapping("/user")
public class UserController {

    private Logger logger = LoggerFactory.getLogger(UserController.class);

    @Autowired
    private UserService userService;

    @GetMapping("/register")
    public String register() {
        return "user/join";
    }

    @PostMapping("/register")
    public String register(UserDto userDto, Model model) throws Exception {
        logger.info("memberDto info : {}", userDto);
        userService.registerMember(userDto);
        return "redirect:/user/login";
    }
    ...
}
```

- `@Controller` : servlet-context.xml에서 해당 클래스가 컨트롤러임을 알게 하기 위해 선언
- `@RequestMapping` : "/user"로 들어오는 모든 요청에 대해 해당 클래스로 매핑 시키기 위해 선언
  - 각각의 메서드에도 `@RequestMapping`, `@GetMapping`, `@PostMapping`으로 설정 가능
  - `@RequestMapping(value = "/register", method = RequestMethod.GET)`



##### HTML form과 DTO

```java
@PostMapping("/register")
public String register(UserDto userDto, Model model) throws Exception {
    userService.registerMember(userDto);
    return "redirect:/user/login";
}
```

- 스프링 MVC는 html form에 입력된 데이터를 DTO와 같은 빈 객체의 property와 자동으로 매핑할 수 있다



##### View에서 DTO에 접근

```java
@PostMapping("/register")
public String register(UserDto userDto, Model model) throws Exception {
    userService.registerMember(userDto);
    return "index";
}
```

- DTO 객체는 자동 반환되는 Model에 추가되어 jsp에서 `${userDto.userid}`와 같이 접근할 수 있다



##### Controller 메서드 리턴 타입

- ModelAndView : model 정보와 view 정보를 모두 담고있는 객체
- Model : view에 전달할 정보를 담고있는 객체
- Map : view에 전달할 정보를 담고있는 객체
- String : view의 이름을 반환
- View : view 객체를 직접 리턴
- @ResponseBody : 메서드에 해당 어노테이션을 적용하면, 리턴 객체를 http 응답으로 그대로 전송



##### Bean 등록

- servlet-context.xml에서 직접 빈으로 등록할 수 있다

  ```xml
  <beans:bean id="userController"class="com.sungil.guestbook.controller.UserController">
  	<beans:property name="userService" ref="userService" />
  </beans:bean>
  ```

- servlet-context.xml에서 자동 스캔하도록 component-scan 경로를 설정할 수 있다

  ```xml
  <context:component-scan base-package="com.sungil.guestbook.controller" />
  ```

  - servlet-context.xml은 웹과 관련한 요소들의 설정을 담는다. 따라서 일반적으로 컨트롤러의 경우 servlet-context.xml에서 스캔하도록 설정한다.



### 5-5. Spring MVC - View

##### View Resolver (servlet-context.xml)

```xml
<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <beans:property name="prefix" value="/WEB-INF/views/" />
    <beans:property name="suffix" value=".jsp" />
</beans:bean>
```

- 논리적 view와 실제 jsp 파일을 매핑해주는 view resolver 부분이다



##### View 이름 명시적 지정

```java
@GetMapping("/register")
public String register() {
    return "user/join";
}
```

- 위와 같이 String을 이용하거나 ModelAndView, View를 이용하여 view의 이름을 명시적으로 지정해 준다



##### redirect view

```java
@PostMapping("/register")
public String register(UserDto userDto, Model model) throws Exception {
    userService.registerMember(userDto);
    return "redirect:/user/login";
}
```

- view 이름에 'redirect:' 접두어를 붙이면 지정한 페이지로 redirect 된다



### 5-6. Spring MVC - Model

##### View에 전달하는 데이터

- `@RequestMapping`이 적용된 메서드의 Map, Model, ModelMap
- `@RequestMapping`이 적용된 메서드가 리턴하는 ModelAndView
- `@ModelAttribute`이 적용된 메서드가 리턴하는 객체



##### Map, Model

```java
// Map
@GetMapping("/hello")
public String hello(Map model) {
    model.put("message", "안녕하세요");
    return "hello";
}
```

- Map을 view에서 받아오는 요청을 매핑할 객체로 사용하려면 앞에 `@RequestParam`을 붙여주어야 한다
- Map은 view의 정보를 받는 역할, view로 정보를 보내주는 역할 두 가지를 모두 할 수 있다

```java
// Model
@GetMapping("/list")
public String bookList(SearchCondition condition, Model model) throws Exception {
    List<Book> list = bookService.search(condition);
    model.addAttribute("booklist", list);
    return "book/booklist";
}
```

- `addAttribute()`, `addAllAttributes()`를 이용해 Model에 요소를 추가할 수 있다



##### ModelAndView

```java
// ModelAndView
@GetMapping("/modify")
public ModelAndView modify(@RequestParam("articleno") int articleNo) throws Exception {
    ModelAndView mav = new ModelAndView();
    GuestBookDto guestBookDto = guestBookService.getArticle(articleNo);
    mav.addObject("article", guestBookDto);
    mav.setViewName("guestbook/modify");
    return mav;
}
```

- `addObject()`를 통해 객체를 추가하고, `setViewName()`을 통해 view의 이름을 지정할 수 있다



##### 요청 URL 매칭

```java
@RequestMapping("/user/{userId}")
public String showUserInfo(@PathVariable String userId, Model model) {
    UserDto userDto = userService.getUserInfo(userId);
    model.addAttribute("userinfo", userDto);
    return "user/viewinfo";
}
```

- `{템플릿변수}`, `@PathVariable`을 이용해 템플릿 변수와 동일한 이름을 갖는 parameter 매칭 가능

