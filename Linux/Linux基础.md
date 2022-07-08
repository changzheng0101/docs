# 历史记录

在进入`~`目录下之后:hand:,一般存储在`.bash_history`文件中，可以用`cat`命令查看

`!!`上一条命令

`!+number` 

* !7 运行第七条命令
* !-7 运行倒数第七条命令

`!+xxx`搜索xx开头的命令，`!+xxx:p`会将要执行的命令打印出来

## 历史记录行为:taco:

假设有一些命令，在输入之后不希望history进行保存，例如一些涉及保密的操作。

`echo $HISTCONTROL`查看当前行为，在不同的Linux发行版上会有不同

* HISTCONTROL=ignoredups      重复的命令history只记录一次
* HISTCONTROL=ignorespace     在命令前面添加空格，该命令不会被记录

* HISTCONTROL=ignoreboth      结合以上二者行为

>这次更改是暂时的更改，重启之后又会变为默认的行为，如果想要永远的完成更改，需要对`.bashrc`进行更改，简单方法`echo HISTCONTROL=ignoreboth >> .bashrc`

# 文件系统

![Linux文件系统](md_img/Linux基础/4-1.png)

# 常用命令

## wc

```bash
wc /etc/passwd
# 行数 单词数 字符数 
# 分开数
wc -l /etc/passwd
wc -w /etc/passwd
wc -c /etc/passwd
```

# 重定向

将在命令行中输出的结果保存到一个文件中。

>通常文件会有一定stdin(0)用于输入命令，之后会有stdout(1)和stderror(2)用来输出错误。

## 将命令进行输入

```bash
# 将ls -l输出的结果写入 test.txt
ls -l > test.txt
```

`>`会覆盖之前的内容，如果需要追加写入则需要使用`>>`

```bash
ls -l >> test.txt
```

## 错误输出进行写入

在命令行执行某些命令时，可能会报错，例如随便输入一条命令，`asdf`，会有错误输出`-bash: asdf: command not found`

如果执行`asdf > test.txt`,会发现错误的输出直接在命令行输出，并没有输入到test.txt文件中。这是因为`>`操作默认只输出stdout，如果想要将错误数据输入test.txt，要将命令改为

```bash
asdf 2> test.txt
```

与stdout同理，追加写入命令为

```bash
asdf 2>> test.txt
```

## 正常输出和错误一起写入

在执行某些命令的时候，可能部分正确执行，部分报错。

例如：`cat a.txt b.txt`,a.txt文件存在，可以正常输出里面的内容，b.txt不存在

如果想将正常输出的内容写入output.txt，将错误输出的内容写入error.txt

```bash
cat a.txt b.txt > output.txt 2> error.txt
```

如果想将所有的结果都写入output.txt

```bash
cat a.txt b.txt > output.txt 2>&1
```

# 文件名搜索

>find命令：对文件名进行搜索

用`man find`来查看帮助手册，比较多，直接看EXAMPLES可以看到一些简单的示例。

```bash
# 在当前目录中找test，必须全字匹配
find . -name test
# i代表忽略大小写
find . -iname test
# 使用正则
find . -iname test*
# 增加文件类型
find . -type f -iname test*
# 对搜索出来的每个文件执行某个操作
find .  -type f -exec cat {} \;
# 交互式执行命令
find .  -type f -ok cat {} \;
```

## 内容搜索

主要是依靠grep命令

```bash
# 在某个文件内搜索
grep test test.txt
# 忽略大小写
grep -i Test test.txt
# 前三行 后四行
grep -A 4 -B 3 Test test.txt
# 在某个文件夹下搜索所有文件
grep -r test ./ 
```

