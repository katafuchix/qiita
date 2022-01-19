<!--
title:   Swift ライブラリKeychainAccessの利用例
tags:    KeychainAccess,Swift
id:      f9bb07dfedef191b3b8b
private: false
-->
### ライブラリKeychainAccess利用
https://github.com/kishikawakatsumi/KeychainAccess/
アプリを削除した後にもデータを残して、再インストールした後に使いたい、iCloud Keychain で値を保存しておきたい場合など

### 導入
インスタンス生成の際にキーが必要なのでBundleIdentifierをキーとしています。
もちろん任意の文字列が使えます。

```swift
import KeychainAccess

var keychain: Keychain {
    guard let identifier = Bundle.main.object(forInfoDictionaryKey: "CFBundleIdentifier") as? String else { return Keychain(service: "") }
    return Keychain(service: identifier)
}
```

### 保存
キーを指定して値を保存

```swift
do {
    try? keychain.set(value, key: "key")
} catch {
    // エラー処理
}
```

### 参照
キーを指定して値を参照
Stringの例
エラーが発生することもあるのでオプショナル型にしておくのが無難っぽい

```swift
var stringValue: String? {
    do {
        return try keychain.get("key")
    } catch {
        return nil
    }
}
```