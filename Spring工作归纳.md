# Spring工作归纳    

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和Spring相关的内容：

### 解决SpringMVC中前端日志传输到后端类型不匹配的错误

``` 
// 1、首先在Date类型字段上加注解，该注解是Spring提供的
@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")

// 2、然后再该字段的get方法上加注解，该注解是Jackson提供的
@JsonSerialize(using = CustomDateTimeSerializer.class)

// 3、最后自定义日期转换类
public class CustomDateTimeSerializer extends JsonSerializer<Date> {
	@Override
	public void serialize(Date value, JsonGenerator jgen, SerializerProvider provider) throws IOException, JsonProcessingException {
		SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		String formattedDate = formatter.format(value);
		jgen.writeString(formattedDate);
	}
}
``` 

### Spring中的EL表达式可以用于注解中
  
``` 
// 首先在properties中定义好cron表达式，在注解中直接通过 ${} 表达式引用
@Scheduled(cron = "${homepage.monitor.cron}")
``` 

### SpringMVC支持的REST

 > REST一定是面向资源、面向资源、面向资源的，重要的事情说三次。
 
### SpringMVC中使用Interceptor拦截器

SpringMVC中的Interceptor拦截器是相当重要和相当有用的，它的主要作用是拦截用户的请求并进行相应的处理。比如通过它来进行权限验证，或者是来判断用户是否登陆，或者是像12306 那样子判断当前时间是否是购票时间。

SpringMVC中Interceptor的两种实现方式：

 - 实现HandlerInterceptor接口，强制要求实现所有的方法；

 - 继承HandlerInterceptorAdapter抽象类，可以自由的实现方法。
 
``` java
// 到达Controller之前的方法处理
boolean preHandle(request, response, handler);
// Controller处理之后的后续操作
void postHandle(request, response, handler, modelAndView) throws Exception;
// preHandle方法返回true时，才会执行该方法，主要用于清理资源
void afterCompletion(request, response, handler, Exception ex) throws Exception;
``` 
 
### SpringMVC中的@RequestMapping不能重复

 > 在Controller中，@RequestMapping是不能重复的，即使一个修饰的是类，另一个修饰的是方法。

### SpringMVC@ResponseBody直接返回String会乱码

SpringMVC使用 @ResponseBody注解，返加字符串不做任何处理时，有可能会出现乱码问题。这是由于 StringHttpMessageConverter 类中，默认采用的字符集是 ISO-8859-1。

``` 
// 解决方案一，在@RequestMapping中加入produces="text/html;charset=UTF-8"
@ResponseBody
@RequestMapping(value="/getBooksNameByOrid", produces="text/html;charset=UTF-8")
public String returnString() {
	return "xxx";
}
// 解决方案二，在spring配置文件进行配置

``` 

### Spring调用init方法

 > Spring中类初始化方法除了在配置文件中定义init-method，还可以直接在方法上使用注解<font color="FF2D2D">@PostConstruct</font>；
 
### Spring中开启异步任务

Spring中开启异步任务非常简单，一种是通过在applicationContext.xml中定义ThreadPoolTaskExecutor，另一种是通过@Async注解。

```
<!-- 定义ThreadPoolTaskExecutor -->
<bean id="taskExecutor" class="org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor">  
   <property name="corePoolSize" value="10"/>  
   <property name="maxPoolSize" value="30"/>  
   // 这个类中属性的意义还请自行了解
</bean>
```

### Spring中发送邮件

Spring为我们封装了发送邮件的功能，使用起来十分简单。

```
<!-- 先在applicationContext.xml中配置相应的Bean -->
<bean id="javaMailSender" class="org.springframework.mail.javamail.JavaMailSenderImpl">
	<property name="host">
		<value>${mail.host}</value>
	</property>
	<property name="javaMailProperties">
		<props>
			<prop key="mail.smtp.auth">true</prop>
			<prop key="mail.smtp.timeout">25000</prop>
			<prop key="mail.port">25</prop>
		</props>
	</property>
	<property name="username">
		<value>${mail.username}</value>
	</property>
	<property name="password">
		<value>${mail.password}</value>
	</property>
	<property name="defaultEncoding">
		<value>UTF-8</value>
	</property>
</bean>

// Spring为我们封装的邮件发送类
<bean id="simpleMailMessage" class="org.springframework.mail.SimpleMailMessage">
	<property name="from" value="${mail.from}" />
	<property name="to" value="${mail.to}" />
</bean>
```

在代码中引用邮件发送的Bean

