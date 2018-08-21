# Maven基本使用：
 
### Maven最主要的命令：
- mvn clean compile
- mvn clean test
- mvn clean package
- mvn clean install
- mvn help:system打印出所有Java系统属性和环境变量，下载maven-help-plugin文件
- 执行test之前会先执行compile的，执行package之前会先执行test，install（把jar包安装到仓库中供所有人使用） 之前会执行package,可以在任何一个Maven项目中执行这些命令。
 
　　可以直接运行mvn archetype:generate去生成项目基本骨架。会有很多Archetype供选择，包括著名的Appfuse项目的Archetype,JPA项目的Archetype等。每一个Archetype前面都会对应有一个编号，同时命令行会提示一个默认的编号，其对应的Archetype为maven-archetype-quickstart,直接回车以选择该Archetype,紧接着Maven会提示输入要创建项目的groupId,artifacetId,version以及包名package
 
###   Maven坐标
- groupId: 当前Maven项目隶属的实际项目，可以是域名反向
- artifacId：该元素定义实际项目中的一个Maven项目（模块），推荐的做法是使用实际项目名作为它的前缀
- version：当前版本
- packaging：定义Maven项目的打包方式。首先，打包方式通常与所生成构件的文件扩展名对应。jar包和war包会使用不同的命令，当不定义packaging的时候，默认使用jar
- classifier：帮助定义构建输出的一些附属构件。附属构件与主构件对应
- dependency :下面的scope用来定义依赖的范围。当为test的时候，该依赖只会被加入到测试代码的测试框架，只有在测试的时候才需要，对于项目主代码，该依赖是不没有任何作用的
 
### Maven依赖
- 　　可运行mvn dependency:list　　查看当前项目的已解析依赖
- 　　可以运行mvn dependency:tree　　查看当前项目的依赖树
- 　　使用mvn dependency:analyze　　工具可以帮助分析当前项目的依赖

```
<project>
 <dependencies>
  <dependency>
   <groupId>..</groudId>
   <artifactId>..</artifactId>
   <version>..</version>
   <type>..</type><!--依赖的类型，对应于项目坐标定义的packaging,大部分情况下，该元素不必声明，其默认值为jar-->
   <sope>..</scope>
   <!--依赖的范围 
   1.compile编译依赖范围。如果没有指定就会默认使用该依赖范围。使用此依赖范围的Maven依赖，对于编译，测试，运行三种classpath都有效
   2.test 测试依赖范围。使用此依赖范围的Maven依赖，只对于测试classpath有效，典型例子是Junit
   3.provided 已提供依赖范围。使用此依赖范围的Maven依赖，对于编译和测试classpath有效，但在运行时无效。典型的例子是serlet-api,编译和测试项目的时候需要该依赖，但在运行项目的时候，由于容器已提供就不需要Maven重复地引入一遍-
   4.runtime 运行时依赖范围。使用此依赖范围的Maven依赖，对于测试和运行classpath有效，但在编译主代码时无效。典型的例子是JDBC驱动实现，项目主代码的编译只需要JDK提供的JDBC接口，只有在执行测试或者运行项目的时候才需要实现上述接口的具体JDBC驱动
   5.system系统依赖范围，该依赖与三种classpath关系，和provided依赖范围完全一致。但是，使用system范围的依赖时必须通过systemPath元素显式地指定依赖文件的 路径。由于此类依赖不是通过Maven仓库解析的，而且往往与本机系统绑定，可能 造成构建的不可移植性。
   6.import 导入依赖范围，该依赖范围不会对三种classpath产生实际影响->
   <optional>..</optional><!--标记依赖是否可选，可选依赖不会被传递-->

   <exclusions><!--用来排除传递性依赖-->
    <exclusion>
    </exclusion>
   </exclusions>
  </dependency>
 </dependencies>
</project>
```


###   Maven仓库管理：

http://repository.sonatype.org/

Nexus是当前最流行的开源Maven仓库管理软件，它提供了关键字搜索，类名搜索，坐标搜索，校验和搜索等功能。
 
###  Maven生命周期：
　　Maven拥有三套相互独立的生命周期，它们分别是clean,default和site。clean生命周期的目的是清理项目，default生命周期的目的是构建项目，而site生命周期的目的是建立项目站点。
#### 1.Clean生命周期
- pre-clean执行一些清理前需要完成的工作
- clean清理上一次构建生成的文件
- post-clean执行一些清理后需要完成的工作
 
