+++
title = "常用操作"
date = 2020-07-11T22:33:15+08:00
weight = 9
+++

{{% notice note %}}
由于 PIV 通常为系统管理员签发，普通用户使用，因此请查阅文档理解下方内容后再操作。
{{% /notice %}}

## 工具

建议使用 [yubico-piv-tool](https://developers.yubico.com/yubico-piv-tool/Releases/) 执行相关操作。

## 分别导入密钥和证书

如果密钥和证书在两个文件，则需要分别导入。

导入私钥：
```sh
yubico-piv-tool -r canokey -a import-key -s 9a -i private-key.pem
```

导入证书：
```sh
yubico-piv-tool -r canokey -a import-certificate -s 9a -i certificate.pem
```

其中，`-s 9a`表示使用9A密钥槽，可以根据需要进行更改。

## 导入PKCS#12文件

若要导入同时包含私钥和证书的 PKCS#12 文件（.p12 或 .pfx），执行：
```sh
yubico-piv-tool -r canokey -a import-key -a import-certificate -K PKCS12 -s 9a -i certificate.p12
```

## 生成密钥并自签名

生成一个新的私钥并对其自签名：
```sh
yubico-piv-tool -r canokey -a generate -s 9a -A RSA2048 -o public-key.pem
yubico-piv-tool -r canokey -a verify-pin -a selfsign -s 9a -S "/CN=Test Certificate" -i public-key.pem -o certificate.pem
yubico-piv-tool -r canokey -a import-certificate -s 9a -i certificate.pem
```

## Windows 的额外操作

由于 Windows 会根据 CHUID 来缓存卡内证书信息，因此在 Windows 上导入证书后，需要更新 CHUID：
```sh
yubico-piv-tool -r canokey -a set-chuid
```
