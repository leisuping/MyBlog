---
title : Spring 实现定时器的几种方式
date: 2019-05-17 09:39:40
img: https://s1.ax1x.com/2022/12/07/zcIX1H.jpg
tags:
 - Spring
 - Job
 - Date
categories: 
 - 后端
 - 定时任务
keywords:
 - 后端
 - Spring
 - Spring 实现定时器的几种方式
---
> 最近在项目开发中使用到spring定时器，综合自己的开发以及在网上查找的资料总结一下。

> 1、Quartz 是我最先了解到的一个功能比较强大的spring定时器。可以实现按照指定时间执行，也可以按照指定的频率执行定时任务。只是在配置方面似乎比较复杂。
> Quartz 任务调度的核心元素有：Scheduler（任务调度器）、Trigger（触发器）、Job（任务）。其中Scheduler是实际任务调度的控制器，Trigger是定义任务调度时间的元素，Job是调度的任务。

demo实现：

（1）、Pom.xml引入依赖：

```xml
<!--集成quartz-->
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>${quartz.version}</version>
    <!-- quartz默认使用csp0连接池，如果项目使用的不是则需要排除依赖包 -->
    <exclusions>
        <exclusion>
            <artifactId>c3p0</artifactId>
            <groupId>c3p0</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
</dependency>

```
（2）、创建配置文件类

```java
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.quartz.SchedulerFactoryBean;

import javax.sql.DataSource;
import java.util.Properties;

/**
 * 定时任务配置
 *
 */
@Configuration
public class QuartzConfig {

    @Bean
    public SchedulerFactoryBean scheduler(@Qualifier("druidDataSource") DataSource dataSource) {
        //quartz参数
        Properties prop = new Properties();
        //配置实例
        //prop.put("org.quartz.scheduler.instanceName", "MyScheduler");//实例名称
        prop.put("org.quartz.scheduler.instanceId", "AUTO");
        //线程池配置
        prop.put("org.quartz.threadPool.threadCount", "5");
        //JobStore配置
        prop.put("org.quartz.jobStore.class", "org.quartz.impl.jdbcjobstore.JobStoreTX");
        prop.put("org.quartz.jobStore.tablePrefix", "QRTZ_");

        SchedulerFactoryBean factory = new SchedulerFactoryBean();
        factory.setDataSource(dataSource);
        factory.setQuartzProperties(prop);
        factory.setSchedulerName("MyScheduler");//数据库中存储的名字
        //QuartzScheduler 延时启动，应用启动5秒后 QuartzScheduler 再启动
        factory.setStartupDelay(5);

        //factory.setApplicationContextSchedulerContextKey("applicationContextKey");
        //可选，QuartzScheduler 启动时更新己存在的Job，这样就不用每次修改targetObject后删除qrtz_job_details表对应记录了
        factory.setOverwriteExistingJobs(true);
        //设置自动启动，默认为true
        factory.setAutoStartup(true);

        return factory;
    }
}

```
（3）、创建自定义Job任务类，继承QuartzJobBean

