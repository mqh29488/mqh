SpringBoot+Vue3打造企业级ERP+CRM一体化SaaS系统

## 认识CRM与ERP

CRM:客户关系管理 (Customer Relationship Management)

ERP:企业资源规划(Enterprise Resources Planning)

## 架构设计方案

<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115141050407.png" alt="image-20250115141050407" style="zoom: 25%;" />

<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115141214485.png" alt="image-20250115141214485" style="zoom:25%;" />

<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115141247680.png" alt="image-20250115141247680" style="zoom:25%;" />

<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115141343668.png" alt="image-20250115141343668" style="zoom:25%;" />

## ERP+CRM 环境搭建

- JAVA 环境:JDK17

​	官网链接:https://www.oracle.com/jp/java/technologies/downloads/#java17-windows

<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115143124319.png" alt="image-20250115143124319" style="zoom:25%;" />	系统变量:

​	系统变量 Path:%JAVA_HOME%\bin

- node 环境：v12.13.1

​	官网链接:https://nodejs.org/dist/v12.13.1/node-v12.13.1-x64.msi

​        配置缓存路径和全局模块路径

<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115144649540.png" alt="image-20250115144649540" style="zoom:25%;" />

```cmd
npm config set prefix "D:\Project\node.js-V12.13.1\node_global"
npm config set cache "D:\Project\node.js-V12.13.1\node_cache"
```

​	用户变量 Path: D:\Project\node.js-V12.13.1\node_global

​	系统变量：<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115145414040.png" alt="image-20250115145414040" style="zoom:25%;" />

​	系统变量 Path: %NODE_PATH%

- 安装Vue及脚手架:

  ```cmd
   npm install vue -g
   npm install -g @vue/cli  
  ```

- mysql:5.7.21

  ​      官方链接：https://cdn.mysql.com/archives/mysql-installer/mysql-installer-community-5.7.21.0.msi

- Redis:5.0.14.1

  ​      链接：https://github.com/tporadowski/redis/releases/download/v5.0.14.1/Redis-x64-5.0.14.1.zip

  <img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115153354835.png" alt="image-20250115153354835" style="zoom:25%;" />

  <img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115153615670.png" alt="image-20250115153615670" style="zoom:25%;" />

- Maven:3.5.4

​	链接:https://archive.apache.org/dist/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.zip	

​	系统变量:<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115153959703.png" alt="image-20250115153959703" style="zoom:25%;" />

​	系统变量 Path:%MAVEN_HOME%\bin

​	创建本地仓库路径 “mvn_repository”：<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115154257865.png" alt="image-20250115154257865" style="zoom:25%;" />

​	修改maven配置 settings.xml：

```cpp
  <localRepository>D:\Project\apache-maven-3.5.4\mvn_repository</localRepository>
     
  <mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
 </mirror>
```

- IDEA:2022.3.3 Ultra

​	推荐插件:Translation  

​		Grep Console 

​		GsonFormatPlus

​		Jadx Class Decompiler

​		Alibaba Java Coding Guidelines

​		CodeGlance3

​		MyBatisCodeHeLperPro

​		MyBatisx

## Saas系统

- **软件即服务** Software as a Service

  <img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115162004219.png" alt="image-20250115162004219" style="zoom:25%;" />

  <img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115162031056.png" alt="image-20250115162031056" style="zoom:25%;" />

  <img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115162038757.png" alt="image-20250115162038757" style="zoom:25%;" />

  <img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115162107319.png" alt="image-20250115162107319" style="zoom:25%;" />

<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115161812194.png" alt="image-20250115161812194" style="zoom:25%;" />

<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115161907495.png" alt="image-20250115161907495" style="zoom:25%;" />

设计Saas数据库

<img src="C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115162245617.png" alt="image-20250115162245617" style="zoom:25%;" />

![image-20250115162237411](C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250115162237411.png)