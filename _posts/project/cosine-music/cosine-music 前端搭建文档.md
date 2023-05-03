## 环境搭建

### 加入 material-ui
[Style library interoperability - Material UI](https://mui.com/material-ui/guides/interoperability/#tailwind-css)
1.  按照 [https://tailwindcss.com/docs/installation](https://tailwindcss.com/docs/installation) 中的说明将 Tailwind CSS 添加到您的项目。
2.  删除 Tailwind CSS 的预检样式，以便它可以改用 MUI 的预检样式 (CssBaseline)。
3. 添加 `important` 选项，使用您的应用包装器的 ID。例如， `#__next` 用于 Next.js， `"#root"` 用于 CRA：
4. 修复 CSS 注入顺序。大多数 CSS-in-JS 解决方案在 HTML `<head>` 的底部注入它们的样式，这使 MUI 优先于 Tailwind CSS。要减少对 `important` 属性的需求，您需要更改 CSS 注入顺序。