#### 2.defaut生命周期
default生命周期定义了真正构建时所需要执行的所有步骤，它是所有生命周期中最核心的部分
- validate
- initialize
- generate-sources
- process-sources        处理项目主资源文件。一般来说，是对src/main/resources目录的内容进行变更替换等工作后，复制到项目输出的主classpath目录中
- generate-resources
- process-resoures
- compile       编译项目的主源代码
- process-classes
- generate-test-sources
- process-test-sources          处理项目测试资源文件，一般来说是对src/test/resources目录的内容进行变量替换等工作后，复制到项目输出的测试classpath目录中
- generate-test-resoures
- process-test-resoures
- test-compile              编译项目的中测试代码
- process-test-classes
- test             使用单元测试框架运行测试，测试代码不会被打包或部署
- prepare-package
- package           接受编译好的代码，打包成可发布的格式
- pre-integration-test
- integration-test
- post-integration-test
- verify
- install          将包安装到Maven本地仓库，供本地其他Maven项目使用
- deploy            将最终的包复制到远程仓库，供其他开发人员和Maven项目使用

####  3.site生命周期
-  pre-site执行一些在生成项目站点之前需要完成的工作
-  site 生成项目站点文档
-  post-site 执行一些在生成项目站点之后需要完成的工作
-  site-deploy将生成的项目站点发布到服务器上
 
##### 命令行与生命周期
- mvn clean   调用clean生命周期的clean阶段
- mvn test   调用defaut生命周期的test阶段
- mvn clean install   调用clean生命周期的clean阶段和defaut生命周期的install阶段
- mvn clean deploy site-deploy  调用了clean生命周期的clean阶段，default生命周期的deploy阶段，以及site生命周期的site-deploy阶段
##### 生命周期与插件绑定  

　　除了内置绑定外，用户还能够自己选择将某个目标绑定到生命周期的某个阶段上，这种自定义绑定方式能让Maven项目在构建过程中执行更多更富有特色的任务。
一个例子是创建项目的jar包，内置的插件绑定关系中并没有涉及这一任务。

```
 1 <!--内置绑定源码-->
 2  <build>
 3   <plugins>
 4    <plugin>
 5     <groupId>org.apache.maven.plugins</groupId>
 6     <artifacetId>maven-source-plugin</artifacetId>
 7     <version>2.1.2</version>
 8     <executions>
 9      <id>attach-soures</id>
10      <phase>verify</phase>
11      <goals>
12       <goal>jar-no-fork</goal>
13      </goals>
14     </executions>
15    </plugin>
16   </plugins>
17  </build>
```

 

### Maven的聚合与继承：

　　Maven的聚合特性能够把项目的各个模块聚合在一起构建，而Maven的继承特性则能帮助抽取各模块相同的依赖和插件等配置，在简化pom的同时，还能促进各个模块配置的一致性。
 
与聚合模块一样，作为父模块的POM，其打包类型也必须为pom
哪些POM元素可以被继承：
-  groupId 项目组ID，项目坐标的核心元素
-  version 项目版本，项目坐标的核心元素
-  description 项目的描述信息
-  organization:项目的组织信息
-  inceptionYear 项目的创始年份
-  url 项目的URL地址
-  developers 项目的开发者
-  contributions项目的贡献者信息
-  distributionManagement：项目的部署配置
-  issueManagement：项目的缺陷跟踪系统信息
-  ciManagement项目的持续集成系统系统信息
-  scm 项目的版本控制系统信息
-  maillingLists:项目的邮件列表信息
-  properties:自定义Maven属性
-  dependencies:项目的依赖配置
-  dependencyManagement:项目的依赖管理配置
-  repositories项目的仓库配置
-  build 包括项目的源码目录配置，输出目录配置，插件配置，插件管理配置等
-  reporting：包括项目的报告输出目录，报告插件配置等  
　　  
Maven提供的dependencyManagement元素既能让子模块继承到父模块的依赖配置，又能保证子模块依赖使用的灵活性。在dependencyManagement元素下的依赖声明不会引入实际的依赖
  
##### 使用Nexus创建私服
　　私服不是Maven的核心概念，它仅仅是一种衍生出来的特殊的Maven仓库。通过建立自己的私服可以降低中央仓库负荷，节省外网带宽，加速Maven构建，自己部署构件等，从而高效地使用Maven
 
##### Maven也可以执行单个测试类 
　　Maven-surefire-plugin提供了一个test参数让Maven用户能够在命令行指定要运行的测试用例。如只想运行accout-captcha的RandomGeneratioTest
$mvn test -Dtest=RandomGeneratorTest,这里test参数的值是测试用例的类名，参数还支持*和，来指定多个用例
 
