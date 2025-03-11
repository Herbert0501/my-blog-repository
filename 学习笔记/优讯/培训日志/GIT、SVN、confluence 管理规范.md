## Win10/Win11 VPN 连接问题

![[images/20250311130397.png]]

### 解决方案

#### 打开注册表编辑器

- 按下 `Win + R` 键，输入 `regedit`，回车。
- 或者直接搜索“注册表编辑器”，点击打开。

---

#### 方法一：手动修改注册表

1. **RasMan 参数设置**
   - **路径**：`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\Parameters`
   - **操作**：找到或新建一个 DWORD 值，命名为 `ProhibitIpSec`，并将其值设置为 `1`。

2. **PolicyAgent 参数设置**
   - **路径**：`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\PolicyAgent`
   - **操作**：找到或新建一个 DWORD 值，命名为 `AssumeUDPEncapsulationContextOnSendRule`，并将其值设置为 `2`。

---

#### 方法二：使用 `.REG` 文件自动修改

如果手动操作过于繁琐，可以选择创建一个 `.reg` 文件来自动化修改：

1. 新建一个文本文件，并将以下内容复制到该文件中。
2. 保存文件后，更改其扩展名为 `.reg`。
3. 双击运行该 `.reg` 文件。

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\Parameters]
"ProhibitIpSec"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\PolicyAgent]
"AssumeUDPEncapsulationContextOnSendRule"=dword:00000002
```

---

#### 确保服务启动

请确保以下服务已启动：

- IPsec Policy Agent  
- Remote Access Connection Manager  
- Secure Socket Tunneling Protocol Service  

**重要提示**：更改完成后可能需要 **重启系统** 才能生效。
**重要提示**：更改完成后可能需要 **重启系统** 才能生效。
**重要提示**：更改完成后可能需要 **重启系统** 才能生效。

---

## Git 分支命名规范

**例如**：

```
feature_功能_姓名_日期

feature_login_liky_20250311
```

## 后端开发规范
### 命名规范

#### 类名

- 接口：使用形容词或名词，例如 `Runnable`、`UserService`。
- 抽象类：以 `Abstract` 或 `Base` 开头，例如 `AbstractController`。
- 枚举类：使用单数形式，例如 `UserRole`。
- 异常类：以 `Exception` 结尾，例如 `UserNotFoundException`。

#### 方法名

- 获取方法：以 `get` 开头，例如 `getUserName`。
- 判断方法：以 `is` 或 `has` 开头，例如 `isValid`、`hasPermission`。
- 设置方法：以 `set` 开头，例如 `setUserName`。
- 工具方法：使用动词描述操作，例如 `calculateTotal`。

#### 变量名

- 临时变量：尽量简短，例如 `i`、`temp`。
- 集合变量：使用复数形式，例如 `users`、`orderList`。
- 布尔变量：以 `is` 或 `has` 开头，例如 `isActive`。

#### 常量名

- 全局常量：使用 `static final` 修饰，例如 `public static final int MAX_COUNT = 100;`。
- 枚举常量：使用全大写，例如 `USER_ROLE_ADMIN`。

### 代码格式

#### 缩进与换行

- **方法参数**：如果参数过多，换行并对齐。
```java
  public void processUserInfo(String userName,
                              String email,
                              String phoneNumber) {
  }
```

- **链式调用**：每个方法调用换行并缩进。
```java
    userService.getUserById(userId)
        .setUserName("John")
        .setEmail("john@example.com");
