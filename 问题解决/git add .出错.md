## git add .出错

## 1.问题出现

在修改了package.json文件之后，使用git add . 提交代码出现以下报错：

```
warning:LF will be replaced by CRLF in package.json.
The file will have its original line endings in your working directory
```

## 2.**错误原因**

windows中的换行符为 CRLF，而在Linux下的换行符为LF，CRLF和LF是不同操作系统上使用的换行符

**错误造成的影响**：Unix/Mac系统下的文件在Windows里打开的话，所有文字会变成一行；而windows里的文件在Unix/Mac下打开的话，在每行的结尾可能会多出一个^M符号。Linux保存的文件windows上用记事本看的话会出现黑点。

所以在不同的系统上都需要进行统一转换

## 3.windows解决办法：

```
git config core.autocrlf false *//禁用自动转换* 
文件提交时进行safecrlf检查：git config –global core.safecrlf true//拒绝提交包含换行符的文件
```
