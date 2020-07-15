Qixi open source digital currency trading system
=====================================

    Qixi is a digital currency trading system based on ZTUO open source code for massive optimization, technical exchange QQ group: 971164403.
    
## Optimization compared to the ZTUO open source version
1. Fix the balance lock bug in the system, the abnormal settlement problem in high concurrency scenarios
    
2. Modify and upgrade the processing logic of the market project to support multi-node cluster deployment and higher processing efficiency

3. New interface cache function, using redis to provide secondary cache data, reduce database pressure

4. Support mysql master-slave replication mode deployment

5. Support Redis sentry mode cluster environment

6. Introduce the Redission framework and use it to implement distributed locks

7. Upgrade spring-session framework

8. Upgrade spring-boot framework

9. Introduce the xxl-job framework and use it to implement distributed automatic task scheduling

## Technical indicators

 1. Matching engine speed 3000+ per second
 2. Complete 600+ liquidation every second
 3. Support 1w+ users to trade online at the same time
## Vision

    Our mission is to use Java to develop the world's best, high-performance, secure, open source (focus) digital currency trading system.

    We hope that the power of the open source community can communicate with each other and improve together.

    Thank you very much for your help (issue or money. Okay, more money!), please feel free to submit a request or open an issue.


## caveat

1. Operating an exchange is very difficult.

    Qixi framework can make it easy for you to build a digital currency trading system, but she is much more difficult than building a website. Don't think that the simple thing is to click next, and the next step will be completed. The entire system architecture is divided into many components, which requires professional knowledge or team to run successfully. Fortunately, we can contact us at any time.

2. System security knowledge.

    Qixi Framework cannot protect your digital assets, nor can it guarantee the safe operation of your system. During the deployment process, you need to pay attention to the network security settings. If you are not good, you can find a professional operation and maintenance staff.

3. Legal risks

-Legal risk Article 1: Do not violate the laws and regulations of the People's Republic of China.

-Technology is not guilty, please use the Qixi framework within the scope of the law.

-If you want to use Qixi as a business application, it is best to ask a lawyer to ensure that your business application is within the scope allowed by law. The Qixi team is not responsible for any legal and economic problems caused by commercialization projects.

4. Basic knowledge you need to know

-Legal knowledge (safety first, law is most important)
-Java knowledge (mainly spring)
-Linux knowledge (CentOS, Ubuntu, etc.)
- safety knowledge


### Main technique
-Backend: Spring, SpringMVC, SpringData, SpringCloud, SpringBoot

-Database: Mysql, Mongodb

-Others: redis, kafka, Alibaba Cloud OSS, Tencent waterproof verification


### Open source license agreement
    MIT

---

# Architecture design

#### ![](https://images.gitee.com/uploads/images/2019/0824/101549_9dea9664_1725492.png)

## Module introduction

1. cloud

-Provide the function of SpringCloud microservice registration center, as a basic module, it must be deployed
-Dependency service: none

2. ucenter-api

-Provide user-related interfaces (such as login, registration, asset list), the module is based on the basic module, must be deployed
-Dependency services: mysql, kafka, redis, mongodb, SMS interface, email account

3. otc-api

-Provide over-the-counter trading function interface, no over-the-counter trading can be deployed
-Dependency service: mysql, redis, mongodb, SMS interface

4. exchange-api

-Provide coin-to-coin transaction interface, projects without coin-to-coin transaction can not be deployed
-Dependency services: mysql, redis, mongodb, kafka

5. gateway

-Microservice gateway

6. admin

-Provide all service interfaces of the management background, which must be deployed
-Dependency services: mysql, redis, mongodb

7. wallet

-Provide wallet services such as deposit, withdrawal, and address acquisition, as basic modules, must be deployed
-Dependency services: mysql, mongodb, kafka, cloud

8. market

-Provide interface services such as currency price, k-line, and real-time transaction, no need to deploy OTC transactions
-Dependency services: mysql, redis, mongodb, kafka, cloud

9. exchange

-Provide matching transaction services, no need to deploy OTC transactions
-Dependency services: mysql, mongodb, kafka

## Key business introduction

    The core modules of the back-end framework are exchange and market modules.

    Among them, the exhcnge module completely uses Java memory processing queues, which greatly speeds up the processing logic, does not involve database operations in the middle, and guarantees fast processing speed. Among them, the project uses the inherited ApplicationListener method to automatically run after the project starts;

    Automatically load unprocessed orders after startup and reload them into the JVM to ensure the accuracy of the data. After the order is processed by the exchange, the transaction record is sent to the market;

    The market module is mainly a database operation, which persists user change information into the database. The main difficulty is to interact with the front-end socket push. There are two ways to push socket. The socket on the web uses SpringSocket, and the mobile uses Netty push. Netty push is processed by timed tasks.

## Environment construction

#### CentOS environment build()

## Service deployment preparation

1. The project uses the Lombok plugin, no matter what IDE tool is used, please be sure to install the Lombok plugin first
2. The project uses QueryDsl. If you encounter a class beginning with Q and cannot find it, please compile the corresponding core module first, such as core, exchange-core, xxx-core, etc.
3. The jar package cannot be found under the project jar folder
4. jdk version above 1.8
5. Initialize sql configuration file in sql folder
Open this setting in the configuration file will automatically create a table
#jpa
spring.jpa.hibernate.ddl-auto=update

## Modify service configuration file
Please modify the following configuration according to the actual deployment of the service. The location of the configuration file is as follows. If there is no configuration in the configuration file, it means that this function is not used by the module and no need to add:

```
Various modules/src/main/resources/dev/application.properties
```

mysql database:

```
spring.datasource.**
```

reids

```
redis.**
```

mongodb (mainly stores K-line chart related data)

```
spring.data.mongodb.uri
```

kafka

```
spring.kafka.bootstrap-servers
```

Alibaba Cloud OSS, image resource upload

```
aliyun.**
```

SMS configuration

```
sms.**
```

Mail authentication

```
spring.mail.**
```

Tencent waterproof calibration

```
water.proof.app.**
```

### Service start
 1. maven build package service

    ```
    cd /project path/framework
    mvn clean package
    ```

 2. Upload XX.jar under the target folder of each module to its own server

 3. Start the cloud module first, and then start the market and exchange modules. There is no order for the rest.

 4. Start the service

    example:

    ```
    nohup java -jar / own jar package path/cloud.jar >/dev/null 2>&1 &
    ```
    
    ```
    nohup java -jar /web/app/cloud.jar >/dev/null 2>&1 &
    nohup java -jar /web/app/exchange.jar >/dev/null 2>&1 &
    nohup java -jar /web/app/market.jar >/dev/null 2>&1 &
    nohup java -jar /web/app/exchange-api.jar >/dev/null 2>&1 &
    nohup java -jar /web/app/ucenter-api.jar >/dev/null 2>&1 &
    nohup java -jar /web/app/otc-api.jar >/dev/null 2>&1 &
    nohup java -jar /web/app/chat.jar >/dev/null 2>&1 &
    nohup java -jar /web/app/wallet.jar >/dev/null 2>&1 &
    nohup java -jar /web/app/admin.jar >/dev/null 2>&1 &
    ```

### Questions and suggestions
-Using Isuse, we will follow up and answer in time.
-Exchange group QQ: 971164403
