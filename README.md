
七喜开源数字货币交易系统
=====================================

    七喜是一个基于ZTUO开源代码进行大量优化的数字货币交易系统，技术交流QQ群: 971164403。
    
## 与ZTUO开源版本相比的优化    
1. 修复系统内的余额锁BUG，既在高并发场景下的结算异常问题
    
2. 修改升级market项目处理逻辑，使之支持多节点集群部署，处理效率更高

3. 新增接口缓存功能，利用redis提供数据二级缓存，减少数据库压力

4. 支持mysql主从复制模式部署

5. 支持Redis哨兵模式集群环境

6. 引入Redission框架，并利用它实现分布式锁

7. 升级spring-session框架

8. 升级spring-boot框架

9. 引入xxl-job框架，利用它实现分布式自动任务调度

## 技术指标

 1.撮合引擎速度每秒3000+
 2.每秒完成600+清算
 3.支持1w+用户同时在线交易
## 愿景

    我们的使命是用Java开发世界上最好的、高性能的、安全的、开源的（重点）数字货币交易系统。

    我们希望通过开源社区的力量能相互交流，共同改进。

    非常感谢您的帮助（issue  or money。好吧，more money！），请随时提交请求或公开问题。


## 警告

1. 运营一家交易所是非常不容易的.

    七喜框架可以使你很容易的建立一套数字货币交易系统，但是，她远远比搭建一个网站要难的多得多.不要以为简单的就是点击下一步，下一步即可完成。整个体系架构分为了很多的组件，需要专业的知识或者团队才能运行成功，好在有我们，可以随时联系我们。

2. 系统安全知识.

    七喜框架不能保护你的数字资产安全，也不能保证你的系统运行安全。在部署过程中，需要注意网络安全的设置，如果你不在行的话，可以找一个专业的运维人员。

3. 法律风险

- 法律风险第一条：不要触犯中华人民共和国的法律条例。

- 技术无罪，请在法律范围内使用七喜框架。

- 如果你想使用七喜作为商业应用，最好请个律师，确保你的商业应用在法律允许的范围内。一切用于商业化项目所带来的法律和经济问题，七喜团队概不负责。

4. 你需要知道的基本知识

- 法律知识（安全第一条，法律最重要）
- Java知识（主要是spring）
- linux知识（CentOS、Ubuntu等等）
- 安全知识


### 主要技术
- 后端：Spring、SpringMVC、SpringData、SpringCloud、SpringBoot

- 数据库：Mysql、Mongodb

- 其他：redis、kafka、阿里云OSS、腾讯防水校验


### 开源许可协议
    MIT

---

# 架构设计

#### ![](https://images.gitee.com/uploads/images/2019/0824/101549_9dea9664_1725492.png)

##  模块介绍

1. cloud

- 提供SpringCloud微服务注册中心功能，为基础模块，必须部署
- 依赖服务：无

2. ucenter-api

- 提供用户相关的接口（如登录、注册、资产列表）,该模块为基础为基础模块，必须部署
- 依赖服务：mysql,kafka,redis,mongodb,短信接口，邮箱账号

3. otc-api

- 提供场外交易功能接口，没有场外交易的可以不部署
- 依赖服务：mysql,redis,mongodb,短信接口

4. exchange-api

- 提供币币交易接口，没有币币交易的项目可以不部署
- 依赖服务：mysql,redis,mongodb,kafka

5. gateway

- 微服务网关

6. admin

- 提供管理后台的所有服务接口，必须部署
- 依赖服务：mysql,redis,mongodb

7. wallet

- 提供充币、提币、获取地址等钱包服务，为基础模块，必须部署
- 依赖服务：mysql,mongodb,kafka,cloud

8. market

- 提供币种价格、k线、实时成交等接口服务，场外交易不需要部署
- 依赖服务：mysql,redis,mongodb,kafka,cloud

9. exchange

- 提供撮合交易服务，场外交易不需要部署
- 依赖服务：mysql,mongodb,kafka

