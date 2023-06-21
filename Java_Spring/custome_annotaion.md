# [Spring] Custom Annotation으로 AOP Poincut에서 제외시키기



## 개요

현재는 Spring AOP를 사용해서, `C1Service` 라는 클래스에서 select로 시작되는 메소드가 null 혹은 빈 List를 반환할 경우, 나중에 재처리할 대상으로 분류하고 있다. 

하지만 특정 몇몇 메소드에 대해서는 null 혹은 빈 List를 반환하더라도 재처리 대상으로 분류를 안 하게끔 로직 수정이 필요해졌다.



재처리 대상 메소드와 재처리 미대상 메소드를 Pointcut상에서 이름으로 분류하기 어려워, Custon Annotation을 생성하고 해당 Annotation이 달려있으면 재처리 미대상으로 분류하게끔 수정하고자 한다.



### [수정 전 소스]

```java
@Pointcut("execution(* com.sample.package.C1Service.select*(..)))")
private void reprocessing(){
}

@AfterReturning(pointcut = "reprocessing()", returning = "returnValue")
private void afterReturningService(JoinPoint joinPoint, Object returnValue) throws Exception {
    
    if(ObjectUtils.isEmpty(returnValue)){
        // 재처리 분류 로직 수행
    }
}
```





## Custon Annotation 생성



Annotation이란 프로그램 부수적인 정보를 제공하는 일종의 메타 데이터이다. 

@Bean, @Controller와 같이 Spring에서 기본적으로 제공하는 annotation외에도 생성이 가능하다.



## 생성

```java
@Target({ElementType.METHOD})
@Retension(RententionPolicy.RUNTIME)
public @interface ReProcessExclusion {
    
}
```

### @interface

어노테이션으로 생성함을 명시합니다.



### @Target

어노테이션이 생성될 수 있는 위치를 지정합니다.

`ElementType` 의 값은 다음과 같다.

| ElementType 종류 | description                            |
| ---------------- | -------------------------------------- |
| PACKAGE          | 패키지 선언 시                         |
| TYPE             | 타입(클래스, 인터페이스, Enum) 선언 시 |
| CONSTRUCTOR      | 생성자 선언 시                         |
| FIELD            | 멤버 변수 및 Enum 상수 선언 시         |
| METHOD           | 메소드 선언 시                         |
| ANNOTATION_TYPE  | 어노테이션 타입 선언 시                |
| LOCAL_VARIABLE   | 지역변수 선언 시                       |
| PARAMETER        | 파라미터 선언 시                       |
| TYPE_PARAMETER   | 파라미터 타입 선언 시                  |

만약 ElementType이 여러개가 지정되야 할 경우에는 `,` 으로 구분지어서 여러 개를 지정할 수 있다.









위치

``` java
@Pointcut("execution(* com.sample.package.C1Service.select*(..))) && !@annotation(com.your.package.annotation.NoLogging)")
private void reprocessing(){
    
}
```



```java
@Pointcut("execution(* com.sample.package.C1Service.select*(..))")
private void reprocessingTarget(){
    
}


@Pointcut("!@annotation(com.sample.package.common.ReProcessExclustion)")
private void reprocessingExclusion(){
    
}

@Pointcut("reProcessingTarget() && reProcessingExclusion()")
private void reprocessing{
    
}


```







https://shinsunyoung.tistory.com/83

https://www.javatpoint.com/spring-boot-annotations

https://gmoon92.github.io/spring/aop/2019/05/06/pointcut.html