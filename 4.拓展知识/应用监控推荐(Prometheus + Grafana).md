---
title: 应用监控推荐(Prometheus + Grafana)
slug: ying-yong-jian-kong-tui-jian-prometheus-grafana
cover: ""
categories: []
tags: 
halo:
  site: https://blog.kangyaocoding.top
  name: 89caf673-5700-49d6-9ced-996e49d46ffd
  publish: false
---
### 一、Prometheus

Prometheus 是一个开源的系统监控和报警工具包，最初由 SoundCloud 开发，现已成为 CNCF（云原生计算基金会）的项目。它主要用于收集和存储时间序列数据，并允许用户对这些数据进行查询和分析。

#### 1. 主要特性

- **多维数据模型**：数据以度量（metrics）和标签（labels）的形式存储。
- **强大的查询语言（PromQL）**：允许用户灵活地对数据进行查询。
- **无需依赖分布式存储**：单个服务器节点即可运行。
- **数据收集方式**：通过 HTTP pull 模型，从受监控的目标（targets）主动拉取数据。
- **内置的报警管理**：通过 Alertmanager 进行报警管理和通知。
- **多种可视化方式**：虽然 Prometheus 自带基本的图形界面，但更多时候与 Grafana 配合使用。

#### 2. 架构

Prometheus 主要由以下几个组件组成：

- **Prometheus Server**：负责抓取和存储时间序列数据。
- **Pushgateway**：用于短期任务的指标推送。
- **Client Libraries**：用于应用程序代码内嵌的指标采集。
- **Alertmanager**：处理报警。
- **Exporters**：用于从第三方服务收集数据（如 Node Exporter、Blackbox Exporter）。

### 二、Grafana

Grafana 是一个开源的可视化和分析工具，专注于为复杂的数据查询和展示提供解决方案。它支持从多个数据源获取数据，包括 Prometheus、Graphite、InfluxDB、Elasticsearch 等。

#### **主要特性：**

- **多数据源支持**：可从不同的数据源获取和整合数据。
- **丰富的可视化组件**：提供多种图表和面板组件，如时序图、柱状图、饼图、热力图等。
- **灵活的仪表盘**：用户可以根据需要创建和定制仪表盘。
- **查询编辑器**：强大的查询编辑器支持各类数据源的查询语法。
- **警报功能**：能够基于仪表盘数据设置警报。
- **用户权限管理**：支持团队协作和权限管理。

### 三、Prometheus + Grafana 结合

1. **数据收集与存储**：Prometheus 负责从各种目标（如应用、数据库、硬件设备等）中拉取数据，并将其存储为时间序列数据。
2. **数据可视化**：Grafana 从 Prometheus 中读取数据，并将其以丰富的图表形式展示在仪表盘上。
3. **报警管理**：通过 Prometheus 和 Grafana 的集成，用户可以基于监控数据创建报警规则，并在触发条件满足时接收通知。
4. **查询与分析**：使用 Grafana 的查询编辑器，用户可以通过 PromQL 灵活地查询 Prometheus 中的数据，并进行深度分析。

### 四、应用场景

- **服务器和基础设施监控**：监控 CPU、内存、磁盘、网络等系统指标。
- **应用性能监控**：监控应用程序的响应时间、错误率、请求数等指标。
- **业务指标监控**：监控订单量、用户活跃度、转化率等业务数据。
- **分布式系统监控**：监控 Kubernetes 集群、微服务架构等复杂分布式系统的运行状况。

### 五、实战部署到项目

#### 1. 环境配置

打开Linux，我们采用docker的方式进行安装，文件目录大致如下：

