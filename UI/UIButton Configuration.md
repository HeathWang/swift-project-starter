# UIButton Configuration

## 概览

`iOS 15.0+` UIButton增加新的api，可以更加方便的配置button UI。

简单的属性介绍请参见一下blog介绍：
1. [new-way-to-style-uibutton-in-ios15](https://sarunw.com/posts/new-way-to-style-uibutton-in-ios15/)
2. [button-configuration-in-ios-15](https://useyourloaf.com/blog/button-configuration-in-ios-15/)


### 坑
下面重点说一下使用过程中遇到的一些坑点
我的按钮需求很简单，一个常规圆角按钮，按钮内包含一个icon和按钮标题；icon和标题需要居中显示，并且icon居左，标题居右；
按钮有一个常规色，比如灰色，点击是需要高亮，高亮色为粉色，高亮时按钮文字需要变成白色，常规色为黑色。

看起来用以前的方式实现没什么难度，这次我使用Configuration来实现这个button。

第一版实现如下：
```swift
extension UIButton {
    /// 配置带图标的按钮样式
    /// - Parameters:
    ///   - title: 按钮文字
    ///   - image: 按钮图标
    ///   - cornerRadius: 圆角大小
    ///   - normalColor: 正常状态背景色
    ///   - highlightedColor: 按压状态背景色
    ///   - normalTextColor: 正常状态文字颜色
    ///   - highlightedTextColor: 按压状态文字颜色
    ///   - imagePadding: 图标与文字间距
    static func iconTextByMiddleButton(
        title: String,
        image: UIImage?,
        cornerRadius: CGFloat = 16,
        normalColor: UIColor = .systemBlue,
        highlightedColor: UIColor? = nil,
        normalTextColor: UIColor = .white,
        highlightedTextColor: UIColor? = nil,
        imagePadding: CGFloat = 8,
        font: UIFont = .systemFont(ofSize: 16)
    ) -> UIButton {
        var config = UIButton.Configuration.filled()
        config.background.cornerRadius = cornerRadius
        config.background.backgroundColor = normalColor
        config.automaticallyUpdateForSelection = false
        
        config.baseForegroundColor = normalTextColor
        config.image = image
        config.title = title
        config.titleAlignment = .leading
        config.imagePadding = imagePadding
        config.titleTextAttributesTransformer = UIConfigurationTextAttributesTransformer { incoming in
            var outgoing = incoming
            outgoing.font = font
            return outgoing
        }
        
        let button = UIButton(configuration: config)
        
        button.configurationUpdateHandler = { button in
            guard var config = button.configuration else { return }
            
            if button.state.contains(.highlighted) || button.state.contains(.selected) {
                config.background.backgroundColor = highlightedColor ?? normalColor
                config.baseForegroundColor = highlightedTextColor ?? normalTextColor
            } else {
                config.background.backgroundColor = normalColor
                config.baseForegroundColor = normalTextColor
            }
            button.configuration = config
        }
        
        return button
    }
```

下面的代码用来调用上述extension方法来生成一个UIButton：
```swift
let button = UIButton.iconTextByMiddleButton(
            title: title,
            image: icon,
            normalColor: .color("#F5F5F5")!,
            highlightedColor: ColorAsset.Color(.purpleMain),
            normalTextColor: ColorAsset.Color(.darkBase),
            highlightedTextColor: .white,
            font: .appFont(fontSize: 15, weight: .medium))
```

可以看到，做了一下配置：
1. 常规背景色为`#F5F5F5`，高亮背景色为`purpleMain`
2. 常规字体颜色为`darkBase`，高亮颜色为`white`

当实际点击时，按钮颜色确实变成了白色，但是白色降低了alpha，我想要的是纯白色。

这里卡了很长时间，从代码中我们可以看到，`baseForegroundColor`实际上影响了按钮title颜色，但是高亮时确实设置了为白色，为什么点击时颜色降低了呢？

通过查看Apple doc，可以看到Configuration中有个属性为`attributedTitle`，介绍如下：
> The text and style attributes for the button’s title label.
> The configuration sets the title property to match the string value of this attributed string. To change the button title when the button state changes, use configurationUpdateHandler or updateConfiguration().

尝试在`configurationUpdateHandler`回调中变更attributedTitle，最终达到保持高亮时字体颜色是我们设置呢。

```swift
button.configurationUpdateHandler = { button in
    guard var config = button.configuration else { return }
    
    if button.state.contains(.highlighted) || button.state.contains(.selected) {
        config.background.backgroundColor = highlightedColor ?? normalColor
        config.baseForegroundColor = highlightedTextColor ?? normalTextColor
        var attrString = AttributedString(title)
        attrString.foregroundColor = highlightedTextColor ?? normalTextColor
        config.attributedTitle = attrString
    } else {
        config.background.backgroundColor = normalColor
        config.baseForegroundColor = normalTextColor
        var attrString = AttributedString(title)
        attrString.foregroundColor = normalTextColor
        config.attributedTitle = attrString
    }
    button.configuration = config
}
```

