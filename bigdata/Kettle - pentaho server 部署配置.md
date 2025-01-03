# 下载

- 下载地址：[点击下载](https://pentaho.com/pentaho-community-edition/#communityProducts)
- 选择：pentaho-server-ce-xxx.zip

# 安装配置

## 条件

- Java 环境

## 启动

1. 解压对应 pentaho-server-ce-xxx.zip 到指定目录
2. 执行启动脚本 `start-pentaho.sh`


## 配置

### 更改端口

1. `vi tomcat/conf/server.xml` 更改里面对应端口
2. `vi pentaho-solutions/system/server.properties` 更改里面 `fully-qualified-server-url` 参数里面对应的端口

### 更改数据源为 mysql

#### 导入数据库

```
# mysql -u root -p 

## 导入数据结构
> source pentaho-server/data/mysql/create_repository_mysql.sql; 
> source pentaho-server/data/mysql/create_quartz_mysql.sql; 
> source pentaho-server/data/mysql/create_jcr_mysql.sql; 

```

#### 修改配置

```
# vi pentaho-solutions/system/applicationContext-spring-security-jdbc.properties datasource.driver.classname=com.mysql.jdbc.Driver
datasource.url=jdbc:mysql://localhost:3306/hibernate
datasource.username=hibuser
datasource.password=password
datasource.validation.query=SELECT 1
datasource.pool.max.wait=-1
datasource.pool.max.active=8



# vi pentaho-solutions/system/dialects/mysql5/applicationContext-spring-security-jdbc.properties 
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/hibernate
jdbc.username=hibuser
jdbc.password=password
hibernate.dialect=org.hibernate.dialect.MySQL5Dialect




# vi pentaho-solutions/system/applicationContext-spring-security-hibernate.properties 
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/hibernate
jdbc.username=hibuser
jdbc.password=password
hibernate.dialect=org.hibernate.dialect.MySQL5Dialect



# vi pentaho-solutions/system/hibernate/hibernate-settings.xml 
<config-file>system/hibernate/mysql5.hibernate.cfg.xml</config-file> 



# vi pentaho-solutions/system/hibernate/mysql5.hibernate.cfg.xml
<property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>
<property name="connection.url">jdbc:mysql://localhost:3306/hibernate</property>
<property name="dialect">org.hibernate.dialect.MySQL5InnoDBDialect</property>
<property name="connection.username">hibuser</property>
<property name="connection.password">password</property>
<property name="connection.pool_size">10</property>
<property name="show_sql">false</property>
<property name="hibernate.jdbc.use_streams_for_binary">true</property>



## 替换audit_sql.xml文件
# cp pentaho-solutions/system/dialects/mysql5/audit_sql.xml pentaho-solutions/system/



# vi pentaho-solutions/system/quartz/quartz.properties org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.StdJDBCDelegate 



# vi pentaho-solutions/system/simple-jndi/jdbc.properties SampleData/type=javax.sql.DataSource
SampleData/driver=com.mysql.cj.jdbc.Driver
SampleData/url.cj.jdbc:mysql://localhost:3306/hibernate
SampleData/user=hibuser
SampleData/password=password
Hibernate/type=javax.sql.DataSource
Hibernate/driver=com.mysql.cj.jdbc.Driver
Hibernate/url.cj.jdbc:mysql://localhost:3306/hibernate
Hibernate/user=hibuser
Hibernate/password=password
Quartz/type=javax.sql.DataSource
Quartz/driver=com.mysql.cj.jdbc.Driver
Quartz/url.cj.jdbc:mysql://localhost:3306/quartz
Quartz/user=pentaho_user
Quartz/password=password
Shark/type=javax.sql.DataSource
Shark/driver=com.mysql.cj.jdbc.Driver
Shark/url.cj.jdbc:mysql://localhost:3306/hbibernate
Shark/user=hibuser
Shark/password=password
SampleDataAdmin/type=javax.sql.DataSource
SampleDataAdmin/driver=com.mysql.cj.jdbc.Driver
SampleDataAdmin/url.cj.jdbc:mysql://localhost:3306/hibernate
SampleDataAdmin/user=hibuser
SampleDataAdmin/password=password




# vi tomcat/webapps/pentaho/META-INF/context.xml 
<?xml version="1.0" encoding="UTF-8"?>
<Context path="/pentaho" docbase="webapps/pentaho/">
        <Resource name="jdbc/Hibernate" auth="Container" type="javax.sql.DataSource" factory="org.pentaho.di.core.database.util.DecryptingDataSourceFactory" maxActive="20" minIdle="0" maxIdle="5" initialSize="0" maxWait="10000" username="hibuser" password="password" driverClassName="com.mysql.cj.jdbc.Driver" url="jdbc:mysql://localhost:3306/hibernate" validationQuery="select count(*) from INFORMATION_SCHEMA.SYSTEM_SEQUENCES" />

        <Resource name="jdbc/Quartz" auth="Container" type="javax.sql.DataSource" factory="org.pentaho.di.core.database.util.DecryptingDataSourceFactory" maxActive="20" minIdle="0" maxIdle="5" initialSize="0" maxWait="10000" username="pentaho_user" password="password" testOnBorrow="true" driverClassName="com.mysql.cj.jdbc.Driver" url="jdbc:mysql://localhost:3306/quartz" validationQuery="select count(*) from INFORMATION_SCHEMA.SYSTEM_SEQUENCES"/>

        <Resource name="jdbc/jackrabbit" auth="Container" type="javax.sql.DataSource" factory="org.pentaho.di.core.database.util.DecryptingDataSourceFactory" maxActive="20" minIdle="0" maxIdle="5" initialSize="0" maxWait="10000" username="jcr_user" password="password" driverClassName="org.mysql.cj.jdbc.Driver" url="jdbc:mysql://localhost:3306/jackrabbit" validationQuery="select 1"/>

</Context>



# vi pentaho-solutions/system/jackrabbit/repository.xml
  <!--
  <FileSystem class="org.apache.jackrabbit.core.fs.local.LocalFileSystem">
     <param name="path" value="${rep.home}/repository"/>
  </FileSystem>
  -->

<FileSystem class="org.apache.jackrabbit.core.fs.db.DbFileSystem">
    <param name="driver" value="com.mysql.cj.jdbc.Driver"/>
    <param name="url" value="jdbc:mysql://localhost:3306/jackrabbit"/>
    <param name="user" value="jcr_user"/>
    <param name="password" value="password"/>
    <param name="schema" value="mysql"/>
    <param name="schemaObjectPrefix" value="fs_repos_"/>
</FileSystem>


  <!--
  <DataStore class="org.apache.jackrabbit.core.data.FileDataStore"/>
  -->

<DataStore class="org.apache.jackrabbit.core.data.db.DbDataStore">
    <param name="url" value="jdbc:mysql://localhost:3306/jackrabbit"/>
    <param name="user" value="jcr_user"/>
    <param name="password" value="password"/>
    <param name="databaseType" value="mysql"/>
    <param name="driver" value="com.mysql.cj.jdbc.Driver"/>
    <param name="minRecordLength" value="1024"/>
    <param name="maxConnections" value="3"/>
    <param name="copyWhenReading" value="true"/>
    <param name="tablePrefix" value=""/>
    <param name="schemaObjectPrefix" value="ds_repos_"/>
</DataStore>


    <!--
    <FileSystem class="org.apache.jackrabbit.core.fs.local.LocalFileSystem">
      <param name="path" value="${rep.home}/version" />
    </FileSystem>
    -->
    
<FileSystem class="org.apache.jackrabbit.core.fs.db.DbFileSystem">
    <param name="driver" value="com.mysql.cj.jdbc.Driver"/>
    <param name="url" value="jdbc:mysql://localhost:3306/jackrabbit"/>
    <param name="user" value="jcr_user"/>
    <param name="password" value="password"/>
    <param name="schema" value="mysql"/>
    <param name="schemaObjectPrefix" value="fs_ws_"/>
</FileSystem>


    <!--
    <PersistenceManager class="org.apache.jackrabbit.core.persistence.pool.H2PersistenceManager">
      <param name="url" value="jdbc:h2:${wsp.home}/db"/>
      <param name="schemaObjectPrefix" value="${wsp.name}_"/>
    </PersistenceManager>
    -->
<PersistenceManager class="org.apache.jackrabbit.core.persistence.bundle.MySqlPersistenceManager">
    <param name="driver" value="com.mysql.cj.jdbc.Driver"/>
    <param name="url" value="jdbc:mysql://localhost:3306/jackrabbit"/>
    <param name="user" value="jcr_user" />
    <param name="password" value="password" />
    <param name="schema" value="mysql"/>
    <param name="schemaObjectPrefix" value="${wsp.name}_pm_ws_"/>
</PersistenceManager>


    <!--
    <FileSystem class="org.apache.jackrabbit.core.fs.local.LocalFileSystem">
      <param name="path" value="${wsp.home}"/>
    </FileSystem>
    -->
<FileSystem class="org.apache.jackrabbit.core.fs.db.DbFileSystem">
    <param name="driver" value="com.mysql.cj.jdbc.Driver"/>
    <param name="url" value="jdbc:mysql://localhost:3306/jackrabbit"/>
    <param name="user" value="jcr_user"/>
    <param name="password" value="password"/>
    <param name="schema" value="mysql"/>
    <param name="schemaObjectPrefix" value="fs_ver_"/>
</FileSystem>


    <!--
    <PersistenceManager class="org.apache.jackrabbit.core.persistence.pool.H2PersistenceManager">
      <param name="url" value="jdbc:h2:${rep.home}/version/db"/>
      <param name="schemaObjectPrefix" value="version_"/>
    </PersistenceManager>
    -->
<PersistenceManager class="org.apache.jackrabbit.core.persistence.bundle.MySqlPersistenceManager">
    <param name="driver" value="com.mysql.cj.jdbc.Driver"/>
    <param name="url" value="jdbc:mysql://localhost:3306/jackrabbit"/>
    <param name="user" value="jcr_user" />
    <param name="password" value="password" />
    <param name="schema" value="mysql"/>
    <param name="schemaObjectPrefix" value="pm_ver_"/>
</PersistenceManager>


    <!--
    <Journal class="org.apache.jackrabbit.core.journal.MemoryJournal"/>
    -->
<Journal class="org.apache.jackrabbit.core.journal.DatabaseJournal">
    <param name="revision" value="${rep.home}/revision.log"/>
    <param name="url" value="jdbc:mysql://localhost:3306/jackrabbit"/>
    <param name="driver" value="com.mysql.cj.jdbc.Driver"/>
    <param name="user" value="jcr_user"/>
    <param name="password" value="password"/>
    <param name="schema" value="mysql"/>
    <param name="databaseType" value="mysql"/>
    <param name="janitorEnabled" value="true"/>
    <param name="janitorSleep" value="86400"/>
    <param name="janitorFirstRunHourOfDay" value="3"/>
</Journal>



# vi tomcat/webapps/pentaho/WEB-INF/web.xml 

# 注释以下行： 
<!-- [BEGIN HSQLDB DATABASES] --> 
<!--- 
<context-param> 
	<param-name>hsqldb-databases</param-name> 
	<param-value>sampledata@../../data/hsqldb/sampledata,hibernate@../../data/hsqldb/hibernate,quartz@../../data/hsqldb/quartz</param-value>
</context-param>
--> 

<!-- [END HSQLDB DATABASES] --> 
<!-- [BEGIN HSQLDB STARTER] --> 

<!-- 
<listener> 
	<listener-class>org.pentaho.platform.web.http.context.HsqldbStartupListener</listener-class> </listener>
--> 
<!-- [END HSQLDB STARTER] -->



# vi pentaho-solutions/system/systemListeners.xml
# 注释掉id为: pooledDataSourceSystemListener, nonPooledDataSourceSystemListener, dynamicallyPooledDataSourceSystemListener, quartzSystemListener 的bean


```



#### 替换连接驱动包

```
# cp mysql-connector-java-8.0.30.jar tomcat/lib/
```



#### 禁用H2

此时启动还会报 H2 错误，即使你已经指定为 Mysql 为数据源仍会报错，这是因为 H2 数据库用于存储系统的一些内部信息和配置。

如果你确定不需要使用 H2 数据库，可以将其完全禁用，请在 GettingStartedDB-spring.xml 文件中找到H2Server Bean的定义，并将标签的 init-medthod="start" 属性更改为 ""，阻止系统初始化就可以了。

```
# vi pentaho-solutions/system/GettingStartedDB-spring.xml

```


#### 启动服务

```
# ./start-pentaho.sh
```




# 参考

- [Linux部署Kettle（pentaho-server-ce-9.4.0.0-343）记录/配置MySQL存储](https://blog.csdn.net/qq_36434219/article/details/134813608)