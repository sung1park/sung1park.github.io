---
layout: post
title: '[Spring] DI'
date: 2021-10-19 18:00:00 +0900
category: spring
---

# 3. DI (Dependency Injection)

> 의존하는 객체를 직접 생성하는 대신 의존 객체를 전달받는 방식



### 3-1. 의존이란?

```java
public class LoginServiceImpl implements LoginService {
    
	private LoginDao loginDao = new LoginDaoImpl() ;

	@Override
	public MemberDto login(String userid, String userpwd) throws Exception {
		if(userid == null || userpwd == null)
			return null;
		return loginDao.login(userid, userpwd);
	}

}
```

위의 코드는 사용자의 아이디와 패스워드를 받아 로그인을 시도하는 Service 코드이다. `LoginServiceImpl` 클래스는 `LoginDao` 객체의 `login` 메서드를 필요로 하는데, 이처럼 한 클래스가 다른 클래스의 메서드를 실행할 때 이를 <u>의존</u>한다고 표현한다.



### 3-2. Dependency Injection

```java
public class LoginServiceImpl implements LoginService {

	private LoginDao loginDao;
	
	public LoginServiceImpl(LoginDao loginDao) {
		this.loginDao = loginDao;
	}

    ...
}
```

'의존성 주입 (Dependency Injection)'은 의존하는 객체를 직접 생성하는 대신, 의존 객체를 전달받는 방식이다. 위의 코드는 생성자를 통해 `loginDao`를 전달받는다.

DI를 사용한다면 현재의 의존 객체가 아닌 다른 객체를 사용하게 된다고 하더라도 해당 의존 객체를 사용하는 모든 클래스들을 수정하지 않아도 되는 유연함을 갖게 된다.

스프링의 DI는 스프링 컨테이너가 DI 조립기를 제공한다. 스프링 설정 파일을 통해 객체 간 의존 관계를 설정하고, 스프링 컨테이너가 제공하는 API를 이용해 객체를 사용한다.



### 3-3. Singleton Bean

스프링의 빈은 기본적으로 싱글톤으로 만들어진다. 따라서, 컨테이너가 제공하는 빈의 인스턴스는 항상 동일하다.

컨테이너가 상황 별로 새로운 인스턴스를 반환하게 하고 싶을 경우 아래와 같이 설정에서 scope를 변경하면 된다.

```java
<bean id="loginService" class="com.test.guestbook.service.LoginServiceImpl" scope="prototype" />
```

|   scope   |                         설명                          |
| :-------: | :---------------------------------------------------: |
| singleton | 스프링 컨테이너 당 하나의 인스턴스 빈만 생성 (기본값) |
| prototype |  컨테이너에 빈을 요청할 때마다 새로운 인스턴스 생성   |
|  request  |         Http Request별로 새로운 인스턴스 생성         |
|  session  |         Http Session별로 새로운 인스턴스 생성         |



### 3-4. 스프링 빈 설정

##### 스프링 빈 설정방법

1. XML 파일
   - 단순하며 사용하기 쉽고, `<bean>` 태그를 통해 세밀한 제어가 가능하다

2. Annotation

   - 어플리케이션 규모가 커지고 빈의 개수가 많아질 경우 XML 파일을 관리하는 것이 번거롭다

   - 빈으로 사용될 클래스에 annotation을 부여하면 자동으로 빈을 등록할 수 있다

   - annotation으로 빈을 설정할 경우 반드시 component-scan을 설정해야 한다

     ```java
     <context:component-scan base-package="<빈을 스캔할 경로>" />
     ```

   - '오브젝트 빈 스캐너'로 '빈 스캐닝'을 통해 자동으로 등록한다

     - 빈 스캐너는 기본적으로 클래스 이름의 <u>첫 글자만 소문자로 바꾼것</u>을 빈의 아이디로 사용



##### 빈 객체 얻기

컨테이너가 제공하는 API `ClassPathXmlApplicationContext`를 통해 객체를 주입받는다.

```java
ApplicationContext context = new ClassPathXmlApplicationContext("com/test/guestbook/controller/applicationContext.xml");
GuestBookService guestBookService = 
    context.getBean("guestBookService", GuestBookService.class);
```







### 3-5. 빈 의존관계 설정 - XML

##### 생성자 이용

생성자를 이용할 경우 xml 파일에서 아래와 같이 bean을 설정한다. 생성자의 인자가 여러개일 경우 인자를 순서대로 연결하거나 타입을 이용해 연결해준다.

```xml
<bean id="<설정할 빈 이름>" class="<빈으로 설정할 클래스>">
	<constructor-arg ref="<인자로 전달해줄 빈 이름>" />
</bean>
```

아래와 같이 생성자를 통해 의존 객체를 주입받는다.

```java
public LoginServiceImpl implements LoginService {
    private LoginDao loginDao;
    
    public LoginServiceImpl(LoginDao loginDao) {
        this.loginDao = loginDao;
    }
}
```



##### Property 이용

Setter 메서드를 이용해 객체 또는 값을 주입 받는다. Setter를 통해서는 하나의 값만 받을 수 있음에 주의한다.

```xml
<bean id="gbService" class="com.test.service.GuestBookServiceImpl">
    <!-- 하위 태그를 이용 -->
    <property name="guestBookDao">
		<ref bean="gbDao"/>
	</property>
    <!-- 속성 이용 -->
	<property name="guestBookDao" ref="gbDao"/>
</bean>
```

아래와 같이 Setter 메서드를 통해 의존 객체를 주입받는다.

```java
public GuestBookServiceImpl implements GuestBookService {
    private GuestBookDao guestBookDao;
    
    public setGuestBookDao(GuestBookDao guestBookDao) {
        this.guestBookDao = guestBookDao;
    }
}
```



### 3-6. 빈 의존관계 설정 - Annotation

**@Autowired** : 스프링에 의존하는 다른 빈을 자동으로 주입

```java
@Autowired
private GuestBookDao guestBookDao;
```

- 변수 타입에 맞춰서 연결한다
- 동일한 타입의 빈이 여러 개일 경우에는 `@Qualifer("name")`으로 식별한다



**@Configuration** : 해당 annotation이 붙은 클래스를 빈으로 등록

**@Bean** : 객체 생성

```java
@Configuration
@ComponentScan(basePackages = {"com.test.guestbook.model"})
public class ApplicationConfig {
	@Bean
	public DataSource getDataSource() {
		SimpleDriverDataSource dataSource = new SimpleDriverDataSource();
		return dataSource;
	}
}
```

- 위 클래스를 configuration xml 대신 사용할 수도 있다. 이 경우 의존 객체를 주입 받는 클래스에서는 `@Autowired` 또는 `AnnotationConfigApplicationContext` 메서드를 사용한다.

