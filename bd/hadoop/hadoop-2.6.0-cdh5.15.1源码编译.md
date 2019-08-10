#### 1.前期准备工作   

##### 1.1 组件及源码下载

| 组件名称 | 组件版本                          | 下载地址                                                     |
| -------- | --------------------------------- | ------------------------------------------------------------ |
| centos   | centos6.4                         |                                                              |
| jdk      | jdk-7u80-linux-x64.tar.gz         |                                                              |
| maven    | apache-maven-3.6.1-bin.tar.gz     |                                                              |
| scala    | scala2.11.6                       |                                                              |
| hadoop   | Hadoop-2.6.0-cdh5.15.1-src.tar.gz | http://archive.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.15.1-src.tar.gz |

⚠️ 编译该版本源码需要jdk1.7 

##### 1.2 安装必要工具

```shell
# 上传下载
[root@hadoop001 ~]# yum install -y lrzsz
# 下载工具
[root@hadoop001 ~]# yum -y install wget
```

##### 1.3 安装必要的依赖库  

```shell
[root@hadoop001 ~]# yum install -y svn ncurses-devel
[root@hadoop001 ~]# yum install -y gcc gcc-c++ make cmake
[root@hadoop001 ~]# yum install -y openssl openssl-devel svn ncurses-devel zlib-devel libtool
[root@hadoop001 ~]# yum install -y snappy snappy-devel bzip2 bzip2-devel lzo lzo-devel lzop autoconf automake cmake 
```

##### 1.4 上传压缩包并解压缩配置环境

这里没有添加用户，直接用 root 用户

###### 1.4.1 新建目录 

```shell
[root@hadoop001 ~]# mkdir app soft source lib data maven_repo 
```

>  app : 应用安装目录

> soft : 存放待解压缩的安装包

> source : 存放源码包目录

> lib : 需要的外部jar

> data : 测试数据

> maven_repo : maven仓库

###### 1.4.2 将 jdk、maven、scala 上传至 soft 目录 

###### 1.4.3 安装jdk

* 解压缩

```shell
[root@hadoop001 ~]# mkdir /usr/java
[root@hadoop001 ~]# tar -zxvf /home/hadoop/soft/jdk-7u80-linux-x64.tar.gz -C /usr/java
```

* 配置环境变量

```shell
[root@hadoop001 ~]# vi ~/.bash_profile
# jdk1.7
export JAVA_HOME=/usr/java/jdk1.7.0_80
export PATH=$JAVA_HOME/bin:$PATH
[root@hadoop001 ~]# source ~/.bash_profile
# 验证
[root@hadoop001 ~]# java -version 
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
```

###### 1.4.4 安装maven

* 解压缩

```shell
[root@hadoop001 ~]# tar -zxvf /home/hadoop/soft/apache-maven-3.6.1-bin.tar.gz -C /home/hadoop/app
```

* 配置环境变量

```shell
[root@hadoop001 ~]# vi ~/.bash_profile
export MAVEN_HOME=/home/hadoop/app/apache-maven-3.3.9
# maven  注意MAVEN_OPTS设置了maven运行的内存，防止内存太小导致编译失败
export MAVEN_HOME=/home/hadoop/app/apache-maven-3.6.1
export MAVEN_OPTS="-Xms1024m -Xmx1024m"
export PATH=$MAVEN_HOME/bin:$PATH
[root@hadoop001 ~]# source ~/.bash_profile
# 修改配置
[root@hadoop001 ~]# vim /home/hadoop/app/apache-maven-3.6.1/conf/settings.xml
#配置maven的本地仓库位置
<localRepository>/home/hadoop/maven_repo/repo</localRepository>
#添加阿里云中央仓库地址，注意一定要写在<mirrors></mirrors>之间
<mirror>
     <id>nexus-aliyun</id>
     <mirrorOf>central</mirrorOf>
     <name>Nexus aliyun</name>
     <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
# 验证 
[root@hadoop001 ~]# mvn -version
Apache Maven 3.6.1 (d66c9c0b3152b2e69ee9bac180bb8fcc8e6af555; 2019-04-05T03:00:29+08:00)
Maven home: /home/hadoop/app/apache-maven-3.6.1
Java version: 1.7.0_80, vendor: Oracle Corporation, runtime: /usr/java/jdk1.7.0_80/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "linux", version: "2.6.32-642.6.1.el6.x86_64", arch: "amd64", family: "unix"
```

