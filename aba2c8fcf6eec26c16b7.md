<!--
title:   SCPコマンド　大量のファイルの送信時に接続が切れてしまう対策
tags:    Linuxコマンド
id:      aba2c8fcf6eec26c16b7
private: false
-->
## こんな風に途中で切れてしまう場合

```                                                    
received disconnect from xxx.xxx.xx.x: 2: Packet corrupt
```

## 対策

```
[root@localhost ~]# ethtool -K eth0 tx off rx off
```