##### Maven可以与hudson工具等对项目进行持续化集成
简单地说，持续集成就是快速且高频率地自动构建项目的所有源码，并为项目成员提供丰富的反馈信息。
 
##### Maven快速开发web项目
　　同任何Maven项目一样，Maven对Web项目的布局也有一个通用的约定，首先要记住的是用户必须为Web项目显式指定打包方式为war.
使用jetty-maven-plugin进行web测试,它能够周期性地检查项目内容，发现变更后自动更新到内置 的Jetty web容器中。换句话说，它帮我们省去了打包和部署的步骤。在通常情况下，我们只需要直接在IDE中修改源码，IDE能够执行自动编译，jetty-maven-plugin发现编译后的文件变化后，自动将其更新到Jetty容器。使用它非常简单

```
 1 <!--使用jetty-maven-plugin-->
 2    <plugin>
 3     <groupId>org.mortbay.jetty</groupId>
 4     <artifacetId>jetty-maven-plugin</artifacetId>
 5     <version>7.16</version>
 6     <configuration>
 7      <scanIntervalSeconds>10</scanIntervalSeconds>
 8      <webAppconfig>
 9       <contextPath>/test</contextPath><!--表示用户可以通过http://hostname:port/text/访问该应用-->
10      </webAppconfig>
11     </configuration>
12    </plugin>
```


 

下一步启动jetty-maven-plugin。在此之前需要对settings.xml做个微小的修改。默认情况下只有org.apache.maven.plugins和org.codehaus.mojo两个groupId下的插件才支持简化的命令行调用，即可以运行mvn help: system,但mvn jetty: run就不行。因为jetty-maven-plugin的groupId是org.mortbay.jetty。为了能在命令行直接运行mvn jetty: run用户需要配置settings.xml如下



```
<settings>
    <pluginGroups>
        <pluginGroup>org.mortbay.jetty</pluginGroup>
    <pluginGroups>
</settings>
```

默认是8080端口，如果希望使用其他端口，可以添加jetty.port参数 mvn jetty:run -Djetty.port=9999  

启动Jetty后，用户可以在IDE中修改各类文件，如jsp,html,css,javascript甚至于java类。只要不是修改类名，方法名等较大的操作，jetty-maven-plugin都能扫描到变更并正确地将变化更新到Web容器中。  

### Maven与自动化部署  

　　Cargo能够帮助用户实现自动化部署，它通过cargo-maven2-plugin提供了Maven集成。它几乎支持所有的Web容器。虽然它与jetty-maven-plugin的功能看起来相似，但它们的目的是不同的，jetty-maven-plugin主要有来帮助日常的快速开发和测试，而cargo-maven2-plugin主要服务于自动化部署。如专门的测试人员只需要一条简单的Maven命令，就可以构建项目并部署到Web容器，然后进行功能测试。
 
它支持两种本地部署方式，分别是standalone模式和existing模式。
 
在standalone模式中，它会从web容器的安装目录复制一份配置到用户指定的目录，然后在些基础上部署应用，每次重新构建的时候，这个目录都会被清空，所有配置被重新生成。而在exiting模式中，用户需要指定现有的web容器配置目录，然后它会直接使用这些配置并将应用部署到其对应的位置。


```
<!--使用cargo自动化部署-->
   <plugin>
    <groupId>org.codehaus.cargo</groupId>
    <artifactId>cargo-maven2-plugin</artifactId>
    <version>1.0</version>
    <configuration>
     <container>
      <containerId>tomcat6x</containerId>
      <home>d:\cmd\apache-tomcat-6.21</home> 
     </container>
     <configuration>
      <type>standalone</type>
      <home>${project.build.directory}/tomecat6x</home>
     </configuration>
    </configuration>
   </plugin>
```


 

在setting.xml中加入引插件，在命令行中输入mvn cargo:start就可以启动了。其它类似，如果要部署到远程web容器，就要提供拥有该容器的相应管理员权限。
 
远程部署：

```
<!--使用cargo自动化部署到远程web容器-->
   <plugin>
    <groupId>org.codehaus.cargo</groupId>
    <artifactId>cargo-maven2-plugin</artifactId>
    <version>1.0</version>
    <configuration>
     <container>
      <containerId>tomcat6x</containerId>
      <home>remote</home><!--远程部署也必须是remot-->
     </container>
     <configuration>
      <type>runtime</type>
     
      <properties>
       <cargo.remote.username>admin</cargo.remote.username>
       <cargo.remote.password>admin</cargo.remote.password>
       <cargo.tomcat.manager.url>http://localhost:8080/manager</cargo.tomcat.manager.url>
      </properties>
     </configuration>
    </configuration>
   </plugin>
```

 