##  重点业务介绍

    后端框架的核心模块为 exchange,market模块。

    其中exhcnge模块完全采用Java内存处理队列,大大加快处理逻辑,中间不牵涉数据库操作,保证处理速度快,其中项目启动后采用继承ApplicationListener方式，自动运行；

    启动后自动加载未处理的订单,重新加载到JVM中，从而保证数据的准确，exchange将订单处理后，将成交记录发送到market;

    market模块主要都是数据库操作，将用户变化信息持久化到数据库中。主要难点在于和前端交互socket推送，socket推送采用两种方式，web端socket采用SpringSocket，移动端采用Netty推送,其中netty推送通过定时任务处理。

## 环境搭建

#### CentOS环境搭建()

## 服务部署准备

1. 项目用了Lombok插件，无论用什么IDE工具，请务必先安装Lombok插件
2. 项目用了QueryDsl，如果遇见以Q开头的类找不到，请先编译一下对应的core模块，例如core、exchange-core、xxx-core这种模块
3. 找不到的jar包在项目jar文件夹下
4. jdk版本1.8以上
5. 初始化sql在sql文件夹中配置文件
配置文件打开这个设置会自动建表
#jpa
spring.jpa.hibernate.ddl-auto=update

## 修改服务配置文件
请根据服务实际部署情况修改以下配置。配置文件位置如下，如果配置文件中没有某一项配置，说明该模块未使用到该项功能，无需添加：

```
各个模块/src/main/resources/dev/application.properties
```

mysql数据库:

```
spring.datasource.**
```

reids

```
redis.**
```

mongodb(主要存储K线图相关数据)

```
spring.data.mongodb.uri
```

kafka

```
spring.kafka.bootstrap-servers
```

阿里云OSS，图片资源上传

```
aliyun.**
```

短信配置

```
sms.**
```

邮件认证

```
spring.mail.**
```

腾讯防水校验

```
water.proof.app.**
```

### 服务启动
 1. maven构建打包服务

    ```
    cd /项目路径/framework
    mvn clean package
    ```

 2. 将各个模块target文件夹下的XX.jar上传到自己的服务器

 3. 先启动cloud模块，再启动market，exchange模块，剩下的没有顺序。

 4. 启动服务

    例：

    ```
    nohup  java  -jar  /自己的jar包路径/cloud.jar  >/dev/null 2>&1 &
    ```
    
    ```
    nohup  java  -jar  /web/app/cloud.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/exchange.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/market.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/exchange-api.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/ucenter-api.jar  >/dev/null 2>&1 &
    nohup  java  -jar  /web/app/otc-api.jar  >/dev/null 2>&1 & 
    nohup  java  -jar  /web/app/chat.jar  >/dev/null 2>&1 & 
    nohup  java  -jar  /web/app/wallet.jar  >/dev/null 2>&1 & 
    nohup  java  -jar  /web/app/admin.jar  >/dev/null 2>&1 &
    ```

### 提问和建议
- 使用Isuse，我们会及时跟进解答。
- 交流群QQ: 971164403

Qīxǐ kāiyuán shùzì huòbì jiāoyì xìtǒng
=====================================

    qīxǐ shì yīgè jīyú ZTUO kāiyuán dàimǎ jìnxíng dàliàng yōuhuà de shùzì huòbì jiāoyì xìtǒng, jìshù jiāoliú QQ qún: 971164403.
    
## Yǔ ZTUO kāiyuán bǎnběn xiàng bǐ de yōuhuà    
1. Xiūfù xìtǒng nèi de yú'é suǒ BUG, jì zài gāo bìngfā chǎngjǐng xià de jiésuàn yìcháng wèntí
    
2. Xiūgǎi shēngjí market xiàngmù chǔlǐ luójí, shǐ zhī zhīchí duō jiédiǎn jíqún bùshǔ, chǔlǐ xiàolǜ gèng gāo

3. Xīn zēng jiēkǒu huǎncún gōngnéng, lìyòng redis tígōng shùjù èr jí huǎncún, jiǎnshǎo shùjùkù yālì

