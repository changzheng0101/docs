# 什么是bash脚本

可以将bash脚本看成一堆命令的集合，可以一起执行

## 运行bash

```bash
# 使用shell来执行
sh hello.sh

# 使用bash来执行
bash hello.sh

# 使用.来执行
. ./hello.sh

# 使用source来执行
source hello.sh

# 还可以赋予脚本所有者执行权限，允许该用户执行该脚本
chmod u+rx hello.sh
./hello.sh
```

## 常见脚本

```bash
#!/bin/bash
echo "hello,shiyanlou1"

echo "My Fist Script"
```

使用变量

```bash
#!/bin/bash
hello="hello,shiyanlou2"
echo $hello
```

接收外界参数

```bash
#!/bin/bash 输出第一个参数
echo $1
```

```bash
#!/bin/bash
function hello {
  echo "hello,shiyanlou4"
}

$1

# 运行： bash test.sh hello --->传入后相当于执行了hello函数
```

### 接收参数

```bash
#!/bin/bash

echo hello,my name is changzhzeng
echo
echo what,is your name?
read nameinput
echo
echo hello $nameinput
```

> 如果想让命令执行得使用``来完成。

```bash
#!/bin/bash

a=`hostname`
echo hello,my name is $a
echo
echo what,is your name?
read nameinput
echo
echo hello $nameinput
```

### ifelse

```bash
#!/bin/bash

count=100
if [ $count -eq 100 ]
then
	echo count is 100
else
	echo ocunt is not 100
fi
```

* if关键字之要有空格
* `[]`后面都需要有一个空格

### 清楚某个文件内容

```bash
#!/bin/bash

# 初始化一个变量
LOG_DIR=/var/log

cd $LOG_DIR

# /dev/null 是一个空文件
cat /dev/null > dpkg.log

echo "Logs cleaned up."

exit
```

## 判断是否存在某个文件

```bash
#!/bin/bash
echo hello; echo there
filename=ttt.sh
if [ -e "$filename" ]
then    # 注意: "if"和"then"需要分隔，-e用于判断文件是否存在
    echo "File $filename exists."; cp $filename $filename.bak
else
    echo "File $filename not found."; touch $filename
fi; echo "File test complete."
```

注意：

* [ -e "$filename" ]，在`[]`后面都有一个空格，没这个空格不行
* `if else`之间的语句要写在一行
* if关键字之后也需要有一个空格

### case语句

```bash
#!/bin/bash

varname=b

case "$varname" in
    [a-z]) echo "abc";;
    [0-9]) echo "123";;
esac
```

注意:

* `;;`代表case结束，需要和`)`一起使用
* `esac`代表case结束

### 命令替换

反引号中的命令会优先执行，如：

```bash
cp `mkdir back` test.sh back
ls
```

先创建了 back 目录，然后复制 test.sh 到 back 目录。



### while语句

```bash
#!/bin/bash

while :
do
    echo "endless loop"
done

while true
do
    echo "endless loop"
done
```

注意： `while`后面需要有空格

### 常用符号

* \> 输入

* \>\>追加输入

* ? 为三元操作符

* ```bash
   #!/bin/bash
    
   a=10
   (( t=a<50?8:9 )) # 这里两个(()) 没有好像不行
   echo $t
  ```

### 数组

```bash
#!/bin/bash

arr=(1 4 5 7 9 21)
echo ${arr[3]} # get a value of arr
```

### 局部变量

```bash
#!/bin/bash

a=123
( a=321; )

echo "$a" #a的值为123而不是321，因为括号将判断为局部变量
```





### Zabbix监控流程

- （1）添加要监控的 Agent（这一步骤在上一个实验中已经完成，如果你没有使用保存的环境，就需要重新进行配置）。
- （2）给主机添加监控项，即我们想要监控的指标。
- （3）将我们监控项按组划分为两个图形。
- （4）将图形汇总到一个聚合图形上。