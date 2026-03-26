# 文档中心使用指南

本文档站点使用 [Docsify](https://docsify.js.org/) 构建，部署在 GitHub Pages 上。

## 目录

- [本地预览](#本地预览)
- [添加文档](#添加文档)
- [组织层级结构](#组织层级结构)
- [部署到 GitHub Pages](#部署到-github-pages)

---

## 本地预览

在本地修改文档前，建议先启动本地服务器预览效果。

### 方式一：Python（推荐）

```bash
# 进入项目目录
cd yanshaoshuai.github.io

# 启动 HTTP 服务器
python3 -m http.server 3000

# 访问 http://localhost:3000
```

### 方式二：Node.js

```bash
# 使用 npx（无需安装）
npx serve .

# 或使用 docsify-cli
npm i docsify-cli -g
docsify serve .
```

### 方式三：PHP

```bash
php -S localhost:3000
```

---

## 添加文档

### 1. 创建 Markdown 文件

在合适的位置创建 `.md` 文件：

```bash
# 根目录文档
touch new-page.md

# 子目录文档
mkdir -p topic/sub-topic
touch topic/sub-topic/detail.md
```

### 2. 编写内容

使用标准 Markdown 语法：

```markdown
# 页面标题

## 二级标题

正文内容，支持 **粗体**、*斜体*、`代码` 等。

### 代码块

```javascript
function hello() {
  console.log('Hello, World!');
}
```

### 表格

| 列1 | 列2 |
|-----|-----|
| A   | B   |

### 链接

[链接文字](path/to/page.md)
```

### 3. 更新侧边栏

编辑 `_sidebar.md`，添加新页面链接：

```markdown
* [新页面标题](new-page.md)
* [子目录页面](topic/sub-topic/detail.md)
```

### 4. 刷新浏览器

Docsify 会自动加载新内容，无需重启服务器。

---

## 组织层级结构

### 侧边栏分组

使用缩进列表创建层级结构：

```markdown
* **入门指南**              ← 分组标题（粗体）
  * [首页](/)
  * [快速开始](quickstart.md)
  * [安装配置](install.md)

* **技术文档**              ← 另一个分组
  * [Java 基础](java/basic.md)
  * [Python 基础](python/basic.md)
  * [前端开发](frontend/web.md)

* **其他**
  * [关于我](about.md)
```

**效果：**

- 分组标题显示为粗体，可点击折叠/展开
- 子项缩进显示，带有左边框

### 文件目录组织

推荐的目录结构：

```
.
├── index.html          # 站点配置（无需修改）
├── _sidebar.md         # 侧边栏配置
├── README.md           # 首页内容（本文档）
├── quickstart.md       # 根目录单页
├── install.md
├── about.md
├── java/               # 分类目录
│   ├── basic.md
│   ├── springboot.md
│   └── jvm.md
├── python/             # 另一个分类
│   ├── basic.md
│   ├── django.md
│   └── data-analysis.md
└── frontend/           # 前端分类
    ├── html-css.md
    ├── javascript.md
    └── vue.md
```

### 嵌套层级

如需更深的层级，在 `_sidebar.md` 中继续缩进：

```markdown
* **编程语言**
  * Java
    * [基础](java/basic.md)
    * [进阶](java/advanced.md)
  * Python
    * [基础](python/basic.md)
    * [Web 开发](python/web.md)
```

**注意：** 层级过深可能影响阅读体验，建议最多 3 层。

---

## 文档规范

### 文件名

- 使用小写字母
- 单词间用连字符 `-` 连接
- 例如：`quick-start.md`、`java-basic.md`

### 页面标题

- 每个 `.md` 文件的第一行应为 `# 一级标题`
- 标题会显示在浏览器标签页和侧边栏

### 内部链接

- 相对路径：`[链接](path/to/file.md)`
- 根目录：`[首页](/)` 或 `[首页](README.md)`
- 锚点：`[跳转到标题](page.md#标题-id)`

### 图片引用

```markdown
![图片描述](assets/image.png)
```

图片放在 `assets/` 目录下。

---

## 部署到 GitHub Pages

### 1. 提交代码

```bash
git add .
git commit -m "添加新文档"
git push origin main
```

### 2. 开启 GitHub Pages

1. 进入 GitHub 仓库页面
2. 点击 **Settings** → **Pages**
3. **Source** 选择 `Deploy from a branch`
4. **Branch** 选择 `main`，文件夹选择 `/(root)`
5. 点击 **Save**

### 3. 访问站点

等待 1-2 分钟后，访问：

```
https://yanshaoshuai.github.io
```

---

## 常用功能

### 搜索

站点支持全文搜索，在侧边栏顶部的搜索框输入关键词即可。

### 折叠侧边栏

点击分组标题（如"入门指南"）可折叠/展开该分组。折叠状态会在页面切换后保持。

### 代码高亮

支持多种编程语言：

- `javascript`
- `python`
- `java`
- `bash`
- `json`
- `html`
- `css`

### 图片缩放

点击文档中的图片可放大查看。

---

## 参考资料

- [Docsify 官方文档](https://docsify.js.org/#/zh-cn/)
- [Markdown 语法指南](https://www.markdownguide.org/)
- [GitHub Pages 文档](https://docs.github.com/cn/pages)