➡️ 如果网速不给力的话，可以提前将需要的jar下载放到maven库中，以防由于网速问题导致编译过慢

###### 1.4.5 安装scala 

* 解压缩

```shell
[root@hadoop001 ~]# tar -zxvf /home/hadoop/soft/scala-2.11.6.tgz -C /home/hadoop/app
```

* 配置环境变量

```shell
[root@hadoop001 ~]# vi ~/.bash_profile
# scala 
export SCALA_HOME=/home/hadoop/app/scala-2.11.6
export PATH=$SCALA_HOME/bin:$PATH
[root@hadoop001 ~]# source ~/.bash_profile
# 验证
[root@hadoop001 ~]# scala -version
Scala code runner version 2.11.6 -- Copyright 2002-2013, LAMP/EPFL
```

#### 2.编译Hadoop源码

##### 2.1 解压 hadoop 源码并编译

* 解压缩

```shell
[root@hadoop001 ~]# tar -zxvf /home/hadoop/soft/hadoop-2.6.0-cdh5.15.1 -C /home/hadoop/source/
```

* 编译

```shell
[root@hadoop001 ~]#cd /home/hadoop/source/hadoop-2.6.0-cdh5.15.1/
# 第一次编译比较耗时
# (使用香港服务器 编译完成耗时25min 前提是已经下载好一部分jar放到maven库且没有计算中间失败的时间)
[root@hadoop001 hadoop-2.6.0-cdh5.15.1]#mvn clean package -Pdist,native -DskipTests -Dtar
# 中间会出现各种包或者文件下载中断、失败的问题
# 这种问题的解决方案就是仔细查看报错信息 然后使用wget手动下载对应jar包放到对应的maven库中即可
```

出现下图示样，即表示编译成功

![hadoop-src-compiler-success](/Users/cll/image/hadoop-src-compiler-success.png)

##### 2.2 验证  
###### 2.2.1 配置ssh

**说明** hadoop的进程之间同信使用ssh方式，需要每次都要输入密码。为了实现自动化操作，需要配置ssh免密码登陆方式。 

- 主节点配置： 

> 首先到用户主目录（cd  ~），ls  -a查看文件，其中一个为“.ssh”，该文件价是存放密钥的。待会我们生成的密钥都会放到这个文件夹中。 

```shell
[root@hadoop001 ~]#cd ~
[root@hadoop001 ~]#ls -a
```



> 现在执行命令生成密钥： ssh-keygen -t rsa -P ""  (使用rsa加密方式生成密钥)回车后，会提示三次输入信息，我们直接回车即可。 

```shell
[root@hadoop001 ~]#ssh-keygen -t rsa -P ""
```



> 进入文件夹cd  .ssh (进入文件夹后可以执行ls  -a 查看文件)  

```shell
[root@hadoop001 ~]#cd .ssh
[root@hadoop001 ~]#ls -a 
```



> 将生成的公钥id_rsa.pub 内容追加到authorized_keys（执行命令：cat id_rsa.pub >> authorized_keys） 

```shell
[root@hadoop001 ~]#cat id_rsa.pub >> authorized_keys
```

- 从节点配置： 

以同样的方式生成秘钥（ssh-keygen -t rsa -P "" ），然后s1和s2将生成的id_rsa.pub公钥追加到m1的authorized_keys中） 

在s1中执行命令：scp id_rsa.pub m1:/root/.ssh/id_rsa.pub.s1 ，在s2中执行命令：scp id_rsa.pub m1:/root/.ssh/id_rsa.pub.s2 

进入m1执行命令：cat id_rsa.pub.s1 >> authorized_keys ，cat id_rsa.pub.s1 >> authorized_keys  

最后将生成的包含三个节点的秘钥的authorized_keys 复制到s1和s2的.ssh目录下（ scp authorized_keys s1:/root/.ssh/， scp authorized_keys s2:/root/.ssh/） 

- 验证ssh免密码登录 

输入命令ssh  localhost(主机名) 根据提示输入“yes”  

输入命令exit注销（Logout） 

再次输入命令ssh localhost即可直接登录 

