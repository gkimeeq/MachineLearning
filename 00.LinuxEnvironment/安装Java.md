1.下载Java包

下载的是1.7版本的Java。[https://www.oracle.com/technetwork/java/javase/archive-139210.html](https://www.oracle.com/technetwork/java/javase/archive-139210.html)。

2.安装

```
#tar -zxf jdk-xxxx.tar.gz
#mkdir /usr/local/java
#mv jdk1.7.0_80 /usr/local/java
#vi /etc/profile
```

在`profile`文件中添加如下内容：

```
JAVA_HOME=/usr/local/java/jdk1.7.0_80/
JRE_HOME=/usr/local/java/jdk1.7.0_80/jre
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export JAVA_HOME JRE_HOME PATH CLASSPATH
```

启用及查看版本：

```
#source /etc/profile
#java -version
```
