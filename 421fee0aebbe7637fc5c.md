<!--
title:   SVProgressHUDが左上に固定される場合
tags:    Swift
id:      421fee0aebbe7637fc5c
private: false
-->
## SVProgressHUDがなぜか左上に表示される場合
SVProgressHUDの中心が(0, 0)になってしまうことがあるようです。
その場合の応急処置。

```swift
func application(_ application: UIApplication, 
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool 
{
    // Override point for customization after application launch.  

    SVProgressHUD.setOffsetFromCenter(
        UIOffset(horizontal: UIScreen.main.bounds.width/2,
                   vertical: UIScreen.main.bounds.height/2)
    )
}
```

オフセットを調整して中央に来るようにする。
あまりよろしくないが、急ぎの場合はこれで。

###よくあるコードをSwiftで
あまり関係ないかもしれないけど、、、ずれを検出するコード

```swift
extension SVProgressHUD {

    static func adjustSVProgressHuDHudViewCenter() -> CGFloat {
        var keyboardWindow: UIWindow?
        for window in UIApplication.shared.windows {
            if window.nameOfClass == UIWindow.nameOfClass {
                keyboardWindow = window
                break
            }
        }
        
        if let keyboardWindow = keyboardWindow {
            for possibleKeyboard in keyboardWindow.subviews {
                if possibleKeyboard.nameOfClass == "UIPeripheralHostView" ||
                    possibleKeyboard.nameOfClass == "UIKeyboard"
                {
                    return possibleKeyboard.bounds.height
                }
                else {
                    if possibleKeyboard.nameOfClass == "UIInputSetContainerView" {
                        for possibleKeyboardSubview in possibleKeyboard.subviews {
                            if possibleKeyboardSubview.nameOfClass == "UIInputSetHostView" {
                                return possibleKeyboardSubview.bounds.height
                            }
                        }
                    }
                }
            }
        }
        return 0
    }
}


extension NSObject {

    static var nameOfClass: String {
        return NSStringFromClass(self).components(separatedBy: ".").last!
    }

    var nameOfClass: String {
        return NSStringFromClass(type(of: self)).components(separatedBy: ".").last!
    }
}
```