运行命令mvn cargo:redeploy就可以执行了。如果容器已经部署了当前应用，Cargo会先卸载，然后再重新部署
 
### 版本管理  

主干（trunk）:项目开发代码的主体，是从项目开始直到当前都处于活动的状态。从这里可以获得项目最新的源代码以及几乎所有变更历史
分支（branch）:从主干的某个点分离出来的代码拷贝，通常可以在不影响主干的前提下在这里进行重大Bug的修复，或者做一些实验性质的开发。如果分支达到了预期的目的，通常发生在这里的变更会被合并（merge）到主干中。
标签（tag）用来标识主干或者分支的某个点的状态，以代表项目的某个稳定状态。这通常就是版本发布时的状态。  

#### Maven与项目release  

　　Maven Release Plugin可以帮助我们发布项目版本  
　　
它主要有三个目标：  

1.　 release:prepare依次执行以下操作  
   
    检查项目是否有未提交的代码  
    
    检查项目是否有快照版本依赖  
    
    根据用户的输入将快照版本升级为发布版  
    
    将POM中的SCM信息更新为标签地址  
    
    基于修改后的POM执行Maven构建  
     
    提交POM变更  
     
    基于用户输入为代码打标签  
     
    将代码从发布版本升级为新的快照版  
     
    提交POM变更
 
2.releas:rollback   回退release:prepare所执行的操作，将POM回退至release:prepare之前的状态，并提交。需要注意的是，该步骤不会删除release:prepare生成的标签，因此用户需要手动删除
 
3.release:perform    执行版本发布。签出release:prepare生成的标签中的源代码，并在此基础上执行mvn deploy命令打包并部署构件至仓库。
 
要为项目发布版本，首先需要为其添加正确的版本控制系统信息，因为Maven Release Plugin需要知道版本控制系统的主干，标签等地址信息后才能执行相关的操作。


```
<scm>
    <connection>scm:svn:http://....app/trunk</connection>
    <developerConnection>scm:svn:https://..../app/trunk</developerConnection>
    <url>http://.../account/trunk</url>
</scm>
```


 上述代码中，connectin表示一个只读的scm地址，而developerConnection元素表示可写的scm地址，url则表示可以浏览器中访问的scm地址。为了能让Maven识别，connection和developerConnection必须以scm开头，冒号之后的部分表示版本控制工具类型。接下来才是实际的scm地址。

该配置只告诉Maven当前代码的位置（主干），而版本发布还要涉及标签操作。因此还要配置Maven Release Plugin告诉其标签的基础目录


```
<plugin>
    <groupId>org.apache.maven.plugins</plugin>
    <artifacetId>maven-release-plugin</artifacetId>
    <version>2.0</version>
    <configuration>
        <tagBase>https://localhost/app/tags</tagBase>
    </configuration>
</plugin>
```


 

###  利用Maven自动化创建分支：
　　在正式版本1.1.0发布的同时，还可以创建一个分支用来修复将来这个版本可能遇到的重大bug。这个过程可以手工完成。如使用svn的copy操作将主干代码复制到一个名为1.1.x的分支中，然后修改分支中的pom文件，升级其版本为1.1.1-snapshot
使用Maven Release Plugin的branch目标，它能够帮我们自动化这些操作：<br>
检查本地有无未提交的代码<br>
为分支更改POM的版本，<br>
将POM中的SCM信息更新为分支地址<br>
提交以上更好<br>
将主干的代码复制到分支中<br>
修改本地代码使其回退到分之前的版本（用户可以指定新的版本）<br>
提交本地更改<br>
当然，为了让它为我们工作，和版本发布一样，必须在在pom中提供正确的scm信息，此外，由于分支操作会涉及版本控制系统里的分支地址，因此还要为它配置分支基础目录


```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifacetId>maven-release-plugin</artifacetId>
    <version>2.0</version>
    <configuration>
        <tagBase>https://localhost/app/tags/</tagBase>
        <branchBase>https://localhost/app/branches/</branchBase>
    </configuration>
</plugin>
```

 

现在运行命令：<br> mvn release:branch -DbranchName = 1.1.x \ -DuddateBranchVersions = true -DupdateWorkingCopyVersions = false
-DbranchName = 1.1.x 用来配置所有创建分支的名称。-DupdateBranchVersions = true表示为分支使用新的版本。-DupdateWorkingCopyVersion = false表示不更新本地代码（即主干）的版本。运行上述命令后，Maven会提示输入项目分支的版本
 
