https://shinsunyoung.tistory.com/83



https://gmoon92.github.io/spring/aop/2019/05/06/pointcut.html

위치

@Pointcut("execution(public \* com.your.package.controller.rest.\*.\*(..))  && !@target(com.your.package.annotation.NoLogging)")

