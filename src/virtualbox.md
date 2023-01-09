
# 備忘録（virtualbox）

```
「エラー事象1」
```
$ sudo apt-get update
Get:1 http://linux3.yz.yamagata-u.ac.jp/pub/linux/kali kali-rolling InRelease [30.5 kB]
Get:2 http://linux3.yz.yamagata-u.ac.jp/pub/linux/kali kali-rolling/main amd64 Packages [16.6 MB]
Err:2 http://linux3.yz.yamagata-u.ac.jp/pub/linux/kali kali-rolling/main amd64 Packages                     
  Hash Sum mismatch
  Hashes of expected file:
   - Filesize:16590427 [weak]
   - SHA256:00ccf318db598c4ddcd7094d28442cdb30088ab7de8cff6c0294de484a102146
   - SHA1:12776612134b22e45ffd84538bcc493c87e881a4 [weak]
   - MD5Sum:b9db76fb5ce9653b1d721068963a1787 [weak]
  Hashes of received file:
   - SHA256:4d733b1f1ead1ce850dbd4ff906a1eb959380b181afb6eba0b9898652f58a2a1
   - SHA1:b14761cbb2748365e608bbd57db3f1c41be04a53 [weak]
   - MD5Sum:b9db76fb5ce9653b1d721068963a1787 [weak]
   - Filesize:16590427 [weak]
  Last modification reported: Tue, 23 Jun 2020 12:03:38 +0000
  Release file created at: Tue, 23 Jun 2020 12:04:33 +0000
```

このエラーが発生した場合は以下のコマンドを実行
```
$ sudo bash
# mkdir /etc/gcrypt
# echo all >> /etc/gcrypt/hwf.deny
```

新しい Linux において使用されている gcrypto20 が原因だそう。