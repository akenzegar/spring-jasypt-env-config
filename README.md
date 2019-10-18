# spring-jasypt-env-config
Spring and Jasypt integration with externalized Jasypt properties and passwords

One of the most important aspect of 12-factor coding standards is externalization of environment specific variables. We will cover how we can easily achieve this using Spring Boot. We will also use JASYPT framework to encrypt these environment variables. 

We will be performing following steps:
- Download this source code 
- Create database, database user and table 
- Run JASYPT command to encrypt your user id and password 
- Configure environment variables - Windows / Linux 
- Review the application.properties and Jasypt Bean definition 
- Build your code 
- Test your application 

### Download this source code 
Clone this github repository 
```
git clone https://github.com/prashantkumashi/spring-jasypt-env-config.git 
```
### Create database, database user and table 
Run following steps using mysql CLI or workbench. 
```
create database testdb;
create user 'appuser'@'%' identified by 'apppasswd123';
grant all privileges on testdb.* to 'appuser'@'%';
flush privileges;

use testdb;

CREATE TABLE `users` (
   `userid` int(11) NOT NULL AUTO_INCREMENT,
   `username` varchar(100) NOT NULL,
   `user_type` varchar(45) DEFAULT NULL,
   PRIMARY KEY (`userid`)
);
```
### Run JASYPT command to encrypt your database user id and password 
##### Run following command for encrypting database user id. 
> Note: If you do not have JASYCPT jar file in your local maven repository, you can choose to download from ```http://www.jasypt.org/download.html```. 

```
java -cp ~/.m2/repository/org/jasypt/jasypt/1.9.3/jasypt-1.9.3.jar  org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input="appuser" password=Password algorithm=PBEWithMD5AndDES

----ENVIRONMENT-----------------

Runtime: Oracle Corporation OpenJDK 64-Bit Server VM 11.0.1+13



----ARGUMENTS-------------------

input: appuser
password: Password
algorithm: PBEWithMD5AndDES



----OUTPUT----------------------

2QOhTWf7XCnYDK2lI/eIOA==
```

##### Run the tool again for you password encryption 
```
java -cp ~/.m2/repository/org/jasypt/jasypt/1.9.3/jasypt-1.9.3.jar  org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input="apppasswd123" password=Password algorithm=PBEWithMD5AndDES

----ENVIRONMENT-----------------

Runtime: Oracle Corporation OpenJDK 64-Bit Server VM 11.0.1+13



----ARGUMENTS-------------------

input: apppasswd123
password: Password
algorithm: PBEWithMD5AndDES



----OUTPUT----------------------

PxSPx1RxhGIoFlBbWLla5YlqpI68w4la
``` 

> IMPORTANT: You can also add other command line options for further hardening your encryption, the options chosen for encryptions should also be configured in the JASYPT encryptor configuration in your java bean definition. For more details go to http://www.jasypt.org/index.html 

### Configure environment variables - Windows / Linux 
##### Setup Linux environment as follows, these variables are used in application.properties file
```
export MYSQL_DB_SERVER=10.138.0.2
export MYSQL_DB_USER="ENC(2QOhTWf7XCnYDK2lI/eIOA==)"
export MYSQL_DB_PASSWD="ENC(PxSPx1RxhGIoFlBbWLla5YlqpI68w4la)"
export JASYPT_ENCRYPTOR_ALGORITHM=PBEWithMD5AndDES
export JASYPT_ENCRYPTOR_PASSWORD=Password
```
##### Setup Windows environment as follows 
Go to Control Panel\All Control Panel Items\System and click on Advanced system settings. In the system properties window click on "Environment Variables" button. Add the five variables as shown below

[[https://github.com/prashantkumashi/spring-jasypt-env-config/raw/master/WindowsEnv.PNG|alt=Windows Environment Variables]]

### Review the application.properties and Jasypt Bean definition 
### Build your code 
### Test your application 
