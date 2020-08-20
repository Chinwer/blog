+++

author = "邱圆辉"

title = "Centos下hadoop, hive本地模式安装总结"

date = "2020-08-20"

description = "hadoop, hive的安装总结"

tags = [
    "hadoop", "hive", "installation"
]

images = ["http://47.94.16.255/images/images/2020/08/09/pexels-photo-531321.jpg"]

+++

### Hadoop 2.6.0 的安装

只是简单的总结一下安装步骤，每一步里的具体操作就懒得记录了，网上一搜一大堆。

1. 从 [官网](https://hadoop.apache.org/releases.html) 下载对应版本.tar.gz压缩包，注意是源码，不要下载带 **bin** 字样的包。

2. 运行 `tar -zxvf hadoop.tar.gz` 解压，这里假设解压在用户 qiu 的家目录，因此hadoop的主目录路径为：`/home/qiu/hadoop`。

3. 设置 HADOOP\_HOME 环境变量：

   这里因为我用的shell环境是zsh，所以编辑的是 `~/.zshrc` 文件，如果shell环境是默认的bash，就编辑`~/.bashrc`就好了。

   ```bash
   vim ~/.zshrc
   export HADOOP_HOME=~/hadoop
   source ~/.zshrc
   ```

4. 把hadoop添加至PATH：

   ```bash
   vim ~/.zshrc
   export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
   source ~/.zshrc
   ```

至此，hadoop的本地模式就算配置好了，很简单的。



### Hive 1.2.2 的安装

同上，只记录一下几个步骤：

1. 从 [hive清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/apache/hive/) 下载对应版本的编译好的压缩包，注意是带 **bin** 字样的。

2. 同hadoop安装的第二步，同时也假设安装在家目录。

3. 设置 HIVE\_HOME 环境变量：

   ```bash
   vim ~/.zshrc
   export HIVE_HOME=~/hive
   source ~/.zshrc
   ```

4. 把hive添加至PATH：

   ```bash
   vim ~/.zshrc
   export PATH=$PATH:$HIVE_HOME
   source ~/.zshrc
   ```

5. 配置 hive-site.xml

   ```bash
   cd $HIVE_HOME/conf
   vim hive-site.xml
   ```

   如果没有 hive-site.xml，就自己新建一个，然后在hive主目录下新建一个名叫 **warehouse** 的目录。

   然后把下面的内容复制进去：（这里使用的数据库是hive默认的derby，如果需要使用其他关系型数据库比如mysql，自己google吧）

   ```xml
   <configuration>                                                                    
           <property>                                                                 
                   <name> hive.metastore.warehouse.dir</name>                         
                   <value>/home/qiu/hive/warehouse</value>                     
           </property>                                                                
                                                     
           <property>                                                                 
                   <name>javax.jdo.option.ConnectionDriverName</name>                 
                   <value>org.apache.derby.jdbc.EmbeddedDriver</value>                
                   <description>Driver class name for a JDBC metastore</description>  
           </property>                                                                
   </configuration>                                                                   
   ```

   注意把上面 `hive.metastore.warehouse.dir` 的路径换成自己的hive路径。

6. 配置 hive-env.sh

   ```bash
   cd $HIVE_HOME/conf
   vim hive-env.sh
   ```

   同样，如果没有这个文件就自己新建一个（不过反正就算不存在的话vim也会自动创建一个）

   找到里面 `export HADOOP_HOME=xxx` 这一行，把后面的路径换成hadoop的安装路径：

   ```bash
   export HADOOP_HOME=/home/qiu/hadoop
   ```

到这里，hive本地模式也算配置好了。

但是我在配置好这些之后运行 `hive` 会报错：

 **Found class jline.Terminal, but interface was expected**，

google后发现是因为hive里jline.jar这个包与hadoop的yarn中的jline.jar版本不一致，把前者复制到后者所在目录并把后者覆盖就行：

```
cp $HIVE_HOME/lib/jline-2.12.jar $HADOOP_HOME/share/hadoop/yarn/lib
rm $HADOOP_HOME/share/hadoop/yarn/lib/jline-0.9.94.jar
```

这里注意把jline的版本替换成自己的版本。