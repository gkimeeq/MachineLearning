1.安装开发工具包

```
#yum groupinstall -y "Development tools"
```

其它必需包：

```
#yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel tcl tcl-devel tk tk-devel
```

2.安装Python-2.7.16

```
#wget https://www.python.org/ftp/python/2.7.16/Python-2.7.16.tgz
#tar -zxf Python-2.7.16.tgz
#cd Python-2.7.16
#./configure --enable-unicode=ucs2 --enable-unicode=ucs4 --with-zlib --with-ssl
#make && make install
```

`--enable-unicode=ucs2 --enable-unicode=ucs4`是为了以后安装tensorflow。

默认python2.7.16安装在`/usr/local/bin`下，系统自带的在`/usr/bin`下。

移除系统默认版本：

```
#mv /usr/bin/python /usr/bin/python.old
```

如果有`python-config`的软件链接，则删除：

```
#rm -f /usr/bin/python-config
```

创建新版本的软链接，从而更新系统默认版本：

```
#ln -s /usr/local/bin/python /usr/bin/python
#ln -s /usr/local/bin/python-config /usr/bin/python-config
#ln -s /usr/local/include/python2.7 /usr/include/python2.7
```

使用新版本的Python后，yum不可用，可以像如下修复：

```
#vi /usr/bin/yum
```

把第一行改为`#!/usr/bin/python2.6`。

这种方法，在升级yum后，又会导致yum不能用，可以通过把yum运行用到的模块从python2.6复制到python2.7下：

```
#cp -r /usr/lib/python2.6/site-packages/yum /usr/local/lib/python2.7/site-packages/
#cp -r /usr/lib64/python2.6/site-packages/rpm /usr/local/lib/python2.7/site-packages/
#cp -r /usr/lib/python2.6/site-packages/urlgrabber /usr/local/lib/python2.7/site-packages/
#cp -r /usr/lib64/python2.6/site-packages/pycurl.so /usr/local/lib/python2.7/site-packages/
#cp -r /usr/lib/python2.6/site-packages/rpmUtils /usr/local/lib/python2.7/site-packages/
#cp -r /usr/lib64/python2.6/site-packages/curl /usr/local/lib/python2.7/site-packages/
#cp -p /usr/lib64/python2.6/site-packages/pycurl.so /usr/local/lib/python2.7/site-packages/
#cp -p /usr/lib64/python2.6/site-packages/_sqlitecache.so /usr/local/lib/python2.7/site-packages/
#cp -p /usr/lib64/python2.6/site-packages/sqlitecachec.py /usr/local/lib/python2.7/site-packages/
#cp -p /usr/lib64/python2.6/site-packages/sqlitecachec.pyc /usr/local/lib/python2.7/site-packages/
#cp -p /usr/lib64/python2.6/site-packages/sqlitecachec.pyo /usr/local/lib/python2.7/site-packages/
```

3.安装pip，virtualenv

安装setuptools：

```
#wget https://bootstrap.pypa.io/ez_setup.py
#python ez_setup.py
```

然后`easy_install`命令被安装到`/usr/local/bin`下，使用它来安装`pip`。

```
#easy_install pip
```

或者这样安装`pip`：

```
#wget https://bootstrap.pypa.io/get-pip.py
#python get-pip.py
#pip -V  # 查看版本
#pip freeze  # 查看当前安装的模块及版本
#pip freeze > requirements.txt  # 把当前环境中所有包及各自的版本的简单列表输出到文件
#pip install -r requirements.txt  # 然后可以重建相同版本的相同包的环境，这可以确保安装、部署和开发者之间的一致性
```

安装`distribute`包：

```
#pip install distribute
```

安装`virtualenv`：

```
#pip install virtualenv
#virtualenv --version
```

`virtualenv`的简单使用：

```
#mkdir test
#cd test
#virtualenv test  # 建立一个虚拟环境
#virtualenv -p /usr/bin/python2.7 test  # 可以指定python解释器
#source test/bin/activate  # 激活虚拟环境
#deactivate  # 停用虚拟环境
```

安装`virtualenvwrapper`，要先确保`virtualenv`已经安装：

```
#pip install virtualenvwrapper
```

`virtualenvwrapper`的简单使用：

```
$cd
$mkdir VirtualEnvs  # 创建虚拟环境管理目录
$find / -name virtualenvwrapper.sh  # 找到virtualenvwrapper.sh的路径，如果不知道在哪，用这个来查找
$export WORKON_HOME=~/VirtualEnvs  # 如果创建前要将环境保存到VirtualEnvs中，则要先设置环境变量WORKON_HOME，再搭建环境
$source /usr/local/bin/virtualenvwrapper.sh  # 运行virtualenvwrapper.sh
$mkvirtualenv test1  # 创建一个虚拟环境
$mkvirtualenv test1 --python=python2.7  # 可以选择一个python解释器
$workon test1  # 虚拟环境上工作
$deactivate  # 停止虚拟环境
$rmvirtualenv test1  # 删除
$lsvirtualenv  # 列出所有环境
$cdvirtualenv  # 切换到当前环境的目录
$cdsitepackages  # 切换到当前环境的site-packages目录
$lssitepackages  # 列出site-packages目录的内容
```

4.安装Python库

```
$export WORKON_HOME=~/VirtualEnvs
$source /usr/local/bin/virtualenvwrapper.sh
$mkvirtualenv Test
$workon Test
$pip install numpy
$pip install pandas
$pip install matplotlib
$pip install scipy
$pip install sklearn
$pip install jieba
$pip install hmmlearn
$pip install gensim
$pip install tensorflow
$pip install tensorlayer
$pip install torch
$pip install torchvision
$pip install moviepy
$pip install IPython
$pip install visdom
$pip install requests
$pip install pymysql
$pip install beautifulsoup4
$pip install lxml  # Beautiful Soup支持Python标准库中的HTML解析器，还支持一些第三方的解析器，如不安装第三方，则用Python默认的解析器。lxml解析器更加强大，速度更快，推荐安装
```

`import tensorflow`时报错：`/lib64/libc.so.6: version 'GLIBC_2.16' not found`，所以要升级GLIBC。

```
#cat /etc/redhat-release
#strings /lib64/libc.so.6 | grep GLIBC_
```

查得CentOS 6.10最高版本的GLIBC是2.12。

（实际升级过程中，2.16版本失败，所以直接升级到2.18）

```
#wget http://ftp.gnu.org/gnu/glibc/glibc-2.18.tar.gz
#tar -zxf glibc-2.18.tar.gz
#mkdir glibc-build-2.18
#cd glibc-build-2.18
#../glibc-2.18/configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin
#make
#make install
```

再查一个版本：

```
#strings /lib64/libc.so.6 | grep GLIBC_
```

可以看到最高版本为2.18了。

```
#ls -al /lib64/libc.so.6
```

链接的版本是2.18。

如果这个过程有报错：`/usr/lib64/libstdc++.so.6: version 'CXXABI_1.3.7' not found`，则要升级gcc。

```
#strings /usr/lib64/libstdc++.so.6 | grep "CXXABI" 
```

查到的最高版本为1.3.3。升级gcc到4.8：

```
#curl -Lks http://www.hop5.in/yum/el6/hop5.repo > /etc/yum.repos.d/hop5.repo
#yum install gcc gcc-g++ -y
#strings /usr/lib64/libstdc++.so.6 | grep "CXXABI" 
```

显示的最高版本为1.3.7。
