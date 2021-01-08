# [极客大挑战 2019]BuyFlag  
TAG:PHP、is_numeric()、strcmp()
## 解题过程：  
题目主页都是废话，点击payflag进入真正的题目页面  
![20210105092825](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105092825.png)  
简单浏览后发现提示，想要获得flag需要是CUIT学生，还需要输入正确的密码  
![20210105092935](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105092935.png)  
但是页面没有可以进行提交的位置，查看页面源代码发现隐藏的post接口，并获得新提示，存在post参数```password```与```money```,传入后台的参数```password```需要能通过```is_numeric()```函数检测为非数字但是仍能表示数字404，传入后台的参数```money```需要等于```100000000```  
![20210105093946](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105093946.png)  
这里需要利用```is_numeric()```函数的"特性",当空字符```%00```放置在要检测的内容前后时，函数都将判断为非数值。在404后添加```%00```绕过```is_numeric()```函数对于数值的检测  
使用hackbar构造post包发送给burpsuite重发器进行测试  
![20210105095021](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105095021.png)  
仍提示需要为学生身份，注意到存在一个```Cookie:user=0```,故将其修改为```Cookie:user=1```后再次发包  
![20210105095159](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105095159.png)  
此时响应结果中显示已经成功的验证了学生身份并输入了正确的密码，但是money参数中的数字太长了，此处没有给出更多提示，需要结合信息分析与猜测   
![20210105095817](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105095817.png)  
从响应包中可以看出，此网站使用的是PHP5.3.3版本，PHP5.3版本存在```strcmp()```函数比较漏洞，可以将参数构造为数组使返回结果为0  
![20210105095325](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105095325.png)  
将```money[]=100000000```传递给后台，成功获取flag  
## 题目分析:  
此题考查了对PHP"特性"的掌握，需要掌握```is_numeric()```函数与```strcmp()```函数的特性才能有较为清晰的解题思路  
本题还有另外的解题方式，即使用科学计数法:```money=1e9```  
![20210105100803](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105100803.png)  
相关函数的具体解析与利用方式请参见：[CTF中常见的PHP“特性”考查点]()
