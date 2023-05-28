1. 1편에서 한 방법
2. AppConfig로 datasource 빈으로 설정

```java
@Configuration
public class AppConfig {

    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/mydatabase");
        dataSource.setUsername("root");
        dataSource.setPassword("password");
        return dataSource;
    }

    // other beans go here...
}
```

3. yaml 파일에서 bean으로 설정

```java
spring:
  datasource:
    driverClassName: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mydatabase
    username: root
    password: password
```

=> 2,3 번으로 auto inject 가능해짐

```java
@Repository
public class UserDaoImpl implements UserDao {

    private final DataSource dataSource;

    public UserDaoImpl(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    // DAO methods using the dataSource go here...
}
```

4. PropertiesReader 사용

```java
PropertiesReader rp = new PropertiesReader();
Properties pro = rp.returnDBProperties();

String dcn = pro.getProperty("driverClassName");
String url = pro.getProperty("url");
String user = pro.getProperty("user");
String pw = pro.getProperty("pw");
Class.forName(dcn);

Connection conn = DriverManager.getConnection(url,user,pw);
conn.setAutoCommit(false);
```

