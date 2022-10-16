1. 若application.yml 和bootStrap.yml 在同一目录下，则bootStrap.yml 的加载顺序要高于application.yml,即bootStrap.yml  会优先被加载。

   原理：bootstrap.yml 用于应用程序上下文的引导阶段。

              bootstrap.yml 由父Spring ApplicationContext加载。

            •bootstrap.yml 可以理解成系统级别的一些参数配置，这些参数一般是不会变动的。  
            •application.yml 可以用来定义应用级别的，如果搭配 spring-cloud-config 使用 application.yml 里面定义的文件可以实现动态替换。

2. 不同位置的配置文件的加载顺序：

   在不指定要被加载文件时，默认的加载顺序：由里向外加载，所以最外层的最后被加载，会覆盖里层的属性（参考官网介绍）

SpringApplication will load properties from application.properties files in the following locations and add them to the Spring Environment: 

A /config subdirectory of the current directory.    //位于与jar包同级目录下的config文件夹，  
The current directory                             //位于与jar包同级目录下  
A classpath /config package          //idea 环境下，resource文件夹下的config文件夹  
The classpath root                                //idea 环境下，resource文件夹下  （1->4, 外->里）  
The list is ordered by precedence (properties defined in locations higher in the list override those defined in lower locations).

3. 可以通过属性指定加载某一文件：

java -jar myproject.jar --spring.config.location=classpath:/default.properties,classpath:/override.properties  
当通过spring.config.location 指定一个配置文件时，配置文件的搜索顺序如下：

file:./custom-config/  
classpath:custom-config/  
file:./config/  
file:./  
classpath:/config/  
classpath:/  
最下层的优先加载，所以最上层的属性会覆盖下层的属性；

4. 如果使用spring-cloud-config时，项目内部的resource下有bootstrap.yml文件，并且在bootstrap.yml 里配置spring.application.name, git.url,spring.active.profies. 将项目打成jar包，放到服务器上，与jar包并列的位置，有start.sh脚本， 

a. 在start 脚本里指定了配置文件：spring.config.location=./bootstrap.yml, 则配置文件的加载顺序将为：

1. cloud-config 仓库里指定的yml 配置；

2. ./bootstrap.yml

3. classpath:/bootstrap.yml

4. 外部application.yml

5. 内部application.yml

b. 在start 脚本里指定了配置文件：spring.config.location=./application.yml, 则配置文件的加载顺序将为：

1. cloud-config 仓库里指定的yml 配置；

2. ./application.yml

3.  classpath:/application.yml

4. ./bootstrap.yml

5. classpath:/bootstrap.yml

所以，不管是jar包内还是jar运行的同级目录下，只要包含bootstrap.yml ，且为云配置，则云配置文件会覆盖其他配置文件；

```yml
spring:
  cloud:
    config:
      # 如果本地配置优先级高，那么 override-none 设置为 true，包括系统环境变量、本地配置文件等配置
      override-none: true
      # 如果想要远程配置优先级高，那么 allow-override 设置为 false，如果想要本地配置优先级高那么 allow-override 设置为 true
      allow-override: true
      # 只有系统环境变量或者系统属性才能覆盖远程配置文件的配置，本地配置文件中配置优先级低于远程配置；注意本地配置文件不是系统属性
      override-system-properties: false
```

**优先级如下：**
命令行参数
java:comp/env 里的 JNDI 属性
JVM 系统属性
系统环境变量
RandomValuePropertySource 属性类生成的 random.* 属性
应用以外的 application.properties（或 yml）文件
打包在应用内的 application.properties（或 yml）文件
在应用 @Configuration 配置类中，用 @PropertySource 注解声明的属性文件
SpringApplication.setDefaultProperties 声明的默认属性
