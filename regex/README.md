# 原生正则外连资源

本目录存放由 SillyTavern **原生正则**通过 `replaceString` 内嵌的 HTML／JavaScript 框架，在运行时载入的大型界面主体。

## 目录约定

每条大型正则界面独立一个 kebab-case 目录：

```text
regex/
├── README.md
└── <resource-id>/
    ├── index.html
    └── assets/
        ├── style.css
        ├── interface.js
        └── ...
```

- `index.html` 是默认入口；若资源需要不同入口，必须在私人主仓库的资源锁定清单中明确记录。
- CSS、JavaScript、SVG、图片等配套文件放在该资源目录内。
- HTML 内引用配套文件时优先使用相对路径，使整个资源目录可随同一 commit 固定。
- 这里保存的是远端界面主体，不保存完整 SillyTavern 正则对象；正则对象与小型载入框架仍由私人主仓库维护。

## 运行方式

角色卡中的原生正则以短小占位标记匹配，再由 `replaceString` 中的 HTML／JavaScript 框架载入此处的 HTML 入口。不得把 URL 单独写入 `replaceString` 并假设平台会自动请求。

用户提供的「[十个约定]状态栏」仅作为技术测试样本，用于验证正则替换内容内嵌 HTML／JavaScript loader 的可行性。它不代表未来角色卡的界面规划、功能需求、数据结构或视觉风格。即使测试成功，也只确认载入机制，不将该状态栏的 HTML、CSS、MVU Schema 或业务逻辑转为正式资源。

测试样本应与正式资源分离；不得将其放入正式资源目录或登记为正式角色卡运行时依赖。

## 版本规则

正式 URL 必须固定到 40 位 commit SHA，例如：

```text
https://raw.githubusercontent.com/yin-shi-luo142cm/zhongsheng-changdi-assets/<commit-sha>/regex/<resource-id>/index.html
```

不得在正式角色卡中使用 `main` 或其他浮动引用。
