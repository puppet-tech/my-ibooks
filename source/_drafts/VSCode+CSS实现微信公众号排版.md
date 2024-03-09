---
id: "chengxin"
---


@import "../../styles/chengxin.less"

如何实现在VSCode中编写Markdown文章并用自定义的CSS样式进行排版之后，直接复制粘贴到微信公众号呢？

<!-- more -->

在VSCode中编写Markdown并将其转换为带有自定义CSS样式的HTML，以便复制粘贴到微信公众号，可以通过以下步骤实现：

## 在VSCode中编写Markdown

首先，在VSCode中创建一个Markdown文件（`.md`），并开始编写你的文章。

## 安装Markdown预览增强插件

为了更好地转换Markdown到HTML，并应用自定义CSS样式，你可以安装**Markdown Preview Enhanced**这个VSCode扩展。这个扩展不仅允许你预览Markdown文件，还可以导出成多种格式包括HTML。

在VSCode中，去往扩展市场搜索并安装**Markdown Preview Enhanced**。

## 步骤3: 创建自定义CSS样式文件

创建一个CSS文件来编写你希望应用到文章的自定义样式。比如，你可以创建一个名为`custom-style.css`的文件，并添加一些基本的样式定义。

```css
body {
    font-family: 'Arial', sans-serif;
}
h1, h2, h3 {
    color: #333;
}
p {
    line-height: 1.6;
}
```

## 将自定义CSS应用到Markdown预览

在**Markdown Preview Enhanced**的预览窗口中，你可以应用自定义CSS使预览效果与微信公众号的最终效果尽可能一致。

- 打开Markdown文件。
- 使用快捷键`Ctrl+Shift+P`（Windows）或`Cmd+Shift+P`（Mac）打开命令面板。
- 输入`Markdown Preview Enhanced: Customize Css`来找到并选择相关命令。
- 这将打开一个允许你输入自定义CSS样式的文件。将之前创建的CSS样式复制粘贴到这里，或者直接在这里编写样式。

## 将Markdown导出为HTML

使用**Markdown Preview Enhanced**的导出功能，将你的Markdown文件及其应用了自定义CSS的内容导出为HTML文件。

- 在Markdown文件的预览窗口中，点击右上角的“更多”（...）按钮。
- 选择`导出` -> `HTML`。
- 在弹出的选项中，选择是否包含样式等选项，根据需要进行选择。

## 从HTML复制内容到微信公众号

- 打开导出的HTML文件在浏览器中。
- 使用浏览器的“查看源代码”功能，复制HTML内容。
- 进入微信公众号编辑器，切换到“源代码”模式（通常是一个`</>`图标），粘贴刚才复制的HTML代码。

注意：微信公众号的编辑器可能不支持所有HTML和CSS特性。因此，在粘贴后可能需要调整以确保内容在微信公众号中的显示效果。

## 其他注意事项

- 微信公众号对HTML和CSS的支持有限，可能会过滤掉一些标签和样式。因此，即使你精心设计了CSS样式，最终显示的效果也可能与预期有所不同。
- 有一些在线工具和服务可以帮助转换Markdown到适用于微信公众号的格式，考虑使用这些工具以简化流程。

通过以上步骤，你可以在VSCode中编写Markdown文章，应用自定义CSS样式，并将内容导出和格式化，以便复制到微信公众号中。