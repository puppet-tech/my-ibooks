---
id: "chengxin"
---

@import “../../styles/chengxin.less”

如何实现在 VSCode 中编写 Markdown 文章并用自定义的 CSS 样式进行排版之后，直接复制粘贴到微信公众号呢？

<!-- more -->

在 VSCode 中编写 Markdown 并将其转换为带有自定义 CSS 样式的 HTML，以便复制粘贴到微信公众号，可以通过以下步骤实现：

## 1.1 在 VSCode 中编写 Markdown

首先，在 VSCode 中创建一个 Markdown 文件（`.md`），并开始编写你的文章。

## 1.2 安装 Markdown 预览增强插件

为了更好地转换 Markdown 到 HTML，并应用自定义 CSS 样式，你可以安装**Markdown Preview Enhanced**这个 VSCode 扩展。这个扩展不仅允许你预览 Markdown 文件，还可以导出成多种格式包括 HTML。

在 VSCode 中，去往扩展市场搜索并安装**Markdown Preview Enhanced**。

## 1.3 步骤 3: 创建自定义 CSS 样式文件

创建一个 CSS 文件来编写你希望应用到文章的自定义样式。比如，你可以创建一个名为 `custom-style.css` 的文件，并添加一些基本的样式定义。

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

## 1.4 将自定义 CSS 应用到 Markdown 预览

在**Markdown Preview Enhanced**的预览窗口中，你可以应用自定义 CSS 使预览效果与微信公众号的最终效果尽可能一致。

- 打开 Markdown 文件。
- 使用快捷键 `Ctrl+Shift+P`（Windows）或 `Cmd+Shift+P`（Mac）打开命令面板。
- 输入 `Markdown Preview Enhanced: Customize Css` 来找到并选择相关命令。
- 这将打开一个允许你输入自定义 CSS 样式的文件。将之前创建的 CSS 样式复制粘贴到这里，或者直接在这里编写样式。

## 1.5 将 Markdown 导出为 HTML

使用**Markdown Preview Enhanced**的导出功能，将你的 Markdown 文件及其应用了自定义 CSS 的内容导出为 HTML 文件。

- 在 Markdown 文件的预览窗口中，点击右上角的“更多”（…）按钮。
- 选择 `导出` -> `HTML`。
- 在弹出的选项中，选择是否包含样式等选项，根据需要进行选择。

## 1.6 从 HTML 复制内容到微信公众号

- 打开导出的 HTML 文件在浏览器中。
- 使用浏览器的“查看源代码”功能，复制 HTML 内容。
- 进入微信公众号编辑器，切换到“源代码”模式（通常是一个 `</>` 图标），粘贴刚才复制的 HTML 代码。

注意：微信公众号的编辑器可能不支持所有 HTML 和 CSS 特性。因此，在粘贴后可能需要调整以确保内容在微信公众号中的显示效果。

## 1.7 其他注意事项

- 微信公众号对 HTML 和 CSS 的支持有限，可能会过滤掉一些标签和样式。因此，即使你精心设计了 CSS 样式，最终显示的效果也可能与预期有所不同。
- 有一些在线工具和服务可以帮助转换 Markdown 到适用于微信公众号的格式，考虑使用这些工具以简化流程。

通过以上步骤，你可以在 VSCode 中编写 Markdown 文章，应用自定义 CSS 样式，并将内容导出和格式化，以便复制到微信公众号中。
