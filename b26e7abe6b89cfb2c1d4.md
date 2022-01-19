<!--
title:   Invalid Signature - A sealed resource is missing or invalid. The file at path [アプリ名] is not properly signed.
tags:    Objective-C,Swift,Xcode
id:      b26e7abe6b89cfb2c1d4
private: false
-->
# Invalid Signature - A sealed resource is missing or invalid. The file at path [アプリ名] is not properly signed.

いつも通りアプリを申請に出してもこんなメールが来てバイナリが受け付けられない場合

```
Dear Developer,

We identified one or more issues with a recent delivery for your app, "My ルーレット". 
Please correct the following issues, then upload again.

Invalid Signature - A sealed resource is missing or invalid. The file at path [MyRoulette.app/MyRoulette] is not properly signed. 
Make sure you have signed your application with a distribution certificate, not an ad hoc certificate or a development certificate. 
Verify that the code signing settings in Xcode are correct at the target level (which override any values at the project level). 
Additionally, make sure the bundle you are uploading was built using a Release target in Xcode, not a Simulator target. 
If you are certain your code signing settings are correct, choose "Clean All" in Xcode, 
delete the "build" directory in the Finder, and rebuild your release target. 
For more information, please consult
 https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html

Best regards,

The App Store Team
```
## 全角が入ったファイルが原因
全角の名前を使った画像などのリソースがないかチェック　あれば削除か名前を変更