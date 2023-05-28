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