```

#### 空格

- **运算符**：左右加空格，例如 `int sum = a + b;`。
- **逗号**：右边加空格，例如 `List<String> list = Arrays.asList("a", "b", "c");`
- **大括号**：左大括号前加空格，例如 `if (condition) {`。

#### 空行

- **类成员**：字段、构造方法、方法之间用空行分隔。
- **代码块**：逻辑相关的代码块之间用空行分隔。

### 注释规范

#### 类注释

- 模板
```Java
/**
* 深圳市优讯信息技术有限公司，版权所有，违者必究
* @Copyright: Copyright© ${YEAR} UXUNCHINA
* @Company: 深圳市优讯信息技术有限公司
* @Project: ${PROJECT_NAME}
* @Path: ${PACKAGE_NAME}
* @Author: ${USER}
* @CreateTime:${YEAR}−${MONTH}-${DAY} {TIME}
* @Description: ${Description}
* @Version: V1.0
* @变更记录：
* ${YEAR}−{MONTH}-${DAY} {TIME} ${USER} 创建
*/
public class UserService {
}
 ```

#### 方法注释

- 模板
```Java
/**
 * 根据用户ID获取用户信息
 *
 * @param userId 用户ID，必须大于0
 * @return 用户信息，如果用户不存在返回null
 * @throws UserNotFoundException 用户未找到时抛出异常
 */
public User getUserById(int userId) throws UserNotFoundException {
}
 ```

#### 行注释

- 避免无意义注释：如：`int i = 0; // 初始化 i`。
- 解释复杂的逻辑：在关键算法或复杂逻辑处进行注释。

### 异常处理
#### 自定义异常

- 定义:
```java
public class LuckException extends RuntimeException {
  public LuckException(String message) {
	  super(message);
  }
}
```

- 使用:
```java
if (user == null) {
	throw new LuckException("用户不存在");
}
```

#### 异常日志

- 记录上下文信息：在日志中记录异常发生时的上下文信息。
```java
try {
	// 业务逻辑
} catch (IOException e) {
	log.error("文件读取失败, fileName={}", fileName, e);
	throw new LuckException("文件读取失败");
}
```

### 日志规范
#### 日志级别

- **DEBUG**: 用于开发调试，生产环境关闭。
- **INFO**: 记录重要业务操作，例如用户注册、订单创建。
- **WARN**: 记录潜在问题，例如参数不合法。
- **ERROR**: 记录系统异常，例如数据库连接失败。

#### 日志格式

- **模板**:
```xml
%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n
```

- **示例**:
```
2023-10-01 12:00:00:100 INFO [main] com.example.UserService - 用户登录成功, userId=123
```


### 数据库操作
#### SQL 规范

- **分页查询**: 使用 `LIMIT` 和 `OFFSET` 实现分页。
```sql
  SELECT * FROM users LIMIT 10 OFFSET 20;
```

- **索引优化**: 为常用查询字段添加索引。
    
- **避免全表扫描**: 使用 `WHERE` 条件限制查询范围。

#### 事务管理

- **传播行为**: 根据业务需求设置事务传播行为，例如 `REQUIRED`、`REQUIRES_NEW`。
    
- **隔离级别**: 根据业务需求设置事务隔离级别，例如 `READ_COMMITTED`。

### 代码复用与设计原则

#### 单一职责原则

- **类**: 每个类只负责一个功能，例如 `userService` 只处理用户相关逻辑。
- **方法**: 每个方法只完成一个任务，例如 `validateUser` 只负责用户校验。

#### 开闭原则

- **扩展**: 通过继承或接口实现扩展。
- **修改**: 避免修改现有代码，通过新增代码实现功能。

#### 依赖倒置原则

- **依赖抽象**: 高层模块依赖抽象接口，而不是具体实现。
```java
public interface UserRepository {
    User getUserById(int userId);
}

public class UserServiceImpl implements UserService {
    private final UserRepository userRepository;

    public UserServiceImpl(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
````

### 工具与框架

#### 代码格式化

- IDE 配置：统一团队 IDE 的代码格式化配置。
    
- 插件：使用 Checkstyle 等工具自动格式化代码，Alibaba JavaCoding Guidelines 插件 编码规约扫描。

#### 静态代码检查

- 规则：配置静态代码检查工具的规则，例如禁止魔法数字、强制注释。
    
- 集成：将静态代码检查集成到 CI/CD 流程中。

### API 设计规范

#### 响应格式

- **分页响应**：
```js
{
    "code": 200,
    "message": "成功",
    "data": {
        "list": [...],
        "total": 100,
        "page": 1,
        "pageSize": 10
   }
}
```
#### 错误处理

- **统一异常处理**：使用 `@ControllerAdvice` 统一处理异常。
```SQL
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ErrorResponse> handleBusinessException(BusinessException e) {
        return new ResponseEntity<>(new ErrorResponse(e.getMessage()), HttpStatus.BAD_REQUEST);
   }
}
```

### 依赖管理

#### Maven

- **依赖范围**：明确依赖的作用范围，例如 `compile`、`test`。
    
- **版本管理**：使用 `<dependencyManagement>` 统一管理依赖版本。

### 数据库设计规范

#### 表设计

- **表名**：使用小写字母和下划线分隔，例如 `user_info`。
- **字段名**：使用小写字母和下划线分隔，例如 `user_name`。
- **主键**：
    - 每张表必须有主键。
    - 主键字段命名为 `id`，类型为 `BIGINT` 或 `UUID`。
- **字段类型**：
    - 字符串使用 `VARCHAR`，并指定长度，例如 `VARCHAR(255)`。
    - 布尔值使用 `TINYINT`，1 表示 `true`，0 表示 `false`。
    - 时间戳使用 `DATETIME` 或 `TIMESTAMP`。
- **默认值**：
    - 为字段设置合理的默认值，例如 `created_at` 默认值为当前时间。
- **注释**：为表和字段添加注释，说明其用途。
```SQL
CREATE TABLE user_info (
   id BIGINT PRIMARY KEY COMMENT '用户ID',
   user_name VARCHAR(50) NOT NULL COMMENT '用户名',
   created_at DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间'
) COMMENT '用户信息表';
```

#### 索引设计

- **主键索引**：每张表必须有主键索引。
- **唯一索引**：为唯一性约束字段创建唯一索引。
- **普通索引**：为查询频繁的字段创建索引。
- **复合索引**：根据查询条件创建复合索引，注意最左前缀原则。
- **索引命名**：索引名格式为 `idx_表名_字段名`，例如 `idx_user_info_user_name`。

#### 范式与反范式

- **范式**：遵循第三范式（3NF），减少数据冗余。
- **反范式**：在查询性能要求高的场景下，适当反范式化，例如增加冗余字段。

### SQL 编写规范

#### 基本规范

- **关键字大写**：SQL 关键字使用大写，例如 `SELECT`、`FROM`。
- **缩进与换行**：SQL 语句换行并对齐。
```SQL
SELECT id, user_name, email
FROM user_info
WHERE id = 1;
```
- **避免 SELECT ***：明确指定查询字段。
- **使用别名**：为表和字段设置别名，提高可读性。
```SQL
 SELECT u.id, u.user_name
