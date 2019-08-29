自从今年Github支持免费的个人私有仓库以后，github越来越成为自己日常学习、写作和开发必不可少的工具了。
之前因为github的私有仓库收费，不得已使用了gitee和bitbucket等工具。事实证明，这个世界，只需要一个Github。
下面就一步步带你搭建一下自己的Maven公共仓库。
本地deploy
在自己本地创建一个目录，来放自己deploy的文件，然后在自己的项目目录下执行如下Maven命令：
mvn deploy -DaltDeploymentRepository=ijiangtao-net-repo::default::file:/1tool/develop/maven/repository/ijiangtao-net-repo/repository

然后你会看到自己放本地deploy的内容目录下面已经生成好了依赖所需的所有文件。
这里需要特别说明的是这3个文件：

maven-metadata.xml
maven-metadata.xml.md5
maven-metadata.xml.sha1

其中md5, sha1是校验文件，它们用来保证这个metadata文件的完整性。
Maven在编绎项目时，会先尝试请求maven-metadata.xml，如果没有找到，则会尝试请求到jar文件，在下载jar文件时也会尝试下载jar的md5, sha1文件。
下面就是我本地deploy生成的文件：
maven-metadata.xml
maven-metadata.xml.md5
maven-metadata.xml.sha1
tao-util-core-1.1.01.01-20190420.080520-1.jar
tao-util-core-1.1.01.01-20190420.080520-1.jar.md5
tao-util-core-1.1.01.01-20190420.080520-1.jar.sha1
tao-util-core-1.1.01.01-20190420.080520-1.pom
tao-util-core-1.1.01.01-20190420.080520-1.pom.md5
tao-util-core-1.1.01.01-20190420.080520-1.pom.sha1

这个maven-metadata.xml文件很重要，如果没有这个文件来指明最新的jar版本，那么即使远程仓库里的jar更新了版本，本地maven编绎时用上-U参数，也不会拉取到最新的jar。
所以并不能简单地把jar包放到github上就完事了，一定要先在本地deploy，生成maven-metadata.xml文件，并上传到github上。
上传到github
这一步比较简单，只要用过github的都没什么难度。
首先在GitHub上创建仓库，然后把本地deploy的文件所属的目录设置为git仓库，最后push到远程即可。
项目配置依赖
除了要把你的依赖坐标放到dependencies标签中之外，还需要在pom.xml中指定你的repositories的url。
<dependency>
    <groupId>net.ijiangtao.tech.util</groupId>
    <artifactId>tao-util-core</artifactId>
    <version>1.1.01.01-SNAPSHOT</version>
</dependency>

<repositories>
    <repository>
        <!-- A unique identifier for a repository. -->
        <id>ijiangtao-net-repo</id>
        <!--Human readable name of the repository. -->
        <name>ijiangtao.net repository</name>
        <url>https://raw.githubusercontent.com/ijiangtao/ijiangtao-net-repo/master/repository</url>
        <releases>
            <enabled>true</enabled>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </snapshots>
    </repository>
</repositories>

这样你就可以下载远程仓库的依赖了。
当你执行mvn install -U的时候，会看到下面的Downloading提示：
Downloading from alimaven: http://maven.aliyun.com/nexus/content/groups/public/net/ijiangtao/tech/util/tao-util/1.1.01.01-SNAPSHOT/maven-metadata.xml
Downloading from xxxx: https://github.com/xxxx/xxxx-repo/raw/master/releases/net/ijiangtao/tech/util/tao-util/1.1.01.01-SNAPSHOT/maven-metadata.xml
Downloading from yyyy: http://yyyy.yyyy.cn/nexus/content/groups/public/net/ijiangtao/tech/util/tao-util/1.1.01.01-SNAPSHOT/maven-metadata.xml
Downloading from sonatype: https://oss.sonatype.org/content/repositories/snapshots/net/ijiangtao/tech/util/tao-util/1.1.01.01-SNAPSHOT/maven-metadata.xml
Downloading from nexus-aliyun: http://maven.aliyun.com/nexus/content/groups/public/net/ijiangtao/tech/util/tao-util/1.1.01.01-SNAPSHOT/maven-metadata.xml
Downloading from ijiangtao-net-repo: https://raw.githubusercontent.com/ijiangtao/ijiangtao-net-repo/master/repository/net/ijiangtao/tech/util/tao-util/1.1.01.01-SNAPSHOT/maven-metadata.xml

这是Maven在你配置的多个仓库中依次下载依赖，因此如果配置的仓库太多的话，下载速度会受到影响。
上文中提到的仓库地址和示例代码，可以访问如下两个仓库地址：

https://github.com/ijiangtao/ijiangtao-net-repo
https://github.com/ispringboot/demos/tree/master/demo-maven-repo

作者：西召
链接：https://www.jianshu.com/p/4894cc8075ac
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。
