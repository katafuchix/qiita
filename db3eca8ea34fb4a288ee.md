<!--
title:   CentsOS 6.x  [SSL: DECRYPTION_FAILED_OR_BAD_RECORD_MAC]  
tags:    CentOS6.x
id:      db3eca8ea34fb4a288ee
private: false
-->
`
# pip install --upgrade pip
Collecting pip
  Downloading https://files.pythonhosted.org/packages/d8/f3/413bab4ff08e1fc4828dfc59996d721917df8e8583ea85385d51125dceff/pip-19.0.3-py2.py3-none-any.whl (1.4MB)
    10% |███▍                            | 143kB 3.0MB/s eta 0:00:01
Could not install packages due to an EnvironmentError: [SSL: DECRYPTION_FAILED_OR_BAD_RECORD_MAC] decryption failed or bad record mac (_ssl.c:2305)
`

こんなエラーが出た場合

```
[root@localhost ~]# mkdir -p /root/cert.bak
[root@localhost ~]# rpm -Vv ca-certificates | awk '$1!="........."&& $2!="d" {system("mv -v " $NF " /root/cert.bak")}'
`/etc/pki/java/cacerts' -> `/root/cert.bak/cacerts'
`/etc/pki/tls/certs/ca-bundle.crt' -> `/root/cert.bak/ca-bundle.crt'
`/etc/pki/tls/certs/ca-bundle.trust.crt' -> `/root/cert.bak/ca-bundle.trust.crt'
[root@localhost ~]# yum check-update ca-certificates; (($?==100)) && yum update ca-certificates || yum reinstall ca-certificates

インストール:
  ca-certificates.noarch 0:2018.2.22-65.1.el6

完了しました!

```

確認

```
[root@localhost ~]# pip install --upgrade pip
Collecting pip
  Downloading https://files.pythonhosted.org/packages/d8/f3/413bab4ff08e1fc4828dfc59996d721917df8e8583ea85385d51125dceff/pip-19.0.3-py2.py3-none-any.whl (1.4MB)
    100% |████████████████████████████████| 1.4MB 5.3MB/s
Installing collected packages: pip
  Found existing installation: pip 19.0.2
    Uninstalling pip-19.0.2:
      Successfully uninstalled pip-19.0.2
Successfully installed pip-19.0.3
```