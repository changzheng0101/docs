# meaven

## 常见标签

### dependency

`<dependencymanagement>`这个注解，里面也会声明依赖，但是这些依赖并未真的以jarr包的形式进入到项目中，一般这个标签用来声明整个项目中用到jar包的版本，一般在parent中使用。

### plugin

指定插件，不同的插件有不同的用途，例如spring相关的插件表明 该项目将会打包为

## meaven的生命周期

* clean **单独的声明周期**，删除`target`文件夹。
* compile 将src目录下的java类进行编译为.class文件
* test 将test目录下的java类编译为.class文件
* package 打包，将文件打包为jar文件
* install  将打包好的jar包发布到本地仓库
* deploy 将打包好的jar包进行远程部署                                                        

>除`clean`之外，后面运行的命令实际上也会运行之前的命令，例如package之前会运行compile和test等。

## 多模块构建

父模块的`package`必须声明为`pom`