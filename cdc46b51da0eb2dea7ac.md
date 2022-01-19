<!--
title:   Swift UITableViewCell 特定のセルだけ線を消す
tags:    Swift,UITableViewCell
id:      cdc46b51da0eb2dea7ac
private: false
-->
# Swift UITableViewCell 特定のセルだけ線を消す

separatorInsetの調整
https://developer.apple.com/documentation/uikit/uitableview/1614851-separatorinset

```swift
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {

    let cell = tableView.dequeueReusableCell(withIdentifier: R.reuseIdentifier.Cell, for: indexPath)!
    cell.separatorInset = UIEdgeInsets(top: 0, left: self.view.bounds.width, bottom: 0, right: 0)
    return cell
}
```