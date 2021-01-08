# [ACTF2020 新生赛]BackupFile  
TAG:PHP、备份文件泄露、源代码审计     
## 解题过程：
![20210104111824](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104111824.png)  
尝试寻找备份文件，发现为```index.php.bak```,下载后进行代码审计  
![20210104114205](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104114205.png)  
![20210104114232](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104114232.png)  
发现需要通过参数key传递数据获得flag  
利用PHP弱类型比较的"特性"，向is_numeric()函数传递```key=123```即可完成比较检查获得flag  
![20210104114123](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104114123.png)  
## 题目分析：
此题主要展示了PHP弱类型比较的"特性"带来的问题  
在此题中，int和string是无法直接比较的，php会将string转换成int然后再进行比较，转换成int比较时只保留数字，第一个字符串之后的所有内容会被截掉，这样就可以只传递```key=123```完成比较检查了  
