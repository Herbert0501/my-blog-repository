
当我们需要简化特定功能的集成或封装一个SDK时，采用自定义 Spring Boot Starter 方式，可以轻松地将该功能被引入到多个项目中使用。

接下来，我将通过示例代码详细讲解创建自定义Spring Boot Starter（假设要制作一个邮件发送的组件）的步骤。

### 1. 引入 Maven 的依赖项

```XML
...... 
...其他依赖项

<dependencies>
    <!-- Spring Boot Autoconfigure -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-autoconfigure</artifactId>
        <version>3.2.0</version>
    </dependency>

    <!-- Configuration Processor for generating meta-data -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>

    <!-- Email Sender Dependency-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-mail</artifactId>
    </dependency>
</dependencies>

```

### 2. 创建配置属性类

我们创建一个配置属性类`EmailSenderProperties`，它将绑定到应用程序的配置文件中的属性。

```Java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.validation.annotation.Validated;

import javax.validation.constraints.NotEmpty;

@ConfigurationProperties(prefix = "myemail")
@Validated
public class EmailSenderProperties {

    @NotEmpty
    private String from;

    // other...
}
```

### 3. 编写自动配置类

我们创建一个自动配置类`EmailSenderAutoConfiguration`，类会根据条件自动装配bean。

```Java
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.mail.javamail.JavaMailSender;

@Configuration
@ConditionalOnClass(JavaMailSender.class)
@EnableConfigurationProperties(EmailSenderProperties.class)
public class EmailSenderAutoConfiguration {

    private final EmailSenderProperties properties;

    public EmailSenderAutoConfiguration(EmailSenderProperties properties) {
        this.properties = properties;
    }

    @Bean
    @ConditionalOnMissingBean
    public EmailService emailService() {
        return new EmailServiceImpl(properties.getFrom());
    }
}
```

### 4. 实现类

我们实现`EmailService`接口和它的默认实现`EmailServiceImpl`

```Java
public interface EmailService {
    void send(String to, String subject, String body);
}
```

```Java
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.stereotype.Service;

@Service
public class EmailServiceImpl implements EmailService {

    private final String from;
    private final JavaMailSender mailSender;

    public EmailServiceImpl(String from, JavaMailSender mailSender) {
        this.from = from;
        this.mailSender = mailSender;
    }

    @Override
    public void send(String to, String subject, String body) {
        SimpleMailMessage message = new SimpleMailMessage();
        message.setFrom(from);
        message.setTo(to);
        message.setSubject(subject);
        message.setText(body);
        mailSender.send(message);
    }
}
```

### 5. 添加自动配置元数据

在 `src/main/resources/META-INF/` 目录下新建一个 `spring.factories`文件，并且添加对应的自动配置类的路径。

```spring
org.springframework.boot.autoconfigure.EnableAutoConfiguration=top.kangyaocoding.emailsender.EmailSenderAutoConfiguration
,
// 其他配置类使用逗号（,）分隔来装配
```

### 6. 使用该自定义组件

在 IDEA 上点击 Maven install，即可把这个组件安装到本地仓库。
然后在其他项目中引入这个 Maven 即可，例如：

```XML
        <dependency>
            <groupId>top.kangyaocoding.emailsender.sdk</groupId>
            <artifactId>top.kangyaocoding.emailsender</artifactId>
            <version>1.0</version>
        </dependency>
```

然后在我们的项目中写上配置文件（application.yml）所需的属性值，例如：

```YML
myemail:
  from: herbert501@qq.com

spring:
  mail:
    host: smtp.qq.com
    username: herbert501@qq.com
    password: 123456
```

最后引入到自己的项目的新建的服务中，即可使用，我这里写在 SpringBootApplicantion 里进行测试了。

```Java
import top.kangyaocoding.emailsender.EmailService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication implements CommandLineRunner {

    @Autowired
    private EmailService emailService;

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        emailService.send("herbert501@qq.com", "Hello", "This is a test email.");
    }
}
```