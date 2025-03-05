### 报错信息

```plaintext
Caused by: org.hibernate.HibernateException: Access to DialectResolutionInfo cannot be null when 'hibernate.dialect' not set
```

---

#### 问题原因

上述错误通常是由于 Hibernate 无法自动检测数据库方言（Dialect）导致的。Hibernate 需要知道目标数据库的类型来生成正确的 SQL 语句。如果未显式指定 `hibernate.dialect` 参数，就会抛出此异常。

---

#### 解决方案

##### **1. 使用 `application.properties` 配置**

如果使用的是 `application.properties` 文件，添加以下配置：

```properties
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
```

---

##### **2. 使用 `application.yml` 配置**

如果使用的是 `application.yml` 文件，在 JPA 配置中添加以下内容：

```yaml
spring:
  jpa:
    database-platform: org.hibernate.dialect.MySQL8Dialect
```

---

#### 注意事项

- **数据库方言的选择**：`MySQL8Dialect` 是针对 MySQL 8.x 版本的方言。如果您使用的是其他数据库（如 PostgreSQL、Oracle 等），请根据实际情况替换为对应的方言类。例如：
  - PostgreSQL: `org.hibernate.dialect.PostgreSQLDialect`
  - Oracle: `org.hibernate.dialect.Oracle12cDialect`
  
- **版本兼容性**：确保所选方言与您的数据库版本兼容。 [Hibernate 官方文档](https://hibernate.org/) 

#### 数据库方言的选择

常见数据库的方言类列表，供参考：

| 数据库类型       | Hibernate 方言类                              |
|------------------|-----------------------------------------------|
| MySQL 5.x        | `org.hibernate.dialect.MySQL5Dialect`         |
| MySQL 8.x        | `org.hibernate.dialect.MySQL8Dialect`         |
| PostgreSQL       | `org.hibernate.dialect.PostgreSQLDialect`     |
| Oracle 12c+      | `org.hibernate.dialect.Oracle12cDialect`      |
| SQL Server 2012+ | `org.hibernate.dialect.SQLServer2012Dialect`  |