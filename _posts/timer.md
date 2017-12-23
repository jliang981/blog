#### java 定时组件

1. cron4j
> cron4j是轻量级的定时组件，可使用linux的crontab表达式来配置线程执行的时间。官方说，可以保证一年内的时间正确性。最小定时精度是一分钟。官网：http://www.sauronsoftware.it/projects/cron4j/manual.php

> 稍微看了下源码，大概是线程每次休眠一分钟，然后查看有没有到可以执行的时间。如果到了就执行，否则，继续休眠。

下面是官方文档上摘录的一些关键概念。

```
You can schedule how many tasks you want.
You can schedule a task when you want, also after the scheduler has been started.
**You can change the scheduling pattern of an already scheduled task**, also while the scheduler is running (reschedule operation).
You can remove a previously scheduled task, also while the scheduler is running (deschedule operation).
You can start and stop a scheduler how many times you want.
You can schedule from a file.
You can schedule from any source you want.
You can supply listeners to the scheduler in order to receive events about the executed task.
You can control any ongoing task.
You can manually launch a task, without using a scheduling pattern.
You can change the scheduler working Time Zone.
You can validate your scheduling patterns before using them with the scheduler.
You can predict when a scheduling pattern will cause a task execution.
```

一个简单的例子：

```
import it.sauronsoftware.cron4j.Scheduler;

public class Quickstart {

	public static void main(String[] args) {
		Scheduler s = new Scheduler();
		// Schedule a once-a-minute task.
		s.schedule("* * * * *", new Runnable() {
			public void run() {
				System.out.println("Another minute ticked away...");
			}
		});
		// Starts the scheduler.
		s.start();
		// Will run for ten minutes.
		try {
			Thread.sleep(1000L * 60L * 10L);
		} catch (InterruptedException e) {
			;
		}
		// Stops the scheduler.
		s.stop();
	}
}
```

- 引用的依赖

```
<dependency>
    <groupId>it.sauronsoftware.cron4j</groupId>
    <artifactId>cron4j</artifactId>
    <version>2.2.5</version>
</dependency>
```


2. Quartz
> Quartz是OpenSymphony开源组织在Jobscheduling领域一个开源项目，Quartz可以用来创建简单或为运行数百个，甚至是好几万个Jobs这样复杂的程序。Jobs可以做成标准的Java组件或EJBs。
Quartz的核心是调度器，他有自己的线程池，可以并发地执行多个作业。框架也是松耦合的，可以替换里面的组件。官网：http://www.quartz-scheduler.org/

- quartz提供了丰富的监听器，可以在任务被调度，被终止等情况触发。
- quartz支持集群。可支持负载均衡，高可用等。

- 引用的依赖

```
<!-- https://mvnrepository.com/artifact/org.quartz-scheduler/quartz -->
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.2.3</version>
</dependency>
```

2.2 quartz起步
> 定时调度框架的几个概念，任务Job，调度器Schedule，触发器Trigger（决定什么时候调度任务）。quartz包含几种调度器：最简单的什么时候执行，执行几次，多久执行一次。复杂的支持cron表达式。

- Trigger有优先级的概念，因为框架使用线程池调度任务，但是当有大量的任务需要被同时调度时，这个优先级就可以决定调度的顺序。
- Calendars不是java中通常的Calendars概念。Calendars可以排除掉某些特定的天。比如需要一个任务每天上午9:30执行，但是节假日又不想运行。就可以使用Calendars排除掉节假日。


下面是一个简单的例子：

```
package com.step.jliang.quartz;

import com.step.jliang.quartz.job.HelloJob;
import org.quartz.*;
import org.quartz.impl.JobDetailImpl;
import org.quartz.impl.StdSchedulerFactory;
import static org.quartz.SimpleScheduleBuilder.simpleSchedule;

public class HelloQuartzScheduling {

    public static void main(String[] args) throws SchedulerException {

        SchedulerFactory schedulerFactory = new StdSchedulerFactory();
        Scheduler scheduler = schedulerFactory.getScheduler();

        JobDetail helloJob = new JobDetailImpl("helloQuartzJob",
                Scheduler.DEFAULT_GROUP, HelloJob.class);

        Trigger trigger = TriggerBuilder.newTrigger()
                .withIdentity("trigger1", "group1")
                .startNow()
                .withSchedule(simpleSchedule().
                        repeatSecondlyForever())
                .build();

        scheduler.scheduleJob(helloJob, trigger);
        scheduler.start();
        try {
            Thread.sleep(1000 * 60 * 3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```

**推荐一篇比较好的博客.**但是因为博客引用的api比较老，很多例子跑不起来，但是讲的还是很好的。[quartz博客](http://blog.csdn.net/huihuimimi17/article/details/8215779)

3. spring框架的@Schedule
> 如果使用spring框架，再使用定时任务就很简单了。spring提供了对cron表达式的支持。只需要在定时任务上，通过@Schedule就可以。还可以配置自己的任务线程池。

具体可参考这篇博客[spring定时任务](http://blog.csdn.net/qq_33556185/article/details/51852537)