```
@Service
public class MailSenderService {
	private static Logger logger = Logger.getLogger(MailSenderService.class);

	@Autowired
	private SimpleMailMessage simpleMailMessage;

	@Autowired
	private JavaMailSender javaMailSender;

	@Autowired
	private TaskExecutor taskExecutor;

	/**
	 * 异步发送邮件
	 */
	public void asyncSendMail(final MailBean mail) {
		taskExecutor.execute(new Runnable() {
			public void run() {
				try {
					sendMail(mail);
				} catch (Exception e) {
					logger.error("异步发送邮件失败，原因：" + e.getMessage(), e);
				}
			}
		});
	}

	/**
	 * 发生邮件
	 * 
	 * @param mail
	 */
	public void sendMail(final MailBean mail) {

		logger.info("开始建立邮件消息");
		MimeMessage message = javaMailSender.createMimeMessage();// 建立邮件消息
		MimeMessageHelper messageHelper;
		try {
			messageHelper = new MimeMessageHelper(message, true, "UTF-8");
			// 设置发件人邮箱
			if (mail.getEmailFrom() != null) {
				messageHelper.setFrom(mail.getEmailFrom());
			} else {
				messageHelper.setFrom(simpleMailMessage.getFrom());
			}

			// 设置收件人邮箱
			logger.info("开始设置收件人邮箱");
			if (mail.getToEmails() != null) {
				String[] toEmailArray = mail.getToEmails().split(";");
				List<String> toEmailList = new ArrayList<String>();
				if (null == toEmailArray || toEmailArray.length <= 0) {
					throw new MessagingException("收件人邮箱不得为空！");
				} else {
					for (String s : toEmailArray) {
						if (!StringUtils.isBlank(s)) {
							toEmailList.add(s);
						}
					}
					if (null == toEmailList || toEmailList.size() <= 0) {
						throw new MessagingException("收件人邮箱不得为空！");
					} else {
						toEmailArray = new String[toEmailList.size()];
						for (int i = 0; i < toEmailList.size(); i++) {
							toEmailArray[i] = toEmailList.get(i);
						}
					}
				}
				messageHelper.setTo(toEmailArray);
			} else {
				messageHelper.setTo(simpleMailMessage.getTo());
			}

			// 邮件主题
			logger.info("开始设置邮件主题");
			if (mail.getSubject() != null) {
				messageHelper.setSubject(mail.getSubject());
			} else {
				// String subject = new
				// String(simpleMailMessage.getSubject().getBytes("ISO8859-1"),"UTF-8").toString();
				// //转码
				messageHelper.setSubject(simpleMailMessage.getSubject());
			}

			// 邮件内容
			logger.info("开始设置邮件内容");
			if (mail.getContent() != null) {
				messageHelper.setText(mail.getContent(), true);
			} else {
				// String text = new
				// String(simpleMailMessage.getText().getBytes("ISO8859-1"),"UTF-8").toString();
				// //转码
				messageHelper.setText(simpleMailMessage.getText(), true); // true
																			// 表示启动HTML格式的邮件
			}

			// 添加图片
			if (null != mail.getPictures()) {
				logger.info("添加邮件中的图片");
				for (Iterator<Map.Entry<String, String>> it = mail.getPictures().entrySet().iterator(); it.hasNext();) {
					Map.Entry<String, String> entry = it.next();
					String cid = entry.getKey();
					String filePath = entry.getValue();
					if (null == cid || null == filePath) {
						throw new RuntimeException("请确认每张图片的ID和图片地址是否齐全！");
					}

					File file = new File(filePath);
					if (!file.exists()) {
						throw new RuntimeException("图片" + filePath + "不存在！");
					}

					FileSystemResource img = new FileSystemResource(file);
					messageHelper.addInline(cid, img);
				}
			}

			// 添加附件
			if (null != mail.getAttachments()) {
				logger.info("添加邮件中的附件");
				for (Iterator<Map.Entry<String, String>> it = mail.getAttachments().entrySet().iterator(); it.hasNext();) {
					Map.Entry<String, String> entry = it.next();
					String cid = entry.getKey();
					String filePath = entry.getValue();
					if (null == cid || null == filePath) {
						throw new RuntimeException("请确认每个附件的ID和地址是否齐全！");
					}

					File file = new File(filePath);
					if (!file.exists()) {
						throw new RuntimeException("附件" + filePath + "不存在！");
					}

					FileSystemResource fileResource = new FileSystemResource(file);
					messageHelper.addAttachment(cid, fileResource);
				}
			}
			logger.info("设置邮件日期");
			messageHelper.setSentDate(new Date());
			// 发送邮件
			logger.info("开始发送邮件");
			javaMailSender.send(message);
			logger.info("------------发送邮件完成----------");
		} catch (MessagingException e) {
			logger.error(e.getMessage(), e);
		}
	}

}
```
 
### Spring中使用@Async

今天在使用Spring中的@Async注解来执行异步操作，但总是没生效，经历一番搜索、调试终于得知原因并解决：

 - 对于使用了@Async的Bean，Spring容器在启动的时候扫包时，不能重复扫描该Bean，否则@Async会失效；

 - 在Bean中定义了@Async的方法，自身类在调用该方法时是不会异步执行的，需要通过代理类来调用来执行。

异步任务的配置信息：

``` 
<context:component-scan base-package="com.xingbook.admin.service.task" />
<!-- 引用Executor，使得@Async生效 -->
<task:annotation-driven executor="taskExecutor" scheduler="taskScheduler"/>
<!-- 8-16表示coresize为8，maxsize为16 -->
<task:executor id="taskExecutor" pool-size="8-16" queue-capacity="1024"/>
<task:scheduler id="taskScheduler" pool-size="8"/>
``` 

### Spring中@Async的小发现

``` 
@Component
public class AsyncClass {

    @Async
    void m1() {
    };
    
    @Async
    void m2() {
    }
    
    @Async
    void m3() {
        // 这里的m1()和m2()不会异步执行
        m1();
        m2();
    }

}
``` 

### Spring中配置多个事务

因为项目中引用了三个数据源，所以就不得不配置多个事务管理器了，如下：

``` 
// 需要注意的是<qualifier value=""/>这个属性标签一定要有
<!-- 启用注解式事务 -->
<tx:annotation-driven/> 
<!-- 配置多个事务管理器 -->
<bean id="transactionManager0"
	class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="xbgSource"></property>
	<qualifier value="xbgTX"/>
</bean>
<bean id="transactionManager1"
	class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="logDataSource"></property>
	<qualifier value="logTX"/>
</bean>
<bean id="transactionManager2"
	class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="xingBookDataSource"></property>
	<qualifier value="xbTX"/>
</bean>
``` 