![2024-07-22T18:59:19-xddhnpyh.png](https://image.kangyaocoding.top/blog/post/2024-07-22T18:59:19-xddhnpyh.png)

##### datasource.yml 配置文件

```yml
apiVersion: 1.0 # 定义 API 版本，这里是 1.0 

datasources: # 数据源的定义部分 
	- name: Prometheus # 数据源的名称，可以自定义 
	  type: prometheus # 数据源的类型，这里指定为 
	  Prometheus access: proxy # 访问方式，使用 proxy 模式 
	  url: http://prometheus:9090 # Prometheus 服务的 URL 地址 
	  isDefault: true # 设置为默认数据源
```

**grafana 配置文件**：

完整文件：[点击下载 grafana.ini](https://image.kangyaocoding.top/blog/configuration/grafana.ini)

grafana.ini 文件：

1. 需要改的第一个地方。

```ini
[server]  
http_port = 4000 # 设置服务端口，默认3000，如果被占用，可以设置为4000
```

2. 需要改的第二个地方。

```ini
[database]  
# You can configure the database connection by specifying type, host, name, user and password  
# as separate properties or as on string using the url properties.  
  
# Either "mysql", "postgres" or "sqlite3", it's your choice  
type = mysql
host = 192.168.1.107:3306  
name = grafana  # 需要在MySQL中先创建名为grafana的数据库
user = root  
password = 123456
```

3. 还有一个配置文件 [点击下载 ldap.toml](https://image.kangyaocoding.top/blog/configuration/ldap.toml)。

**prometheus.yml 配置文件**：

```yml
global: 
	scrape_interval: 15s # 全局抓取间隔时间为15秒 
scrape_configs: 
	- job_name: 'api-app' # 定义抓取任务的名称 
	metrics_path: '/actuator/prometheus' # 指定指标数据的路径，默认是 /metrics
	static_configs: 
		- targets: [ '192.168.1.107:8091' ] # 后端目标地址列表，可以是多个
```

**最后进入 monitor 目录启动安装**：

执行 `docker-compose up -d`

```yml
version: '3'  
# 拷贝配置；docker container cp grafana:/etc/grafana/ ./docs/dev-ops/  
# 启用脚本；docker-compose -f docker-compose.yml up -d  
services:  
  # 数据采集  
  prometheus:
    image: bitnami/prometheus:2.47.2  
    container_name: prometheus  
    restart: always  
    ports:  
      - 9090:9090  
    volumes:  
      - ./etc/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml  
  # 监控界面  
  grafana:  
    image: grafana/grafana:10.2.0  
    container_name: grafana  
    restart: always  
    ports:  
      - 4000:4000  
    depends_on:  
      - prometheus  
    volumes:  
      - ./etc/grafana:/etc/grafana
```

注：如果images拉取不了，可以换为
`imgaes: swr.cn-north-4.myhuaweicloud.com/ddn-k8s/quay.io/prometheus/prometheus:v2.53.0`

#### 2. 配置到 Spring Boot 项目中

- **Spring MVC 架构**，直接导入到项目的pom.yml文件

```yml
<!-- 监控；actuator-上报、prometheus-采集、grafana-展示 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-prometheus</artifactId>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
        </dependency>
```

- **DDD 架构**
- 先导入到根目录的 pom.yml

```yml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->  
<dependency>  
    <groupId>org.aspectj</groupId>  
    <artifactId>aspectjweaver</artifactId>  
    <version>1.9.22.1</version>  
    <scope>runtime</scope>  
</dependency>
```

- 再导入到 app 层目录的 pom.yml

```yml
<!-- 监控；actuator-上报、prometheus-采集、grafana-展示 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-prometheus</artifactId>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
        </dependency>
```

- 再导入到 tigger 层目录的 pom.yml 使用

```yml
<!-- 监控；actuator-上报、prometheus-采集、grafana-展示 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-prometheus</artifactId>
        </dependency>
```

#### 3. 编辑配置文件

- PrometheusConfiguration

```java
package top.kangyaocoding.ai.data.config;

import io.micrometer.core.aop.CountedAspect;
import io.micrometer.core.aop.TimedAspect;
import io.micrometer.core.instrument.Clock;
import io.micrometer.core.instrument.MeterRegistry;
import io.micrometer.prometheus.PrometheusConfig;
import io.micrometer.prometheus.PrometheusMeterRegistry;
import io.prometheus.client.CollectorRegistry;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

/**
 * 描述: Prometheus 配置类
 *
 * @author K·Herbert
 * @since 2024-07-22 12:12
 */
@EnableAspectJAutoProxy
@Configuration
public class PrometheusConfiguration {

    /**
     * 配置CollectorRegistry Bean，用于收集指标数据。
     * @return 返回一个新的CollectorRegistry实例。
     */
    @Bean
    public CollectorRegistry collectorRegistry(){
        return new CollectorRegistry();
    }

    /**
     * 配置PrometheusMeterRegistry Bean，用于注册和管理指标。
     * @param config Prometheus配置，提供配置指标收集方式等。
     * @param collectorRegistry 指标收集器注册表，用于存储和管理指标数据。
     * @return 返回一个新的PrometheusMeterRegistry实例，用于Prometheus指标收集。
     */
    @Bean
    public PrometheusMeterRegistry prometheusMeterRegistry(PrometheusConfig config,  CollectorRegistry collectorRegistry){
        return new PrometheusMeterRegistry(config, collectorRegistry, Clock.SYSTEM);
    }

    /**
     * 配置TimedAspect Bean，用于对方法执行时间进行计时。
     * @param meterRegistry 用于注册计时器的MeterRegistry实例。
     * @return 返回一个新的TimedAspect实例，用于切面编程中的方法执行时间统计。
     */
    @Bean
    public TimedAspect timedAspect(MeterRegistry meterRegistry){
        return new TimedAspect(meterRegistry);
    }

    /**
     * 配置CountedAspect Bean，用于计数方法调用次数。
     * @param registry 用于注册计数器的MeterRegistry实例。
     * @return 返回一个新的CountedAspect实例，用于切面编程中的方法调用次数统计。
     */
    @Bean
    public CountedAspect countedAspect(MeterRegistry registry) {
        return new CountedAspect(registry);
    }

}
```

- 在 applicantion.yml 添加

```yml
# 监控
management:
  endpoints:
    web:
      exposure:
        include: "*" # 暴露所有端点，包括自定义端点
  endpoint:
    metrics:
      enabled: true
    health:
      show-details: always # 显示详细的健康检查信息
  metrics:
    export:
      prometheus:
        enabled: true # 启用Prometheus
  prometheus:
    enabled: true # 启用Prometheus端点
  jmx:
    enabled: true # 启用JMX监控
  system:
    cpu:
      enabled: true # 启用CPU监控
    memory:
      enabled: true # 启用内存监控
```

#### 4. 启动 Spring Boot 项目

- **Prometheus**：
- 打开 IDEA 启动Spring Boot项目，然后进入 `localhost:9090`就是 Prometheus 的可视化界面。
- 注：如果是在虚拟机搭建的需要输入`虚拟机ip:9090`。
- 进入 Status > Service Discovery 就可以看到自己绑定的数据源。

![2024-07-22T18:59:38-djttlceg.png](https://image.kangyaocoding.top/blog/post/2024-07-22T18:59:38-djttlceg.png)

- **Grafana**：
- 进入 `localhost:4000`就是 Grafana 的可视化界面。默认账号 admin 密码 admin 。
- 注：按照自己设置的 ip 和 port 。
- 在导航栏里选择 Connections > Add new connection > 添加 Prometheus 。
- 添加 Prometheus 的 ip 地址，划到下面填写一下 Performance，其他默认即可。

![2024-07-22T18:59:38-vfklmyjp.png](https://image.kangyaocoding.top/blog/post/2024-07-22T18:59:38-vfklmyjp.png)

- 进入到 Home > Dashboards > New dashboard 新建自己的模板吧。
- 可以自己建立，也可以导入一个别人制作好的模 https://grafana.com/grafana/dashboards/

![2024-07-22T18:59:38-crcefyny.png](https://image.kangyaocoding.top/blog/post/2024-07-22T18:59:38-crcefyny.png)


如果您觉得今天的文章对您有帮助，我相信您一定会喜欢我的博客。
[哈利の小屋 - Kang Yao Coding - 努力有时候战胜不了天分，但至少能让别人看得起你](https://blog.kangyaocoding.top/ "哈利の小屋 - Kang Yao Coding - 努力有时候战胜不了天分，但至少能让别人看得起你")。
在那里，我会定期更新关于计算机类的文章，并与您分享更多实用的经验和知识。欢迎您来访问和留言交流。