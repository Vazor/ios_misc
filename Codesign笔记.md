# Code Signing

<!--by Jeff-->

1. **数字签名**：对指定信息使用哈希算法，得到一个固定长度的信息摘要，然后再使用 私钥 （注意必须是私钥）对该摘要加密，就得到了数字签名。所谓的代码签名就是这个意思。

2. **证书生成**：开发者在申请iOS开发证书时，需要通过keychain生成一个CSR文件（Certificate Signing Request），提交给苹果的 Apple Worldwide Developer Relations Certification Authority(WWDR)证书认证中心进行签名，最后从苹果官网下载并安装使用。这个过程中还会产生一个 <u>供开发者使用的私钥</u>。

## 数字证书内容：
主要包含2块内容：

|数字证书|  
|:-:|
|证书本身|
|数字签名|

## 证书的生成：

主要是需要Apple帮助开发者对开发者的 <u>证书本身</u> 进行 **数字签名**

|证书本身|
|:-:|
|用户的公钥|
|用户的姓名|
|证书机构名称|
|证书有效期|

[证书本身]   --哈希算法-->   [信息摘要]   --Apple**私钥**加密-->   [数字签名]

## 证书的使用

iOS系统原本就持有WWDR的公钥。iOS验证证书方式如下：

```
[证书本身] --哈希算法--> [信息摘要1]
[数字签名] --Apple公钥解密--> [信息摘要2]  
[信息摘要1] == [信息摘要2] ? 验证通过 ：验证失败
```

**证书存在的意义**：通过证书使用过程可以看出，证书本身只是一个中间媒介，iOS系统对证书并不关心，它其实只想要证书中包含的开发者的公钥！！

## 签名相关命令
### 快捷查看系统中能用来对代码进行签名的证书

```
________________________________________________________________________________
| ~ @ Jeff-MBP15 (jeff) 
| => security find-identity -v -p codesigning 
  1) A5233C921DF69B4705D9BFC03B2991D705244037 "iPhone Developer: Vazor Wang (R29E849SVQ)"
     1 valid identities found
________________________________________________________________________________
```
这就说明当前有一个同时有公钥和私钥的可用证书。

### 对未签名app手动签名
```
________________________________________________________________________________
| ~ @ Jeff-MBP15 (jeff) 
| => codesign -s 'iPhone Developer: Vazor Wang (R29E849SVQ)' ~/Downloads/WdmCrashAnalyser_1.4.0.app/
________________________________________________________________________________
```

### 对已签名app重新签名
为了重新设置签名，你必须带上 -f 参数，有了这个参数，codesign 会用你选择的签名替换掉已经存在的那一个：
```
codesign -f -s 'iPhone Developer: Vazor Wang (R29E849SVQ)' ~/Downloads/WdmCrashAnalyser_1.4.0.app
```
### 查看指定app的签名信息

codesign 还可以为你提供有关一个可执行文件签名状态的信息，这些信息在出现不明错误时会提供巨大的帮助：

```
________________________________________________________________________________
| ~ @ Jeff-MBP15 (jeff) 
| => codesign -vv -d ~/Downloads/WdmCrashAnalyser_1.4.0.app/
Executable=/Users/jeff/Downloads/WdmCrashAnalyser_1.4.0.app/Contents/MacOS/WdmCrashAnalyser
Identifier=com.xiaowei..WdmCrashAnalyser
Format=bundle with Mach-O thin (x86_64)
CodeDirectory v=20200 size=753 flags=0x0(none) hashes=30+3 location=embedded
Signature size=4349
Authority=iPhone Developer: Vazor Wang (R29E849SVQ)
Authority=Apple Worldwide Developer Relations Certification Authority
Authority=Apple Root CA
Signed Time=2015年9月27日 下午7:39:33
Info.plist entries=23
TeamIdentifier=B4Q62TFS5S
Sealed Resources version=2 rules=12 files=6
Internal requirements count=1 size=192
________________________________________________________________________________
```
### 验证签名文件的完整性

检查已签名的文件是否完整可以使用如下命令：

```
________________________________________________________________________________
| ~ @ Jeff-MBP15 (jeff) 
| => codesign --verify ~/Downloads/WdmCrashAnalyser_1.4.0.app/
________________________________________________________________________________
```
没有任何输出代表签名是完好的。如果修改一下这个二进制文件：