4. Zhīchí mysql zhǔ cóng fùzhì móshì bùshǔ

5. Zhīchí Redis shàobīng móshì jíqún huánjìng

6. Yǐnrù Redission kuàngjià, bìng lìyòng tā shíxiàn fēnbù shì suǒ

7. Shēngjí spring-session kuàngjià

8. Shēngjí spring-boot kuàngjià

9. Yǐnrù xxl-job kuàngjià, lìyòng tā shíxiàn fēnbù shì zìdòng rènwù diàodù

## jìshù zhǐbiāo

 1. Cuòhé yǐnqíng sùdù měi miǎo 3000+
 2. Měi miǎo wánchéng 600+qīngsuàn
 3. Zhīchí 1w+yònghù tóngshí zàixiàn jiāoyì
## yuànjǐng

    wǒmen de shǐmìng shì yòng Java kāifā shìjiè shàng zuì hǎo de, gāo xìngnéng de, ānquán de, kāiyuán de (zhòngdiǎn) shùzì huòbì jiāoyì xìtǒng.

    Wǒmen xīwàng tōngguò kāiyuán shèqū de lìliàng néng xiānghù jiāoliú, gòngtóng gǎijìn.

    Fēicháng gǎnxiè nín de bāngzhù (issue  or money. Hǎo ba,more money!), Qǐng suíshí tíjiāo qǐngqiú huò gōngkāi wèntí.


## Jǐnggào

1. Yùnyíng yījiā jiāoyì suǒ shì fēicháng bù róngyì de.

    Qīxǐ kuàngjià kěyǐ shǐ nǐ hěn róngyì de jiànlì yī tào shùzì huòbì jiāoyì xìtǒng, dànshì, tā yuǎn yuǎn bǐ dājiàn yīgè wǎngzhàn yào nán de duō dé duō. Bùyào yǐwéi jiǎndān de jiùshì diǎnjī xià yībù, xià yībù jí kě wánchéng. Zhěnggè tǐxì jiàgòu fēn wéi le hěnduō de zǔjiàn, xūyào zhuānyè de zhīshì huòzhě tuánduì cáinéng yùnxíng chénggōng, hǎo zài yǒu wǒmen, kěyǐ suíshí liánxì wǒmen.

2. Xìtǒng ānquán zhīshì.

    Qīxǐ kuàngjià bùnéng bǎohù nǐ de shùzì zīchǎn ānquán, yě bùnéng bǎozhèng nǐ de xìtǒng yùnxíng ānquán. Zài bùshǔ guòchéng zhōng, xūyào zhùyì wǎngluò ānquán de shèzhì, rúguǒ nǐ bùzài háng dehuà, kěyǐ zhǎo yīgè zhuānyè de yùn wéi rényuán.

3. Fǎlǜ fēngxiǎn

- fǎlǜ fēngxiǎn dì yī tiáo: Bùyào chùfàn zhōnghuá rénmín gònghéguó de fǎlǜ tiáolì.

- Jìshù wú zuì, qǐng zài fǎlǜ fànwéi nèi shǐyòng qīxǐ kuàngjià.

- Rúguǒ nǐ xiǎng shǐyòng qīxǐ zuòwéi shāngyè yìngyòng, zuì hǎo qǐng gè lǜshī, quèbǎo nǐ de shāngyè yìngyòng zài fǎlǜ yǔnxǔ de fànwéi nèi. Yīqiè yòng yú shāngyè huà xiàngmù suǒ dài lái de fǎlǜ hé jīngjì wèntí, qīxǐ tuánduì gài bù fùzé.

4. Nǐ xūyào zhīdào de jīběn zhīshì

- fǎlǜ zhīshì (ānquán dì yī tiáo, fǎlǜ zuì zhòngyào)
- Java zhīshì (zhǔyào shi spring)
- linux zhīshì (CentOS,Ubuntu děng děng)
- ānquán zhīshì


### zhǔyào jìshù
- hòu duān:Spring,SpringMVC,SpringData,SpringCloud,SpringBoot

