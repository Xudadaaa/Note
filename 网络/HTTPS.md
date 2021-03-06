## https 握手过程

<img src="./https.webp"></img>

- 用户在浏览器输入一个 https 网址，连接到服务器的 443 端口。
- 服务器返回给浏览器证书（证书里面包含公钥）
- 浏览器首先会验证公钥是否有效，比如颁发机构，过期时间等等，如果发现异常，则会弹出一个警示框，提示证书存在的问题。如果证书没有问题，那么就生成一个随机值。然后用公钥对这个随机值进行非对称加密。（这个随机值就是以后进行对称加密的公钥）
- 服务器拿私钥解密出对称加密的公钥。然后以后就用这个公钥进行对称加密。
- 浏览器就可以用这个公钥进行解密和加密了。

## 为什么是 ssl 证书是安全的，ssl 解密过程

CA 机构对于服务器的公钥的加密是非对称加密的过程。

CA 机构公开在网络上公钥，以便浏览器用公钥进行解析获得我们的证书信息

证书是否过期，发行服务器证书的 CA 是否可靠，发行者证书的公钥能否正确解开服务器证书的“发行者的数字签名”，服务器证书上的域名是否和服务器的实际域名相匹配。

浏览器拿到证书之后，用公钥进行解析我们的证书信息并验证。。

为什么安全，是因为如果中间人不知道 CA 机构的私钥，所以无法篡改我们的证书里面的内容后再进行加密。加密后的东西就不能用公开在网络上的公钥进行解密了。

---

- http与https的区别：
>http是运行在tcp上，明文传输，客户端与服务器端都无法验证对方的身份。
https是身披SSL外壳的http，运行于ssl上，ssl是运行于tcp上的，是加了加密和认证机制的http。

- 不同：
> 端口不同：前者 80，后者443；
资源消耗：https由于加减密处理消耗更多的cpu和内存资源
开销：https需要证书。

- 对称加密：
> A--(明文传输公钥)-->B；B-(公钥加密的数据)->A(再通过公钥进行解密)
A--(公钥加密的数据)-->B(再通过公钥解密)……;  
这样当C在A传输公钥给B的过程中拦截公钥即可，所以不安全。


- 非对称加密：公钥与私钥，公钥解私密，私钥解公密
> A--(明文传输A公钥)-->B; B--(A公钥加密的数据+B公钥)-->A(再通过A私钥进行解密);  
A--(B公钥加密的数据)-->B(再用B私钥进行解密);
对于私钥都在自己的手上，不会外泄，安全性会高很多，但是加密时间很长，效率很低。
- 非对称加密无法判断是不是我们想找的人：可能信息被修改。
>A本来想跟B互发东西的。  
A--（明文传出A公钥）-->C--(发送自己的C公钥)-->B  
B--(C公钥加密数据)-->C(C私钥解密后，改B发的数据，然后用A的公钥加密)-->A(A私钥解密得到错误信息以及C的公钥)  
A--(C公钥加密信息)-->C(C私钥解密，改数据，用B的公钥加密再发给B，同时附上自己的C公钥)-->B

- 所以有了数字签名的出现。
> A--(A公钥)-->认证机构
认证机构--(证书（包括数字签名和A公钥）)-->A
A--(证书+数据)-->B(拿到后检验证书是否有效即可)
>>中间人无法修改证书：证书的签名是由服务器网址等信息生成的，并且经过机构私钥加密，中间人也无法篡改