### Maven与签名:
　　手动地对Maven构件进行签名并将这些签名部署到Maven仓库是一件耗时的体力活。而使用Maven GPG Plugin只需要提供几行简单的配置，它就能够帮我们自动完成签名这一工作。

```
<!--使用GPG给构件加签名-->
   <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifacetId>maven-gpg-plugin</artifacetId>
    <version>1.0</version>
    <executions>
     <execution>
      <id>sign-artifacts</id>
      <phase>verify</phase>
      <goals>
       <goal>sign</goal>
      </goals>
     </execution>
    </executions>
   </plugin>
```

 然后使用一般mvn命令签名并发布项目构件：

mvn clean deploy -Dgpg.passphrase = yourpassphrase<br>
如果不提供-Dgpg.passphrase参数，运行时就会要求输入密码
 
### Maven灵活的构建：
　　Maven为了支持灵活的构建，内置了三大特性，即属性，Profile和资源过滤。

####  Maven属性：maven有6类属性<br>
1. 内置属性： 主要有两个内置属性${basedir}表示项目的根目录，即包含pom.xml文件的目录。${version}表示项目版本
2. POM属性 用户可以使用该类属性引用POM文件中对应元素的值。如<br> ${project.artifactId}对应<project><artifactId>元素的值。<br>
　　${project.build.sourceDirectory}项目主源码目录 默认src/main/java<br>
　　${project.build.testSourceDirectory}<br>
　　${project.build.directory}     默认target/<br>
　　${project.outputDirectory}项目主代码编译输出目录 默认为target/classes/<br>
　　${project.build.finalName}项目打包输出文件的名称，默认为${project.artifactId}-${project.version}<br>
　　这些属性都对应了一个POM元素，它们中的一些属性默认值都是在超级POM中定义的
 
3. 自定义属性：用户可以POM的<properties>元素下自定义Maven属性如：<br>
　　<properties><br>
   　　 <my.prop>hello</my.prop><br>
　　</properties><br>
然后在POM的其他地方使用${my.prop}的时候会被替换成hello
 
4. Settings属性:与POM属性同理，用户使用以settings开关的属性引用settings.xml文件中XmL元素的值。如常用 的${setttings.localRespository}指向用户本地仓库的地址。
 
5. Java系统属性  所有Java系统属性都可以使用Maven属性引用 。如${user.home}指向了用户目录。用户可以使用mvn help:system查看所有的Java系统属性
 
6. 环境变量 ：所有环境变量都可以使用以env.开头的Maven属性引用。如${env.JAVA_HOME}指代了JAVA_HOM环境变量的值。用户可以使用mvn help:system查看所有环境变量
 
正确使用这些Maven的属性可以帮助我们简化POM的配置和维护工作。
 
 
#### 资源过滤：
为了应对环境的变化，首先需要使用Maven属性将这些将会发生变化的部分提取出来。如数据库配置中，连接数据库使用的驱动类，URL，用户名和密码都可能发生变化，因此用maven属性取代它们：<br>
　　database.jdbc.driverClass=${db.driver}<br>
　　database.jdbc.connectionURL=${db.url}<br>
　　database.jdbc.username=${db.username}<br>
　　database.jdbc.password=${db.password}<br>
既然使用了maven属性，就应该在某个地方定义它们，这里使用一个额外的profile将其包裹：

```
<profiles>
 <profile>
  <id>dev</id>
  <properties>
   <db.driver>com.mysql.jdbc.Driver</db.driver>
   <db.url>jdbc:mysql://localhost:3306/test</db.url>
   <db.username>dev</db.username>
   <db.password>dev-pwd</db.password>
  </properties>
 </profile>
</profiles>
```

 

这里Maven属性定义与直接在POM的properties元素下定义并无二致，这里使用一个id为dev的profile，其目的是将开发环境下的配置与其他环境区别开来。
 
有了属性定义，配置文件中也使用了这些属性。这还不行，Maven属性默认只有在POM中才会被解析，也就是说，${db.username}放到POM中会就test，但是如果放到src/main/resources/目录下的文件中，构建的时候它将仍然还是${db.username}，因此，需要让Maven解析资源文件中的Maven属性。<br>
资源文件的处理其实是maven-resource-plugin做的事情，它默认的行为只是将项目主资源文件复制到主代码编译输出目录中，将测试资源文件复制到测试代码编译输出目录中。不过只要通过一些简单的POM配置，该插件就能够解析资源文件中的Maven属性，即开启资源过滤。
 