FROM user_info u
WHERE u.id = 1;
```

#### 查询优化

- **分页查询**：使用 `LIMIT` 和 `OFFSET` 实现分页。
```SQL
SELECT id, user_name
FROM user_info
LIMIT 10 OFFSET 20;
```
- **避免子查询**：尽量使用 `JOIN` 替代子查询。
- **使用 EXISTS**：检查是否存在记录时，使用 `EXISTS` 替代 `COUNT`。
```SQL
SELECT id, user_name
FROM user_info u
WHERE EXISTS (SELECT 1 FROM order_info o WHERE o.user_id = u.id);
```

#### 事务管理

- **事务范围**：事务范围尽量小，避免长事务。
- **事务隔离级别**：根据业务需求设置隔离级别，例如 `READ_COMMITTED`。
- **异常回滚**：在事务中捕获异常并回滚。
```Java
@Transactional(rollbackFor = Exception.class)
public void updateUserInfo(User user) {
    try {
        userRepository.update(user);
   } catch (Exception e) {
        throw new BusinessException("更新用户信息失败");
   }
}
```
### Java 数据库操作规范

#### 使用 ORM 框架

- **推荐框架**：使用 MyBatis 或 JPA（Hibernate）。
- **实体类映射**：实体类与数据库表一一对应，字段名与列名一致。
```Java
@Entity
@Table(name = "user_info")
public class UserInfo {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
 
    @Column(name = "user_name")
    private String userName;
}
```

#### SQL 注入防护

- **使用预编译语句**：避免拼接 SQL 字符串。
```Java
String sql = "SELECT * FROM user_info WHERE id = ?";
PreparedStatement ps = connection.prepareStatement(sql);
ps.setLong(1, userId);
```
- **使用框架特性**：MyBatis 使用 `#{}` 防止 SQL 注入。
```SQL
<select id="getUserById" resultType="User">
   SELECT * FROM user_info WHERE id = #{userId}
</select>
```

#### 批量操作

- **批量插入**：使用 `addBatch` 和 `executeBatch`。
```Java
PreparedStatement ps = connection.prepareStatement("INSERT INTO user_info (user_name) VALUES (?)");
for (User user : userList) {
    ps.setString(1, user.getUserName());
    ps.addBatch();
}
ps.executeBatch();
```
- **批量更新**：使用 MyBatis 的 `foreach` 标签。
```SQL
<update id="batchUpdateUser">
   UPDATE user_info
   SET user_name = #{userName}
   WHERE id IN
    <foreach collection="idList" item="id" open="(" separator="," close=")">
       #{id}
    </foreach>
</update>
```

### 数据库连接与连接池

#### 连接池配置

- **推荐工具**：使用 HikariCP 或 Druid。
- **配置参数**：
    - `maximumPoolSize`：最大连接数，根据业务需求设置。
    - `minimumIdle`：最小空闲连接数。
    - `maxLifetime`：连接最大存活时间。
    - `idleTimeout`：空闲连接超时时间。

#### 连接管理

- **及时关闭连接**：使用 `try-with-resources` 确保连接关闭。
```Java
try (Connection connection = dataSource.getConnection();
     PreparedStatement ps = connection.prepareStatement(sql)) {
    // 操作
}
```

### 数据库性能优化

#### 查询优化

- **索引优化**：为查询条件字段创建索引。
- **避免全表扫描**：使用 `WHERE` 条件限制查询范围。
- **分库分表**：在数据量大的场景下，使用分库分表。

#### 缓存优化

- **本地缓存**：使用 Guava Cache 或 Caffeine。
- **分布式缓存**：使用 Redis 缓存热点数据。

### 数据库安全规范

#### 权限控制

- **最小权限原则**：数据库用户只授予必要的权限。
- **禁止超级用户**：生产环境禁止使用超级用户连接数据库。

#### 数据加密

- **敏感字段加密**：对密码、手机号等敏感字段加密存储。
- **传输加密**：使用 SSL/TLS 加密数据库连接。

### 数据库监控与维护

#### 监控工具

- **慢查询监控**：使用工具（如 Prometheus）监控慢查询。
- **连接池监控**：监控连接池状态，避免连接泄漏。

#### 数据备份

- **定期备份**：每天备份一次数据。
- **备份验证**：定期验证备份数据的完整性和可恢复性。