```java
import com.alibaba.fastjson.JSON;
import com.qfedu.rongzaiboot.entity.ScheduleJob;
import com.qfedu.rongzaiboot.entity.ScheduleJobLog;
import com.qfedu.rongzaiboot.service.ScheduleJobLogService;
import com.qfedu.rongzaiboot.utils.SpringContextUtils;
import org.apache.commons.lang.StringUtils;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.quartz.QuartzJobBean;
import org.springframework.util.ReflectionUtils;
import java.lang.reflect.Method;
import java.util.Date;

public class QuartzJob extends QuartzJobBean {
    private Logger logger = LoggerFactory.getLogger(getClass());

    @Override
    protected void executeInternal(JobExecutionContext context) throws JobExecutionException {
        System.out.println("执行quartz任务。。。。。");

        String json = context.getMergedJobDataMap().getString("JOB_PARAM_KEY");
        //将获取的对象序列化的json 转化为实体类对象
        ScheduleJob scheduleJob = JSON.parseObject(json, ScheduleJob.class);

        Long jobId = scheduleJob.getJobId();
        String beanName = scheduleJob.getBeanName();
        String methodName = scheduleJob.getMethodName();
        String params = scheduleJob.getParams();

        //quartz没有被spring管理 所以通过其它方式获取service
        ScheduleJobLogService scheduleJobLogService = (ScheduleJobLogService) SpringContextUtils.getBean("scheduleJobLogServiceImpl");
        //保存任务记录日志
        ScheduleJobLog scheduleJobLog = new ScheduleJobLog();
        scheduleJobLog.setJobId(jobId);
        scheduleJobLog.setBeanName(beanName);
        scheduleJobLog.setMethodName(methodName);
        scheduleJobLog.setParams(params);
        scheduleJobLog.setCreateTime(new Date());

        long startTime = System.currentTimeMillis();

        try {
            Object targetClass = SpringContextUtils.getBean(beanName);
            Method method = null;
            //通过反射获取方法
            if (StringUtils.isNotBlank(params)) {
                method = targetClass.getClass().getDeclaredMethod(methodName, String.class);
            } else {
                method = targetClass.getClass().getDeclaredMethod(methodName);
            }

            ReflectionUtils.makeAccessible(method);//使方法具有public权限
            //根据反射执行方法
            if (StringUtils.isNotBlank(params)) {
                method.invoke(targetClass, params);
            } else {
                method.invoke(targetClass);
            }

            long endTime = System.currentTimeMillis() - startTime;

            scheduleJobLog.setStatus((byte) 0);//保存日志里的操作状态 0:成功
            scheduleJobLog.setTimes(endTime);//耗时多长时间

            logger.info("任务执行成功，任务ID：" + jobId + "，总耗时：" + endTime + "毫秒");

        } catch (Exception e) {
            long endTime = System.currentTimeMillis() - startTime;
            scheduleJobLog.setError(StringUtils.substring(e.toString(),2000));//错误消息
            scheduleJobLog.setStatus((byte)1);//失败
            scheduleJobLog.setTimes(endTime);//耗时

            e.printStackTrace();
            logger.error("任务执行失败，任务ID："+jobId);
        } finally {
            //最后调用service保存定时任务日志记录
            scheduleJobLogService.save(scheduleJobLog);
        }
    }
}

```

（4）、创建Scheduler工具类,quartz的操作核心，包括操作quartz在数据库中的表
```java
import com.alibaba.fastjson.JSON;
import com.qfedu.rongzaiboot.entity.ScheduleJob;
import com.qfedu.rongzaiboot.quartz.QuartzJob;
import org.quartz.*;

public class SchedulerUtils {

    /**
     * 创建任务
     */
    public static void createJob(Scheduler scheduler, ScheduleJob scheduleJob) {

        try {
            Long jobId = scheduleJob.getJobId();
            //创建Job对象
            JobDetail job = JobBuilder.newJob(QuartzJob.class).withIdentity("JOB_" + jobId).build();
            //获取cron表达式 并创建对象
            CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule(scheduleJob.getCronExpression())
                    .withMisfireHandlingInstructionDoNothing();
            //创建触发器
            CronTrigger trigger = TriggerBuilder.newTrigger()
                    .withIdentity("TRIGGET_" + jobId)
                    .withSchedule(cronScheduleBuilder) //将cron表达式配置到触发器
                    .build();

            //将对象josn序列化存储到Job的getJobDataMap()方法中，为后续根据获取属性执行对应的类的任务
            job.getJobDataMap().put("JOB_PARAM_KEY", JSON.toJSONString(scheduleJob));
            //存数据
            scheduler.scheduleJob(job, trigger);
            scheduler.pauseJob(JobKey.jobKey("JOB_" + jobId));//使任务处于等待状态,创建后不会执行
        } catch (SchedulerException e) {
            throw new RRException("创建任务失败", e);
        }
    }

    /**
     * 更新任务
     */
    public static void updateJob(Scheduler scheduler, ScheduleJob scheduleJob) {
        //获取新的cron表达式
        CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule(scheduleJob.getCronExpression())
                .withMisfireHandlingInstructionDoNothing();

        Long jobId = scheduleJob.getJobId();

        try {
            //拿到原有的trigger
            TriggerKey triggerKey = TriggerKey.triggerKey("TRIGGER_" + jobId);
            CronTrigger trigger = (CronTrigger) scheduler.getTrigger(triggerKey);
            //为原有的trigger赋予新的cron表达式
            trigger = trigger.getTriggerBuilder().withIdentity(triggerKey)
                    .withSchedule(cronScheduleBuilder).build();
            //执行原有的trigger更新
            scheduler.rescheduleJob(triggerKey, trigger);
        } catch (SchedulerException e) {
            e.printStackTrace();
            throw new RRException("更新定时任务失败", e);
        }
    }

    /**
     * 删除任务
     */
    public static void deleteJob(Scheduler scheduler, Long jobId) {
        try {
            scheduler.deleteJob(JobKey.jobKey("JOB_" + jobId));
        } catch (SchedulerException e) {
            e.printStackTrace();
            throw new RRException("删除定时任务失败", e);
        }
    }

    /**
     * 恢复任务
     */
    public static void resumeJob(Scheduler scheduler, Long jobId) {
        try {
            scheduler.resumeJob(JobKey.jobKey("JOB_" + jobId));
        } catch (SchedulerException e) {
            e.printStackTrace();
            throw new RRException("恢复定时任务失败", e);
        }
    }

    /**
     * 立即执行定时任务
     */
    public static void run(Scheduler scheduler, Long jobId) {
        try {
            //只执行一次并且不会改变任务的状态
            scheduler.triggerJob(JobKey.jobKey("JOB_" + jobId));
        } catch (SchedulerException e) {
            e.printStackTrace();
            throw new RRException("立即执行定时任务失败", e);
        }
    }

    /**
     * 暂停任务
     *
     * @param scheduler
     * @param jobId
     */
    public static void pauseJob(Scheduler scheduler, Long jobId) {
        try {
            scheduler.pauseJob(JobKey.jobKey("JOB_" + jobId));
        } catch (SchedulerException e) {
            e.printStackTrace();
            throw new RRException("暂停定时任务失败", e);
        }
    }
}

```

