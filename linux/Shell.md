#### Shell

脚本执行的方式：

```bash
bash myScript.sh
```

或者

```bash
chmod 755 myScript.sh
./myScript.sh
```

**echo**是用于终端打印的最基本命令。

```bash
echo "Welcome to Bash" #这里的双引号也可以省略，也可以使用单引号，双引号允许shell解释字符串中出现特殊字符，单引号不会对其做任何解释。
```

**printf**功能是和c语言的printf函数功能相似，不过，默认情况下，printf不会自动添加换行符，需要自己手动指定。如：

```bash
printf "%-5s %-10s\n" No Name Mark
```

使用**env**或者**printenv**命令可以查看当前shell所定义的全部环境变量。

使用**pgrep**命令获得进程ID，然后使用cat /proc/进程ID/environ可以查看有关进程的环境,如果想生成更易读的报表，可以配合管道符：如

```bash
cat /proc/2735/environ | tr '\0' '\n'
```

可以使用等号操作符给变量赋值，如下

```bash
a=100
#然后通过$a 或者${a}来访问变量a的内容
```

注意：var = value 不同于var=value,前者是判断是否相等，后者是赋值操作。

###### 获得变量的长度

```bash
$(#var)
```

###### 识别当前使用的shell

```bash
echo $SHELL
#或者 echo $0
```

shift命令可以将参数依次向左移动一个位置，让脚本能够使用$1来访问每一个参数。

$#:表示获取参数的个数

```bash
cat showArg.sh
for i in `seq 1 $#`
do
echo $i is $1
shift
done
sh showArgs.sh a b c
```

可以把输出赋给变量,用子shell或反引用的方法将命令的输出保存到变量中，为了保留输出的空格和换 行符（\n），必须使用双引号

```shell
cmd_out=$(ls | cat -n)#此次将去该命令的执行结果赋给变量
echo $cmd_out
#或者cmd_out=`ls | cat -n`
```

###### read命令

```shell
read -n num var #读取n个字符到变量中
read -s var # 无回显的方式读取密码
read -p "Enter input:" var#显示提示信息
read -t timeout var # 给定时效内读取输入
read -d ":" var #以特定的字符作为输入行的结束
```

持续执行命令直至执行成功

```shell
repeat() 
{ 
 while true 
 do 
 $@ && return 
 done 
}
```

IFS:内部字段分隔符.要迭代一个字符串或逗号分隔型数值（Comma Separated Value，CSV） 中的单词。如果是前者，可以使用IFS=" "；如果是后者，则使用IFS=",".

```shell
data="name, gender,rollno,location"
oldIFS=$IFS 
IFS=, #IFS现在被设置为, 
for item in $data; 
do 
 echo Item: $item 
done 
IFS=$oldIFS 
```

- 面向列表的for循环

  ```shell
  for var in list; 
  do 
   commands; #使用变量$var 
  done
  ```

- 迭代指定范围的数字

  ```shell
  for((i=0;i<10;i++)) 
  { 
   commands; #使用变量$i 
  } 
  
  ```

- 循环到条件满足为止

  ```shell
  while condition 
  do 
   commands; 
  done 
  ```

- until循环

  ```shell
  x=0; 
  until [ $x -eq 9 ]; #条件是[$x -eq 9 ] 
  do 
   let x++; echo $x; 
  done 
  ```

  if和else语句能够嵌套使用。if的条件判断部分可能会变得很长，但可以用 逻辑运算符将它变得简洁一些： 

   [ condition ] && action; # 如果condition为真，则执行action 

   [ condition ] || action; # 如果condition为假，则执行action

-  -gt：大于。

-   -lt：小于。

-   -ge：大于或等于。

-   -le：小于或等于。

-a是逻辑与操作符，-o是逻辑或操作符。可以按照下面的方法结合多个条件进行测试：

 [ $var1 -ne 0 -a $var2 -gt 2 ] #使用逻辑与-a  

 [ $var1 -ne 0 -o $var2 -gt 2 ] #逻辑或-o 



 文件系统相关测试 

我们可以使用不同的条件标志测试各种文件系统相关的属性。

<p> ● [ -f $file_var ]：如果给定的变量包含正常的文件路径或文件名，则返回真。

 ● [ -x $var ]：如果给定的变量包含的文件可执行，则返回真。 

● [ -d $var ]：如果给定的变量包含的是目录，则返回真。 

● [ -e $var ]：如果给定的变量包含的文件存在，则返回真。

 ● [ -c $var ]：如果给定的变量包含的是一个字符设备文件的路径，则返回真。 

● [ -b $var ]：如果给定的变量包含的是一个块设备文件的路径，则返回真。

 ● [ -w $var ]：如果给定的变量包含的文件可写，则返回真。

 ● [ -r $var ]：如果给定的变量包含的文件可读，则返回真。

 ● [ -L $var ]：如果给定的变量包含的是一个符号链接，则返回真。



字符串比较 进行字符串比较时，最好用双中括号，因为有时候采用单个中括号会产生错误



● [[ $str1 = $str2 ]]：当str1等于str2时，返回真。也就是说，str1和str2包 含的文本是一模一样的。

 ● [[ $str1 == $str2 ]]：这是检查字符串是否相同的另一种写法。 

测试两个字符串是否不同。 

● [[ $str1 != $str2 ]]：如果str1和str2不相同，则返回真。 

找出在字母表中靠后的字符串。

 字符串是依据字符的ASCII值进行比较的。例如，A的值是0x41，a的值是0x61。因此，A 小于a，AAa小于Aaa。

 ● [[ $str1 > $str2 ]]：如果str1的字母序比str2大，则返回真。 

● [[ $str1 < $str2 ]]：如果str1的字母序比str2小，则返回真

[[ -z $str1 ]]：如果str1为空串，则返回真。 

● [[ -n $str1 ]]：如果str1不为空串，则返回真。
