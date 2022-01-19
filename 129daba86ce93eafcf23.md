<!--
title:   mecab-python  インストール時のエラー対応
tags:    Python
id:      129daba86ce93eafcf23
private: false
-->
## mecab-python  インストール時にエラーが出る場合
環境 CentOS8 python 3.8.2

```
# dnf install python3-devel
# pip install mecab-python
WARNING: Running pip install with root privileges is generally not a good idea. Try `pip install --user` instead.
Collecting mecab-python
  Downloading https://files.pythonhosted.org/packages/86/e7/bfeba61fb1c5d1ddcd92bc9b9502f99f80bf71a03429a2b31218fc2d4da2/mecab-python-0.996.tar.gz (40kB)
    100% |████████████████████████████████| 40kB 947kB/s
    Complete output from command python setup.py egg_info:
    Traceback (most recent call last):
      File "<string>", line 1, in <module>
      File "/tmp/pip-build-9vu16hz5/mecab-python/setup.py", line 18, in <module>
        include_dirs=cmd2("mecab-config --inc-dir"),
      File "/tmp/pip-build-9vu16hz5/mecab-python/setup.py", line 10, in cmd2
        return string.split (cmd1(str))
    AttributeError: module 'string' has no attribute 'split'

    ----------------------------------------
Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-9vu16hz5/mecab-python/

```

setup.py を書き換えて対応する

## ダウンロード
まず mecab-python-0.996.tar.gz をダウンロードして解凍する

```
# wget https://files.pythonhosted.org/packages/86/e7/bfeba61fb1c5d1ddcd92bc9b9502f99f80bf71a03429a2b31218fc2d4da2/mecab-python-0.996.tar.gz
# tar xvzf mecab-python-0.996.tar.gz
# cd mecab-python-0.996
# ls
MeCab.py  MeCab_wrap.cxx  PKG-INFO  README  setup.py
```

## setup.py のコード書き換え
エラーになっている部分を書き換える

変更前

```
def cmd2(str):
    return string.split (cmd1(str))
```

変更後

```
def cmd2(str):
    #return string.split (cmd1(str))
    return cmd1(str).split()
```

## インストール

```
# python setup.py install
```

## 確認

```mecab-test.py
import MeCab

text = '米経済対策、総額220兆円も　GDP10％、外出規制が拡大'
m = MeCab.Tagger ()
words = m.parse (text).split('\n')
for elm in words:
    print(elm)
```

`
# python mecab-test.py
米      名詞,固有名詞,地域,国,*,*,米,ベイ,ベイ
経済    名詞,一般,*,*,*,*,経済,ケイザイ,ケイザイ
対策    名詞,サ変接続,*,*,*,*,対策,タイサク,タイサク
、      記号,読点,*,*,*,*,、,、,、
総額    名詞,一般,*,*,*,*,総額,ソウガク,ソーガク
220     名詞,数,*,*,*,*,*
兆      名詞,数,*,*,*,*,兆,チョウ,チョー
円      名詞,接尾,助数詞,*,*,*,円,エン,エン
も      助詞,係助詞,*,*,*,*,も,モ,モ
　      記号,空白,*,*,*,*,　,　,　
GDP     名詞,一般,*,*,*,*,*
10      名詞,数,*,*,*,*,*
％      名詞,接尾,助数詞,*,*,*,％,パーセント,パーセント
、      記号,読点,*,*,*,*,、,、,、
外出    名詞,サ変接続,*,*,*,*,外出,ガイシュツ,ガイシュツ
規制    名詞,サ変接続,*,*,*,*,規制,キセイ,キセイ
が      助詞,格助詞,一般,*,*,*,が,ガ,ガ
拡大    名詞,サ変接続,*,*,*,*,拡大,カクダイ,カクダイ
EOS
`

## エラーが出る場合

```
# python mecab-test.py
Traceback (most recent call last):
  File "mecab-test.py", line 4, in <module>
    m = MeCab.Tagger ()
  File "/usr/local/lib64/python3.6/site-packages/MeCab.py", line 307, in __init__
    this = _MeCab.new_Tagger(*args)
RuntimeError
```

環境変数MECABRCを設定

```
[mecab インストールパス]/etc/mecabrc
```

設定の例

```/etc/profile
export MECABRC=/usr/local/etc/mecabrc
```

## mecabインストール手順
[php-mecabインストール](c19407f34ac9d23d3b34.md)