- shùjùkù:Mysql,Mongodb

- qítā:Redis,kafka, ālǐ yún OSS, téngxùn fángshuǐ jiào yàn


### kāiyuán xǔkě xiéyì
    MIT

---

# jiàgòu shèjì

#### ![](Https://Images.Gitee.Com/uploads/images/2019/0824/101549_9dea9664_1725492.Png)

##  mókuài jièshào

1. Cloud

- tígōng SpringCloud wéi fúwù zhùcè zhōngxīn gōngnéng, wèi jīchǔ mókuài, bìxū bùshǔ
- yīlài fúwù: Wú

2. Ucenter-api

- tígōng yònghù xiāngguān de jiēkǒu (rú dēnglù, zhùcè, zīchǎn lièbiǎo), gāi mókuài wèi jīchǔ wèi jīchǔ mókuài, bìxū bùshǔ
- yīlài fúwù:Mysql,kafka,redis,mongodb, duǎnxìn jiēkǒu, yóuxiāng zhànghào

3. Otc-api

- tígōng chǎng wài jiāoyì gōngnéng jiēkǒu, méiyǒu chǎng wài jiāoyì de kěyǐ bù bùshǔ
- yīlài fúwù:Mysql,redis,mongodb, duǎnxìn jiēkǒu

4. Exchange-api

- tígōng bì bì jiāoyì jiēkǒu, méiyǒu bì bì jiāoyì de xiàngmù kěyǐ bù bùshǔ
- yīlài fúwù:Mysql,redis,mongodb,kafka

5. Gateway

- wéi fúwù wǎngguān

6. Admin

- tígōng guǎnlǐ hòutái de suǒyǒu fúwù jiēkǒu, bìxū bùshǔ
- yīlài fúwù:Mysql,redis,mongodb

7. Wallet

- tígōng chōng bì, tí bì, huòqǔ dìzhǐ děng qiánbāo fúwù, wèi jīchǔ mókuài, bìxū bùshǔ
- yīlài fúwù:Mysql,mongodb,kafka,cloud

8. Market

- tígōng bì zhǒng jiàgé,k xiàn, shíshí chéngjiāo děng jiēkǒu fúwù, chǎng wài jiāoyì bù xūyào bùshǔ
- yīlài fúwù:Mysql,redis,mongodb,kafka,cloud

9. Exchange

- tígōng cuòhé jiāoyì fúwù, chǎng wài jiāoyì bù xūyào bùshǔ
- yīlài fúwù:Mysql,mongodb,kafka

##  zhòngdiǎn yèwù jièshào

    hòu duān kuàngjià de héxīn mókuài wèi exchange,market mókuài.

    Qízhōng exhcnge mókuài wánquán cǎiyòng Java nèicún chǔlǐ duìliè, dàdà jiākuài chǔlǐ luójí, zhōngjiān bù qiānshè shùjùkù cāozuò, bǎozhèng chǔlǐ sùdù kuài, qízhōng xiàngmù qǐdòng hòu cǎiyòng jìchéng ApplicationListener fāngshì, zìdòng yùnxíng;

    qǐdòng hòu zìdòng jiāzài wèi chǔlǐ de dìngdān, chóngxīn jiāzài dào JVM zhōng, cóng'ér bǎozhèng shùjù de zhǔnquè,exchange jiāng dìngdān chǔlǐ hòu, jiāng chéngjiāo jìlù fāsòng dào market;

    market mókuài zhǔyào dōu shì shùjùkù cāozuò, jiāng yònghù biànhuà xìnxī chíjiǔ huà dào shùjùkù zhōng. Zhǔyào nándiǎn zàiyú hé qiánduān jiāohù socket tuīsòng,socket tuīsòng cǎiyòng liǎng zhǒng fāngshì,web duān socket cǎiyòng SpringSocket, yídòng duān cǎiyòng Netty tuīsòng, qízhōng netty tuīsòng tōngguò dìngshí rènwù chǔlǐ.

