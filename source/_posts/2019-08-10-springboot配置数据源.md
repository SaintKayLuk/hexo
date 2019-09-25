---
title: springboot配置数据源
date: 2019-08-10 18:48:52
tags:
    - java
    - spring
    - 数据库
categories: 
    - java
    - spring
    - Spring Boot
---

# springboot自动配置

spring boot替我们做了大量得自动化配置

<!-- more -->

**DataSourceAutoConfiguration**
* 配置 DataSource

**DataSourceTransactionManagerAutoConfiguration**
* 配置 DataSourceTransactionManager

**jdbcTemplateAutoConfiguration**
* 配置 jdbcTemplate

符合条件时才进行配置

<!-- more -->

# 单数据源

配置单数据源没什么好说的，只要在配置文件 application.properties或者application.yml中配置就行，配置在spring.datasource.*下面。下面我以mysql为例
其中，driver-class-name可以不写，springboot会以url自动解析，并选择合适的驱动。
~~~
spring.datasource.url=jdbc:mysql://localhost/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
spring.datasource.driver-class-name=com.mysql.jdbc.Driver(可选)
~~~

# 多数据源

在springboot当中配置多数据源有2种方式

* 配置@primary的bean
springboot会把配置了@primary的数据源作为主要数据源，即优先注入的bean。

* 排除springboot的自动配置
    DataSourceAutoConfiguration
    DataSourceTransactionManagerAutoConfiguration
    jdbcTemplateAutoConfiguration

在我们的SpringBootApplication注解里面我们排除这3个自动配置的类
~~~
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class,DataSourceTransactionManagerAutoConfiguration.class,
        JdbcTemplateAutoConfiguration.class})
~~~

再通过@ConfigurationProperties配置我们的datasource，下面我们就创建了一个foo的datasource，bean为fooDataSource。

~~~
    @Bean
    @ConfigurationProperties("foo.datasource")
    public DataSourceProperties fooDataSourceProperties(){
        return new DataSourceProperties();
    }

    @Bean
    public DataSource fooDataSource(){
        DataSourceProperties dataSourceProperties=fooDataSourceProperties();
        return dataSourceProperties.initializeDataSourceBuilder().build();
    }

~~~