Maven默认的主资源目录和测试资源目录的定义是在超级POM中，要为资源目录过滤，只要在此基础上添加一行filtering配置即可，
```
<resources>
    <resource>
        <directory>${project.basedir}/src/main/resoureces</directory>
<filtering>true</filtering>
    </resource>
</resources>
```

类似地可以为测试资源目录开启过滤。<br>
到目前为止一切基本就绪了，我们将数据库配置的变化部分提取成了Maven属性，在POM的profile中定义了这些属性的值，并且为资源目录开启了属性过渡。最后，只需要在命令行激活profile，Maven就能够在构建项目的时候使用profile中属性值替换数据库配置文件中的属性引用。运行命令如下：<br>
mvn clean install -Pdev<br>
-P参数表示在命令行激活一个profile。这里激活了id为dev的profile.构建完成后，输出目录中的数据库配置就是开发环境的配置了。
 
####  Maven Profile
　　从前面内容我们可以看到，不同环境的构建很可能是不同的，典型的情况就是数据库配置。除此之外，有些环境可能需要配置插件使用本地文件，或者使用特殊版本的依赖，或者需要一个特殊的构件名称。为了能让构建在各个环境下方便地移植，Maven引入了profile概念。profile能够在构建的时候修改POM的一个子集，或者添加额外的配置元素。用户可以使用很多方式激活profile，以实现构建在不同环境下的移植。
继续以数据库为例，如下配置引入了一个针对开发环境的profile，类似还可以加入测试环境和产品环境profile

```
<profiles>
 <profile>
  <id>dev</id>
  <properties>
   <db.driver>com.mysql.jdbc.Driver</db.driver>
   <db.url>jdbc:mysql://localhost:3306/dev</db.url>
   <db.username>dev</db.username>
   <db.password>dev-pwd</db.password>
  </properties>
 </profile>
 <profile>
  <id>test</id>
  <properties>
   <db.driver>com.mysql.jdbc.Driver</db.driver>
   <db.url>jdbc:mysql://localhost:3306/test</db.url>
   <db.username>test</db.username>
   <db.password>test-pwd</db.password>
  </properties>
 </profile>
</profiles>
```
 

同样的属性在两上profile中的值是不一样的，dev profile是开发环境数据库的配置，而test profile提供的是测试环境数据库的配置。现在开发人员可以使用mvn命令的时候在后面加上-Pdev激活dev profile，而测试人员可以使用-Ptest激活test Profile
 
为了方便用户，Maven支持多种激活Profile方式：
1. 命令行激活 <br>
　　用户可以使用mvn命令行参数-P加上profile的id来激活profile，多个id之间可以逗号分隔。如下激活了dev -x和dev -y两个profile
mvn clean install -Pdev -x,dev -y
2. settings文件显示激活 <br>
　　如果用户希望某个profile默认一直处于激活状态，就可以配置settings.xml文件的activeProfiles元素，表示其配置的profile对于所有项目都处于激活状态。

```
<settings>
    <activeProfiles>
        <activeProfile>dev -x</activeProfile>
    </activeProfiles>
</settings>
```

3. 系统属性激活

用户可能配置当某系统属性存在的时候，自动激活profile

```
<profiles>
    <profile>
        <activation>
            <property>
                <name>test</name>
            </property>
        </activation>
    </profile>
</profiles>
```

 

可以进一步配置当某系统属性test存在，且值等于x时激活profile

```
<profiles>
    <profile>
        <activation>
            <property>
                <name>test</name>
                <value>x</value>
            </property>
        </activation>
    </profile>
</profiles>
```
  

不要忘记，用户可以在命令行声明系统属性如： <br>
mvn clean install -Dtest=x <br>
因此这其实是一种从命令行激活profile的方法，而且多个profile完全可以使用同一个系统属性来激活
4. 操作系统属性激活<br>
profile还可以自动根据操作系统环境激活，如果构建在不同的操作系统有差异，用户完全可以将这些差异写进profile，然后配置它们自动基于操作系统环境激活。


```
<profiles>
    <profile>
        <activation>
            <os>
                <name>Windows XP</name>
                < family>Windows</family>
                <arch>x86</arch>
                <version>5.1.2600</version>
            </os>
        </activation>
    </profile>
</profiles>
```

 这里family会值包括Windows,UNIX和Mac等，其它几项，name,arch,versin,用户可以通过查看环境中和系统属性os.name.os.arch.os.version获得

 
5. 文件存在与否激活<br>
Maven能够根据项目中某个文件存在与否决定是否激活profile