## Huánjìng dājiàn

#### CentOS huánjìng dājiàn ()

## fúwù bùshǔ zhǔnbèi

1. Xiàngmù yòngle Lombok chājiàn, wúlùn yòng shénme IDE gōngjù, qǐng wùbì xiān ānzhuāng Lombok chājiàn
2. Xiàngmù yòngle QueryDsl, rúguǒ yùjiàn yǐ Q kāitóu de lèi zhǎo bù dào, qǐng xiān biānyì yīxià duìyìng de core mókuài, lìrú core,exchange-core,xxx-core zhè zhǒng mókuài
3. Zhǎo bù dào de jar bāo zài xiàngmù jar wénjiàn jiā xià
4. Jdk bǎnběn 1.8 Yǐshàng
5. Chūshǐhuà sql zài sql wénjiàn jiā zhōng pèizhì wénjiàn
pèizhì wénjiàn dǎkāi zhège shèzhì huì zìdòng jiàn biǎo
#jpa
spring.Jpa.Hibernate.Ddl-auto=update

## xiūgǎi fúwù pèizhì wénjiàn
qǐng gēnjù fúwù shíjì bùshǔ qíngkuàng xiūgǎi yǐxià pèizhì. Pèizhì wénjiàn wèizhì rúxià, rúguǒ pèizhì wénjiàn zhōng méiyǒu mǒu yī xiàng pèizhì, shuōmíng gāi mókuài wèi shǐyòng dào gāi xiàng gōngnéng, wúxū tiānjiā:

```
Gège mókuài/src/main/resources/dev/application.Properties
```

mysql shùjùkù:

```
Spring.Datasource.**
```

Reids

```
redis.**
```

Mongodb(zhǔyào cúnchú K xiàn tú xiāngguān shùjù)

```
spring.Data.Mongodb.Uri
```

kafka

```
spring.Kafka.Bootstrap-servers
```

ālǐ yún OSS, túpiàn zīyuán shàngchuán

```
aliyun.**
```

Duǎnxìn pèizhì

```
sms.**
```

Yóujiàn rènzhèng

```
spring.Mail.**
```

Téngxùn fángshuǐ jiào yàn

```
water.Proof.App.**
```

### Fúwù qǐdòng
 1. Maven gòujiàn dǎbāo fúwù

    ```
    cd/xiàngmù lùjìng/framework
    mvn clean package
    ```

 2. Jiāng gège mókuài target wénjiàn jiā xià de XX.Jar shàngchuán dào zìjǐ de fúwùqì

 3. Xiān qǐdòng cloud mókuài, zài qǐdòng market,exchange mókuài, shèng xià de méiyǒu shùnxù.

 4. Qǐdòng fúwù

    lì:

    ```
    Nohup  java  -jar  /zìjǐ de jar bāo lùjìng/cloud.Jar  >/dev/null 2>&1&
    ```
    
    ```
    nohup  java  -jar  /web/app/cloud.Jar  >/dev/null 2>&1&
    nohup  java  -jar  /web/app/exchange.Jar  >/dev/null 2>&1&
    nohup  java  -jar  /web/app/market.Jar  >/dev/null 2>&1&
    nohup  java  -jar  /web/app/exchange-api.Jar  >/dev/null 2>&1&
    nohup  java  -jar  /web/app/ucenter-api.Jar  >/dev/null 2>&1&
    nohup  java  -jar  /web/app/otc-api.Jar  >/dev/null 2>&1& 
    nohup  java  -jar  /web/app/chat.Jar  >/dev/null 2>&1& 
    nohup  java  -jar  /web/app/wallet.Jar  >/dev/null 2>&1& 
    nohup  java  -jar  /web/app/admin.Jar  >/dev/null 2>&1&
    ```

### tíwèn hé jiànyì
- shǐyòng Isuse, wǒmen huì jíshí gēn jìn jiědá.
- Jiāoliú qún QQ: 971164403
Show more
3881/5000
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
Send feedback
History
Saved
Community