（5）、保存任务的自定义类实体类

```java
public class ScheduleJob implements Serializable {
    private static final long serialVersionUID = 1L;

    private Long jobId;

    private String beanName; //执行的类名

    private String methodName; //方法名

    private String params; //参数

    private String cronExpression; //cron表达式

    private Byte status; //任务状态 0，运行 1，暂停

    private String remark; //备注

    private Date createTime; //创建时间
}

```

> 2、Timer 是java自带的java.util.Timer类，允许调度一个java.util.TimerTask任务，可以让程序按照某个频率执行任务，但不能指定时间运行，所以一般使用的较少。

demo 实现：

```java
public class TestTimer {

   public static void main(String[] args) {

       TimerTask timerTask = new TimerTask() {
           @Override
           public void run() {
               System.out.println("task  run:"+ new Date());
           }

       };

       Timer timer = new Timer();

       //安排指定的任务在指定的时间开始进行重复的固定延迟执行。这里是每3秒执行一次
       timer.schedule(timerTask,10,3000);
   }
}

```

> 3、ScheduledExecutorService jdk自带的一个类；是基于线程池设计的定时任务类,每个调度任务都会分配到线程池中的一个线程去执行,也就是说,任务是并发执行,互不影响。

demo 实现：

```java
public class TestScheduledExecutorService {

   public static void main(String[] args) {

       ScheduledExecutorService service = Executors.newSingleThreadScheduledExecutor();

       // 参数：1、任务体 2、首次执行的延时时间
       //      3、任务执行间隔 4、间隔时间单位
       service.scheduleAtFixedRate(()->System.out.println("task ScheduledExecutorService "+new Date()), 0, 3, TimeUnit.SECONDS);

   }
}

```

> 4、Spring Task：Spring3.0以后自带的task，可以将它看成一个轻量级的Quartz，而且使用起来比Quartz简单许多。

demo 实现：

（1）、Pom.xml依赖：
```xml
<dependencies>

 <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
 </dependency>

 <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter</artifactId>
 </dependency>

 <dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok</artifactId>
   <optional>true</optional>
 </dependency>

 <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-test</artifactId>
   <scope>test</scope>
 </dependency>

</dependencies>
```

（2）、创建任务类：在主类上使用@EnableScheduling注解开启对定时任务的支持，然后启动项目。
```java
@Slf4j
@Component
public class ScheduledService {

   @Scheduled(cron = "0/5 * * * * *")
   public void scheduled(){
       log.info("=====>>>>>使用cron  {}",System.currentTimeMillis());
   }

   @Scheduled(fixedRate = 5000)
   public void scheduled1() {
       log.info("=====>>>>>使用fixedRate{}", System.currentTimeMillis());
   }

   @Scheduled(fixedDelay = 5000)
   public void scheduled2() {
       log.info("=====>>>>>fixedDelay{}",System.currentTimeMillis());
   }

}
```

> 推荐：Spring快速开启计划。可以看到三个定时任务都已经执行，并且使同一个线程中串行执行，如果只有一个定时任务，这样做肯定没问题，当定时任务增多，如果一个任务卡死，会导致其他任务也无法执行。

[参考链接](https://juejin.im/post/5ca24fb1e51d454a490a4809)





