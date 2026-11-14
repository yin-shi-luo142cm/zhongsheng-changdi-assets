# 酒馆助手脚本外连资源

本目录存放大型酒馆助手脚本的界面或功能主体。它们由角色卡内的小型酒馆助手 loader 在运行时载入。

## 目录约定

每项资源独立一个 kebab-case 目录：

```text
scripts/
├── README.md
└── <resource-id>/
    ├── index.js
    ├── index.html
    └── assets/
        ├── style.css
        ├── interface.js
        └── ...
```

- `index.js` 是建议的默认脚本入口；如资源只提供 HTML 或采用其他入口，须在私人主仓库资源锁定清单中明确记录。
- HTML、CSS、SVG、图片与其他模块放在同一资源目录。
- 相对路径应以资源目录为基准，并在目标酒馆助手运行环境验证。
- 本目录不保存完整角色卡脚本对象；角色卡内的小型 loader、脚本对象参数与启用状态仍由私人主仓库维护。
- 不假定任何尚未验证的酒馆助手注入、重绘或清理 API；载入器行为须以目标版本实测为准。

## 版本规则

正式 URL 必须固定到 40 位 commit SHA，例如：

```text
https://raw.githubusercontent.com/yin-shi-luo142cm/zhongsheng-changdi-assets/<commit-sha>/scripts/<resource-id>/index.js
```

不得在正式角色卡中使用 `main` 或其他浮动引用。
