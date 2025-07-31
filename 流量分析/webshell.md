
#流量分析

webshell工具。

菜刀基本已经淘汰，没有开源，魔改较少。https://mp.weixin.qq.com/s/SjUMoY6EafQzAb3RNWNkpg

bp只能抓到http和https，

##### 蚁剑：

  特征点：1、UA头       2、Post提交       3、传参的值base64去掉前面2个字符后可以成功解码。正常都是要么解不了，要么乱码 （这么做可以就是防止被解码发现）         4、@ini_set("display_errors", "0");@set_time_limit(0);

![image-20250122111634305](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536756.png)

配好代理，生成后门，连接：

![image-20250122174847934](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536757.png)

webshell一般都是Post提交，因为数据包多，get是传不上这么多的。连接上后做一些操作，观察数据包：

post  我这里ua头好像不是antSword/v2.1，也不知为啥。

![image-20250122180811228](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536758.png)

Base64编码提交的数据（去掉前2位），这也是特征：

![image-20250122181429466](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536759.png)

当连接时候用的为默认时候，这里数据就是ini开始的：

![image-20250122181941604](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536760.png)

##### 冰蝎：

   UA头，有时候会变，但是都是在他的一个库里面进行随机换。	

数据加密方式可能为aes，这种解密是需要密钥或者偏移量来解密。

![image-20250122113008980](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536761.png)

特征1、2、3

![image-20250122182533862](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536762.png)

他的数据包是aes加密的，如果不知道加密密钥(webshell连接密码)，那就解不开数据内容。referer也算个特征，冰蝎是有referer：

![image-20250122183247916](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536763.png)



##### 天蝎：

​       冰蝎的延深

![image-20250122184217903](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536764.png)

3个特征：

![image-20250122184323627](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536765.png)

与冰蝎相比，数据包的加密更换，回显包的加密也更改了：

![image-20250122184558521](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536766.png)

##### 哥斯拉

![image-20250122191422806](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536767.png)

代理还让我配半天，有两个地方需要改：

![image-20250122190102797](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536768.png)

   连接成功会发送3个数据包，失败会发送2个数据包。

成功连接点击success后第3个数据包才出来：

![image-20250122191130097](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536769.png)

特征：

![image-20250122191834352](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536770.png)

![image-20250122191950007](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536771.png)

  蓝队分析工具箱，解密webshell工具的数据包------主要还是要知道密钥

这里使用的是默认密钥：

![image-20250122195912572](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536772.png)

解密返回包，也是判断的方法之一：

![image-20250122200018432](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536773.png)

#### 题型：

  ctf题型：

目的、给了一个wireshark抓包的数据包：

![image-20250122233720077](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536774.png)

首先说了是冰蝎的流量包，那就是http的post请求，先缩小范围：

![image-20250122233754663](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536775.png)

下来需要找ip，那就利用的是冰蝎连接时候会加载phpinfo信息（初始化），这里面就有ip等信息，就需要找这个的流量包：

![image-20250122234308281](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536776.png)

![image-20250122233948720](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536777.png)

将上面加密后的继续解密，得到phpinfo的html代码：

 ![image-20250122234136214](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536778.png)



 第二个   蚁剑

![image-20250122234745882](https://cdn.jsdelivr.net/gh/maybeyjb/blue-team/img/202506161536779.png)

还是找，完了解密时候就注意删前2个字符后再解密。

