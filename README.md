## Spring boot - Scheduling Task 예제
Spring 가이드 Scheduling Task를 공부하면 만든 프로젝트입니다.

### Scheduler
일정한 시간마다 주기적으로 실행되어야 하는 작업이 존재할 수 있다.
예를 들어, 서비스 `A`, `B`, `C`가 DB에 접근하여 값을 변경 시키고, 다른 서비스 `D`가 데이터의 상태를 확인하여 특정한 일을 수행해야 한다고 하자.
그렇다면 `A`, `B`, `C`가 DB에 접근할 때 마다 `D`에게 이 사실을 알려주게 된다면? 원하지 않는 의존성이 생기게 된다. 서비스 `D`의 변화는 `A`, `B`, `C`에게 연쇄적인 변화를 야기할 수 있다는 것이다.

이때 사용하면 좋은 방법이 스케줄러를 사용하는 것이다. `D`를 주기적으로 DB를 확인하여 작업을 처리하게 한다면, 기존에 의존성을 가지던 `A`, `B`, `C`는 작업만 마치고 종료하면 된다. 의존성을 끊어낼 수 있는 것이다!

- 참고자료
    - [예제 프로젝트](https://spring.io/guides/gs/scheduling-tasks/)
    
***

### <h1>프로젝트 구조
실행을 위해 `Main`을 가지는 `SchedulingTasksApplication`과 `ScheduledTasks`로 구성된다.

***

### ScheduledTask

특정한 주기를 가지고 실행되는 메서드를 지정해 주기 위해서 `@Scheduled`어노테이션을 사용한다.
```java
@Component
public class ScheduledTasks {

	private static final Logger log = LoggerFactory.getLogger(ScheduledTasks.class);

	private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");

	@Scheduled(fixedRate = 5000)
	public void reportCurrentTime() {
		log.info("The time is now {}", dateFormat.format(new Date()));
	}
}
```
`@Scheduled` 어노테이션에는 `fixedRate`뿐 만이 아닌 `initialDelay`, `fixedDelay`와 같은 옵션을 주어 다양한 스케줄러를 생성 할 수있다.
심지어 리눅스에서 사용하는 `cron`속성을 값으로 지정 할수도 있다.

***

### SchedulingTasksApplication
`@EnableScheduling`어노테이션은 스프링 부트에게 background task 실행자를 생성하도록 한다. 이를 선언하지 않을 시 그 무엇도 스케줄링 되지 않는다.

```java
@SpringBootApplication
@EnableScheduling
public class SchedulingTasksApplication {

	public static void main(String[] args) {
		SpringApplication.run(SchedulingTasksApplication.class);
	}
}
```

***

### Test
프로그램을 실행하면 `fixedRate`로 지정한 값 `5000`ms 마다 `reportCurrentTime`함수가 실행되는 것을 확인할 수 있다.

```
2020-02-10 01:42:14.068  INFO 26012 --- [   scheduling-1] c.e.schedulingtasks.ScheduledTasks       : The time is now 01:42:14
2020-02-10 01:42:19.048  INFO 26012 --- [   scheduling-1] c.e.schedulingtasks.ScheduledTasks       : The time is now 01:42:19
2020-02-10 01:42:24.048  INFO 26012 --- [   scheduling-1] c.e.schedulingtasks.ScheduledTasks       : The time is now 01:42:24
2020-02-10 01:42:29.048  INFO 26012 --- [   scheduling-1] c.e.schedulingtasks.ScheduledTasks       : The time is now 01:42:29
```