```
<profiles>
    <profile>
        <activation>
            <file>
                <missing>x.properties</missing>
                <exists>y.properties</exists>
            </file>
        </activation>
    </profile>
</profiles>
```


 

6. 默认激活<br>
用户可以在定义profile的时候指定其默认激活


```
<profiles>
    <profile>
        <id>dev</id>
        <activation>
            <activeByDefalut>true</activeByDefault>
        </activation>
    </profile>
</profiles>
```


 

使用activeByDefault元素用户可以指定profile自动激活，需要注意的是，如果POM中有任何一个profile通过以上其他任意一种方式激活了，所有的默认激活配置都会失效。<br>
mvn提供了一个目标帮助用户了解当前激活的profile:<br>
mvn help:active-profiles<br>
mvn还有另外一个目标用来列出当前所有的profile<br>
mvn help:all-profiles<br>
 
### profile的种类
　　根据具体需要，可以在以下位置声明profile，
- pom.xml 很显然pom.xml中声明的profile只对当前项目有效
- 用户setting.xml，用户目录下.ml/settings.xml中的profile对本机上该用户所有的Maven项目有效
- 全局settings.xml .Maven安装目录下conf/settings.xml中的profile对本机上所有的Maven项目有效
- profiles.xml 还可以在项目根目录下使用一个额外的profiles.xml文件来声明profile，不过该特性已经在Maven3中被移除，建议用户将这类profile移到settings.xml中
### web资源过滤
在web项目中，资源文件同样位于src/main/resources/目录下，它们经处理后会位于war包的WEB_INF/classes目录下，这也是Jave代码编译打包后的目录。也就是说，这类资源文件在打包过后位于应用程序的classpath中。web项目中还有另外一类资源文件，默认它们的源码文件位于src/main/webapps/目录，经打包后位于war包根目录。如一个web项目的css源码文件在src/main/webapp/css/目录，项目打包后在war包的css/目录下找到对应的css文件。这一类资源文件称为web资源文件，它们在打包后不位于应用程序的classpath中。与一般的资源文件一样，web资源文件默认不会被过滤，开启一般资源文件的过滤也不会影响到web资源文件。  

   不过有的时候，我们可能希望在构建项目的时候，为不同的客户使用不一样的资源文件。这时可以在web资源文件中使用Maven属性。然后使用profile分别定义这些maven属性的值。最后需要配置maven-war-plugin对src/main/webapp/这一web资源目录开启过滤


```
<!--开启web资源过滤-->
   <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <version>2.1</version>
    <configuration>
     <webResources>
      <filtering>true</filtering>
      <directory>src/main/webapp</directory>
      <includes>
       <include>**/*.css</include>
       <include>**/*.js</includes>
      </includes>
     </webResources>
    </configuration>
   </plugin>
```


### Maven与自动化站点生成

　　Maven不仅仅是一个自动化构建工具和一个依赖管理工具，它还能够帮助聚合项目信息，促进团队间的交流。POM可以包含各种项目信息，如项目描述，版本控制系统地址，缺陷跟踪系统地址，许可证信息，开发者信息等。用户可以让Maven自动生成一个web站点，以web的形式发布这些信息。此外，Maven社区提供了大量插件，能让用户生成各种各样的项目审查报告，包括测试覆盖率，静态代码分析，代码变更等。使用maven-site-plugin插件
 
### Maven与代码检查及代码分析
　　checkstyle代码检查插件
PMD是一款强大的源代码分析工具，它能够寻找代码中的问题，包括潜在的bug，无用代码，可优化代码，重复代码以及过于复杂的表达式。http://pmd.sourceforge.net/
要让Maven在站点生成PMD报告，只需要配置maven-pmd-plugin
 
Maven用户可以直接从命令行获得Effective POM（所有父pom及超级pom）,mvn help:effective -pom
m2eclipse能够自动下载并使用依赖的源码包。用户在项目上右击，选择Maven，再选择Download Sources让m2eclipse为当前项目的依赖下载源码包
### 编写Maven插件的一般步骤

