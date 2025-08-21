---
date:
  created: 2025-08-21
---

# 记一次 Mkdocs 调试记录

终于结束了两个月的夏校和 SAT，在考虑高三一年要做些什么，突然想到可以通过还算熟悉的 Mkdocs 搭建一个站点，做一个 Java 学习的笔记，不仅自己方便看，也可以分享给别人，或者放到 github 上获得星星。说干就干，Mkdocs 的搜索功能和天生就能在 github pages 上免费搭建的特性最好不过了。

## 创建站点

因为考虑到同时想给朋友们和外国友人都能看懂，于是决定创建一个双语站点，开始捣鼓。

### 建立项目

先本地创建目录建一个 mkdocs 的项目。

```zsh
mkdocs new .
```

创建好之后项目的基础结构是这样的

```
.
├─ docs/
│  └─ index.md
└─ mkdocs.yml
```

### 修改配置文件

先修改`mkdocs.yml`，加一些基础好用的功能。

```yml
site_name: Notes of Java #修改站点名字
site_url: https://www.notesofjava.com #修改站点URL
theme:
  language: en #设置站点语言
  name: material #修改一个主题
  features: #加一些插件
    - search.suggest
    - search.highlight
    - search.share
    - header.autohide
    - navigation.tabs
    - navigation.sections
    - navigation.path
    - navigation.indexes
    - navigation.top
    - navigation.instant
    - navigation.instant.prefetch
  icon:
    logo: material/library-outline
  palette: #按照官方文档里加上Dark Mode
    # Palette toggle for automatic mode
    - media: "(prefers-color-scheme)"
      toggle:
        icon: material/brightness-auto
        name: Switch to light mode

    # Palette toggle for light mode
    - media: "(prefers-color-scheme: light)"
      scheme: default
      toggle:
        icon: material/brightness-7
        name: Switch to dark mode

    # Palette toggle for dark mode
    - media: "(prefers-color-scheme: dark)"
      scheme: slate
      toggle:
        icon: material/brightness-4
        name: Switch to system preference

plugins: #加上最好用的搜索功能插件
  - search
  - offline
```

到这里站点已经初具雏形，本地 serve 一下，没有问题就可以准备开始写切换语言的功能了。

```zsh
mkdocs serve
```

```
INFO    -  Building documentation...
INFO    -  Cleaning site directory
INFO    -  Documentation built in 0.18 seconds
INFO    -  [17:45:59] Watching paths for changes: 'docs', 'mkdocs.yml'
INFO    -  [17:45:59] Serving on http://127.0.0.1:8000/mysite/
```

## i18n 的实现过程

Material for MkDocs 支持 i18n，还支持 60+语言的翻译。所以我们可以在配置文件里写一个语言选择器。

### 第一次尝试

还是进入`mkdocs.yml`，在结尾添加一个`extra.alternate`，并且加上中文和英文的路径。

```yml
extra:
  alternate:
    - name: English
      link: /en/
      lang: en
    - name: 简体中文
      link: /zh/
      lang: zh
```

页面上顿时出现了一个语言选择器，但是现在的相对路径还没有创建，所以点进去还是 404。于是，我创建了两个分别为`zh`和`en`的文件夹，把文件放了进去。尝试在本地 server 上切换，结果发现切换过去不是 None 就是 404,检查了一下原来是导航还没有设置，于是又在`mkdocs.yml`里添加了`nav`，随便写了一些结构先看看效果。

```yml
nav:
  - Home: index.md
  - Basics:
      - Basics/Basics_intro.md
```

页面切换成功了，但是语言切换的时候还是页面丢失。因为 Insider 的存在，理论来说在切换语言的时候会切换到对应语言文件夹并智能地读取在对应的当前页面的 Markdown 文件。但是我的站点却没有这样运作。

### 第二次尝试

再次检查所有的地方哪里出了问题。想到可以通过地址栏的路径检查是哪里出了问题，于是从 Material 的 Documentation 找了一些知名项目的文档，看看他们切换语言的时候，url 的页面是怎么变化的。看了 FastAPI 的文档之后，我发现每个页面都是一个路径，但是我的最终页面却是一个 html 文件。检查了好久终于发现是在`plugin`里的`offline`这个功能导致的。为了能让页面在离线的时候也能使用，Material 把每一个文件都变成了 html。删除`offline`之后，终于变成了路径，现在可以正常跳转路径了。

### 第三次尝试

本来以为大功告成了，但是在切换目录的时候有的时候突然会退回首页。又去 FastAPI 的文档看了一遍，发现这个文档的跳转竟然比我自己的还乱，不仅有的时候会跳回首页，Translator 的翻译有的时候也不完全。再次尝试把英语页面的文件又在`docs`主文件夹复制了一份，当作默认的语言，但是也没有用。最终决定创建两个项目，对应两个仓库，等挂上域名了再分别在配置文件里互相指向。

####
