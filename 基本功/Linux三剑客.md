[TOC]

# grep

## 定义
根据用户指定的模式（pattern）对目标文本进行过滤，显示被模式匹配到的行

## 命令形式

```shell
grep [OPTIONS] PATTERN [FILES]
```

- -v 显示不被pattern匹配到的行

- -i 忽略字符大小写

- -n 显示匹配的行号

- -c 统计匹配的行数

- -o 仅显示匹配到的字符串

- -E 使用ERE，相当于egrep

## 实战
```shell
# 查找文件包含root的行数
> grep -n root /etc/passwd

# 查找文件内容不包含root的行
> grep -nv root /ets/passwd

# 查找以s开头的行
> grep -n ^s /etc/passwd

# 查找以n结尾的行
> grep -n n$ /etc/passwd

# 查找以s开头，以n结尾的行
> grep -n ^s[a-zA-Z]*n$ /etc/passwd
> grep -n ^s\\w*n$ /etc/passwd
```

# TODO

```shell
grep --help
```

# sed

## 定义

sed是流编辑器，一次处理一行内容，先将一行内容读取到模式空间，处理完成后输出到屏幕，然后清空模式空间，再次读入新的行，并没有直接修改源数据

![image-20201208222647475](https://gitee.com/bye-oldtime/pictures/raw/markdown/20201208222648.png)

## 命令形式

```shell
sed [-hn..][-e<script>][-f<script FILE>][FILE]
```

- 基本参数

  - -h 显示帮组

  - -n 仅显示script处理后的结果

- 脚本参数

  - -e\<script> 以选项中指定的script来处理输入的文本文件
  - -f\<script文件> 以选项中指定的script文件来处理输入的文本文件

### 脚本常用动作

| 指令 | 说明 | 示例                 | 说明|
| ---- | ---- | -------------------- |--------|
| a    | 新增 | sed -e '4 a newline' |在第4行后，新增一行，内容为“newline”|
| c    | 取代 | sed -e '2,5 c No 2-5 number' | 用c后面的内容取代2-5行的内容 |
| d    | 删除 | sed -e '2,5 d' | 删除2-5行 |
| i    | 插入 | sed -e '2 i newline' | 在第2行前，新增一行，内容为“newline” |
| p    | 打印 | sed -n '/root/p' | 打印匹配root的内容 //之前的内容是正则表达式 |
| s    | 取代 | sed -e 's/old/new/g' | 用new替代old的内容 /g全局 |

## 实战

```shell
# 查看test.txt内容
> cat test.txt
root admin root huyp
newn
root
new
root
snew
soon

# 查看test.txt内容,发现并未修改源文件内容
> cat test.txt
root admin root huyp
newn
root
new
root
snew
soon
# 在第四行后面增加新字符串
> sed -e '4 a newline' test.txt
root admin root huyp
newn
root
new
newline
root
snew
soon

# 在第四行前面增加新字符串
> sed -e '4 i newline' test.txt


# 全局替换
# 未加g参数，只替换了每行第一个匹配项
> sed -e 's/root/hello/' test.txt
hello admin root huyp
newn
hello
new
hello
snew
soon

# 加g参数，替换每行中所有的匹配项
> sed -e 's/root/hello/g' test.txt
hello admin hello huyp
newn
hello
new
hello
snew
soon

# 直接修改文件内容,比较危险，建议先确认结果后再加-i参数
> sed -i 's/root/hello/g' test.txt
> cat test.txt
hello admin hello huyp
newn
hello
new
hello
snew
soon
```

# TODO

```shell
sed -h
```



# awk

## 定义
以换行符为默认记录分隔符，逐一读取记录，以空格为默认分隔符将每条记录切片，切开的部分再进行后续处理

![image-20201208225254651](https://gitee.com/bye-oldtime/pictures/raw/markdown/20201208225256.png)

## 命令形式

```shell
awk 'pattern + action'[FILE]
```

- pattern 正则表达式，以/开头，以/结尾

- action 对匹配的内容执行的命令，默认为输出每行内容

###  常用参数

- FILENAME awk浏览的文件名
- BEGIN 处理文本之前要执行的操作
- END 处理文本之后要执行的操作
- FS 设置输入域分隔符，等价于命令行-F选项
- NF 浏览记录的域的个数（列数）
- NR 已读的记录数（行数）
- OFS 输出域分隔符
- ORS 输出记录分割符
- RS 控制记录分隔符
- $0 整条记录
- $1 表示当前行的第一个域....以此类推

实战

```shell
# 搜索/etc/passwd有root关键字的所有行，并显示对应的shell
> awk -F : '/root/ {print $7}' /etc/passwd

# 打印/etc/passwd的第二行信息
> awk -F : 'NR==2 {print $0}' /etc/passwd

# 使用begin加入标题BEGIN BEGIN
> awk -F : 'BEGIN{print "BEGIN BEGIN"}{print $1,$2}' /etc/passwd

# 自定义分隔符
> echo "111 222|333 444|555 666" | awk 'BEGIN{RS="|"}{print $0}'
111 222
333 444
555 666

```