- 创建一个maven-plugin项目:插件本身也是Maven项目，特殊的地方在于它的packaging必须是maven-plugin，用户可以使用maven-archetype-plugin快速创建一个Maven插件项目
- 为插件编写目标:每个插件都必须以包含一个或多个目标。Maven称之为Mojo(与pojo相对应)。编写插件的时候必须提供一个或多个继承自AbstractMojo的类
为目标提供配置点，大部分Maven插件及其目标都是可以配置的，因此在编写Mojo的时候需要注意提供可配置的参数
- 编写代码实现目标行为：根据实际需要实现Mojo
错误处理及日志，当Mojo发生异常时，根据情况控制Maven的运行状态。在代码中编写必要的日志便为用户提供足够的信息
- 测试插件：编写自动化的测试代码测试行为，然后再实际运行插件以验证其行为
创建一个Mojo所我必要的三项是：继承AbstractMojo,实现execute()方法，提供@goal标注<br>
### 常用archetype介绍
到目前为止，Maven仓库中已经包含了249个archetype(http://repol.maven.org/maven2/archetype-catalog.xml)此外还有大量的没有发布到中央仓库中，
比较常见的是：
- maven-archetype-quicksart  最常见的java项目基本骨架
- maven-archetype-webapp  最简单的Maven war项目的模板
- AppFuse  Archetype
　　AppFuse是一个集成了很多开源工具的项目，它由Matt Raible开发，旨在帮助 Jave编程人员快速高效地创建项目。AppFuse本身使用Maven构建，它的核心其实就是一个项目的骨架，是包含持久层，业务和展现层的一个基本结构。在AppFuse2.x中，已经集成了大量流行的开源工具，如Spring,Struts2,JPA,JSF,Tapestry等
AppFuse 为用户提供了大量Archetype，以方便用户快速创建各类型的项目，它们都使用同样的groupId org.appfuse.针对各种展现层分别为：
- appfuse-*-jsf:基于JSF展现层框架的Archetype
- appfuse-*-spring：基于Spring MVC展现框架的Archetype
- appfuse-*-struts:基于struts2展现层框架的Archetype
- appfuse-*-tapestry：基于Tapestry展现框架的Archetype
第一种展现层框架都有3个Archetype，分别为light,basic和modular，其中，light类型的Archetype只包含最简单的骨架，basic类型的Archetype则包含了一些用户管理及安全方面的特性。modular类型的Archetype会生成多模块的项目，其中的core模块包含了持久层及业务层的代码，而Web模块则是展现层的代码。
更多AppFuse 的信息可以访问官方入门手册：http://appfuse.org/display/apf/appfuse+quickstart
 
我们也可以自己动手编写一个archetype
 
Maven常用插件列表：
- maven-clean-plugin 清理项目
- maven-compile-plugin 编译项目
- maven-deply-plugin 部署项目
- maven-install-plugin 安装项目
- maven-resource-plugin 处理资源文件
- maven-site-plugin 生成站点
- maven-surefire-plugin 执行测试
- maven-jar-plugin 构建JAR项目
- maven-war-plugin 构建WAR项目
- maven-shade-plugin 构建包含依赖的JAR包
- maven-changelog-plugin 生成版本控制变更报告
- maven-checkstyle-plugin 生成CheckStyle报告 
- maven-javadoc-plugin 生成JavaDoc文档
- maven-jxr-plugin 生成源码交叉引用文档
- maven-pmd-plugin 生成PMD报告
- maven-project-info-reports-plugin 生成项目信息报告
- maven-surefire-report-plugin 生成单元测试报告
- maven-antrun-plugin 调用Ant任务
- maven-archetype-plugin  基于Archetype 生成项目骨架
- maven-assembly-plugin 构建自定义格式的分发包
- maven-dependency-plugin 依赖分析及控制
- maven-enforcer-plugin 定义规则并强制要求项目遵守
- maven-pgp-plugin 为项目构件生成pgp签名
- maven-help-plugin 获取项目及Maven环境的信息
- maven-invoker-plugin 自动运行Maven项目构建并验证
- maven-release-plugin 自动化项目版本发布
- maven-scm-plugin 集成版本控制系统
- maven-source-plugin 生成源码包
- maven-eclipse-plugin 生成Eclipse项目环境配置
- build-helper-maven-plugin 包含各种支持构建生命周期的目标
- exec-maven-plugin 运行系统程序或者Java程序
- jboss-maven-plugin 启动，停止Jboss部署项目
- properties-maven-plugin 从properties文件读写Maven属性
- sql-maven-plugin 运行SQL脚本
- tomcat-maven-plugin 启动，停止Tomcat,部署项目
- version-maven-plugin 自动化批量更新POM版本
- cargo-maven-plugin 启动、停止、配置各类web容器自动化部署web项目
- jetty-maven-plugin 集成Jetty容器，实现快速开发测试
- maven-gae-plugin 集成Google App Engine
- maven-license-plugin 自动化添加许可证证明至源码文件
- maven-android-plugin 构建Android项目
- 可以去Apaceh，Codehaus,Googlecode上找到完整的插件列表。