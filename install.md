# 安装配置

## Docsify 简介

Docsify 是一个神奇的文档网站生成器，它不会生成静态 HTML，而是动态加载 Markdown 文件。

## 安装 Docsify CLI（可选）

```bash
npm i docsify-cli -g
```

## 配置文件说明

### index.html

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>文档标题</title>
  <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@4/lib/themes/vue.css">
</head>
<body>
  <div id="app"></div>
  <script>
    window.$docsify = {
      name: '项目名称',
      loadSidebar: true,
      subMaxLevel: 2
    }
  </script>
  <script src="//cdn.jsdelivr.net/npm/docsify@4"></script>
</body>
</html>
```

### _sidebar.md

```markdown
* [首页](/)
* [指南](guide.md)
* [关于](about.md)
```

## 部署到 GitHub Pages

1. 推送到 GitHub 仓库
2. 进入 Settings → Pages
3. Source 选择 Deploy from a branch
4. Branch 选择 main / root
5. 保存后即可访问
