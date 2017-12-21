# Maven工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和Maven相关的内容：

### 一些很好用的maven镜像

``` settings.xml
<mirror>    
    <id>repo2</id>    
    <mirrorOf>central</mirrorOf>    
    <name>Human Readable Name for this Mirror.</name>    
    <url>http://repo2.maven.org/maven2/</url>    
</mirror>    
<mirror>    
    <id>net-cn</id>    
    <mirrorOf>central</mirrorOf>    
    <name>Human Readable Name for this Mirror.</name>    
    <url>http://maven.net.cn/content/groups/public/</url>     
</mirror>    
<mirror>    
    <id>ui</id>    
    <mirrorOf>central</mirrorOf>    
    <name>Human Readable Name for this Mirror.</name>    
    <url>http://uk.maven.org/maven2/</url>    
</mirror>    
<mirror>    
     <id>ibiblio</id>    
    <mirrorOf>central</mirrorOf>    
    <name>Human Readable Name for this Mirror.</name>    
    <url>http://mirrors.ibiblio.org/pub/mirrors/maven2/</url>    
</mirror>    
<mirror>    
    <id>jboss-public-repository-group</id>    
    <mirrorOf>central</mirrorOf>    
    <name>JBoss Public Repository Group</name>    
    <url>http://repository.jboss.org/nexus/content/groups/public</url>    
</mirror>  
<mirror>    
	<id>JBossJBPM</id>   
	<mirrorOf>central</mirrorOf>   
	<name>JBossJBPM Repository</name>   
	<url>https://repository.jboss.org/nexus/content/repositories/releases/</url>  
</mirror>
``` 

### shell脚本中获取版本号

``` shell
VERSION=$(mvn -q \
    -Dexec.executable="echo" \
    -Dexec.args='${project.version}' \
    --non-recursive \
    org.codehaus.mojo:exec-maven-plugin:1.3.1:exec)
```

### Maven中Assembly ID must be present and non-empty

今天遇到了这个错误：

``` mvn
Assembly: null is not configured correctly: Assembly ID must be present and non-empty.
```

解决办法在assembly.xml中加上<id></id>标签，加上之后，打好后的包也会加上id标签中的值，这点需要注意
