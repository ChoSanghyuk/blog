준비 내역



entity

- getter, setter (@Data)

xml

yml

- mybatis
- datasource

dao





Registering many MyBatis XML files does not impose the same type of performance overhead as creating many prepared statements, but it can have an impact on the performance and maintenance of your application in other ways.

When you register a MyBatis XML file, the framework will parse the file and generate the corresponding SQL statements and mappings. This parsing and generation process can take some time and consume some resources, especially if you have many XML files.

However, the impact of registering many MyBatis XML files is generally not as significant as the impact of creating many prepared statements, as the parsing and generation process only happens once per file, typically at application startup time.

The main impact of registering many MyBatis XML files is on the maintenance of your application. When you have many XML files, it can become more difficult to manage and maintain your SQL statements and mappings, especially if they are spread across multiple files.

To mitigate this impact, you should aim to organize your MyBatis XML files in a logical and manageable way. For example, you could group related SQL statements and mappings into separate files, or use a naming convention that makes it easy to identify and locate specific files.

In summary, registering many MyBatis XML files can have an impact on the performance and maintenance of your application, but the impact is generally not as significant as the impact of creating many prepared statements. The key is to organize your XML files in a way that makes them easy to manage and maintain over time.







yml 파일

```yaml
wafful:
	mybatis:
		enabled:true
		config-location: sql/configuration.xml
		mapper-location: classpath:/sql/**/sql-*.xml
		type-aliases-package: com.ooo.ooo.ooo
```



configuration.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd" >
<configuration>
    
    <settings>
    	<setting name="cachedEnabled" value="true"/>
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="multipleResultSetsEnabled" value="true"/>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <setting name="callSettersOnNulls" value="true"/>
        <setting name="jdbcTypeForNull" value="NULL"/>
        <setting name="logImpl" value="SLF4J"/>
        <setting name="defaultExecutorType" value="REUSE"/>
    </settings>
    
    <typeAliases>
    	<typeAlias alias="LowerCamleCaseMap" type = "com.ooo.ooo.ooo.map.LowerCamelCaseMap"/>
        <package name = "ooo.ooo.ooo.online"></package>
    </typeAliases>
    <typeHandlers>
    	<typeHandler handler="org.apache.ibatis.type.ClobTypeHandler" jdbcType="CLOB" javaType="java.lang.String"/>
    </typeHandlers>
    
    
    
    <!--여기 아래부터는 블로그글-->
	<!--mapper에서 매칭할 parmeter Type 별칭 설정-->
	<typeAliases>
		<typeAlias type="kr.or.iei.student.model.vo.Student" alias="student"/>
	</typeAliases>

	<environments default="development">
		<!-- environment id를 구분하여 연결할 DB를 여려개 구성할 수 도 있음 -->
		<environment id="development">
			<transactionManager type="JDBC"/>
				<dataSource type="POOLED">
					<property name="driver" value="oracle.jdbc.driver.OracleDriver"/>
					<property name="url" value="jdbc:oracle:thin:@localhost:1521:xe"/>
					<property name="username" value="mybatis"/>
					<property name="password" value="mybatis"/>
				</dataSource>
		</environment>
	</environments>
	
	<mappers>
		<mapper resource="/student/student-mapper.xml"/> 
	</mappers>
	
	
</configuration>
```



mapper

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "//mybatis.org/DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="mybatis">
  
  <select id="retrieveDepartment"
          resultType=""
          parameterType = "">
  	SELECT * FORM DEPARTMENT
  </select>
  
  
  <insert id="studentInsertValue" parameterType="student">
  	INSERT INTO STUDENT VALUES(student_seq.nextval, #{studentName},#{studentTel},#{studentEmail},#{studentAddr},default)
  </insert>
  
  
</mapper>
```











