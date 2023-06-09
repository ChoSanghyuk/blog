



ResultSet에서 컬럼 정보 얻는 법

```java
ResultSetMetaData rsmd = resultSet.getMetaData();
n = rsmd.getColumnCount();

for(int i = 1; i <=n ; i++){
    System.out.println(rsmd.getColumnName(i)); // select하는 대상 자체를 가져옴
    System.out.println(rsmd.getColumnLable(i)); // AS 뒤의 alias 가져옴
}

```