```
________________________________________________________________________________
| ~ @ Jeff-MBP15 (jeff) 
| => echo 'jeff' >> ~/Downloads/WdmCrashAnalyser_1.4.0.app/Contents/screw
________________________________________________________________________________
| ~ @ Jeff-MBP15 (jeff) 
| => codesign --verify ~/Downloads/WdmCrashAnalyser_1.4.0.app/
/Users/jeff/Downloads/WdmCrashAnalyser_1.4.0.app/: a sealed resource is missing or invalid
________________________________________________________________________________
```
和预料中的一样，修改已经签名的应用会导致数字签名验证不通过。改回文件之后，证书验证通过：

```
________________________________________________________________________________
| ~ @ Jeff-MBP15 (jeff) 
| => rm -rf ~/Downloads/WdmCrashAnalyser_1.4.0.app/Contents/screw 
________________________________________________________________________________
| ~ @ Jeff-MBP15 (jeff) 
| => codesign --verify ~/Downloads/WdmCrashAnalyser_1.4.0.app/
________________________________________________________________________________
```

## 资源文件签名
为了达到为所有文件设置签名的目的，签名的过程中会在程序包（xx.app）中新建一个叫做 _CodeSignatue/CodeResources 的文件，这个文件中存储了被签名的程序包中所有文件的签名。它是一个 plist 格式文件。

- 这个列表文件中不光包含了文件和它们的签名的列表，还包含了一系列规则，这些规则决定了哪些资源文件应当被设置签名。
但在新版本中（OS X 10.10 DP 5 和 10.9.5 之后的版本），所有的代码文件和资源文件都必须设置签名，不再可以有例外。
- 在新版本的代码签名规定中，一个程序包中的可执行程序包，例如扩展 (extension)，是一个独立的需要设置签名的个体，在检查签名是否完整时应当被单独对待。

## 授权文件（entitlements）
在 iOS 上你的应用能做什么依然是沙盒限制的，这些限制大多情况下都由授权文件（entitlements）来决定。授权机制决定了哪些系统资源在什么情况下允许被一个应用使用，简单的说它就是一个沙盒的配置列表。查看命令：

`codesign -d --entitlements - ~/Downloads/WdmCrashAnalyser_1.4.0.app` 

在 Xcode 的 Capabilities 选项卡下选择一些选项之后，Xcode 就会自动生成一个 .entitlements 的XML文件，然后在需要的时候往里面添加条目。

- 当构建整个应用时，这个文件也会提交给 codesign 作为应用所需要拥有哪些授权的参考。这些授权信息必须都在开发者中心的 App ID 中启用，并且包含在后文介绍的描述文件中。在构建应用时需要使用的授权文件可以在 Xcode build setting 中的 code signing entitlements中设置。
- 在新版本的 Xcode 6 之后，授权信息列表会以 Example.app.xcent 这样的名字的文件形式包含在应用包中。这么做或许是为了在出现配置错误时提供更加有用的错误信息。

## 描述文件（provisioning file）
在整个代码签名和沙盒机制中有一个组成部分将被签名。授权和沙盒联系了起来，这就是 **描述文件**。

Xcode 将从开发者中心下载的全部配置文件都放在了：

`~/Library/MobileDevice/Provisioning Profiles`

描述文件并不是一个普通的plist文件，它是一个根据密码讯息语法 (Cryptographic Message Syntax) 加密的文件。
以XML格式查看该文件的命令：

`security cms -D -i example.mobileprovision`

### 描述文件主要包含以下内容
- **UUID**：每一个配置文件都有它自己的 UUID 。Xcode 会用这个 UUID 来作为标识，记录你在 build settings 中选择了哪一个配置文件。
- **ProvisionedDevices**：记录所有可用于调试的设备ID。
- **DeveloperCertificates**：包含了可以为使用这个配置文件的应用签名的所有证书。所有的证书都是基于 Base64 编码符合 PEM (Privacy Enhanced Mail, RFC 1848) 格式的。
- **Entitlements**：有关前面讲到的配置文件的所有内容都会被保存在这里。

---
*参考：<http://www.cocoachina.com/ios/20141017/9949.html>  
参考：<http://objccn.io/issue-17-2>*