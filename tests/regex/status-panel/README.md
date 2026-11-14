# 隔离测试：状态栏正则远端载入

> **仅供技术验证。不是正式角色卡资源，也不代表未来角色卡的界面、功能、字段或视觉规划。**

## 测试目的

验证一条 SillyTavern 原生正则能否在 `replaceString` 中保留短小 HTML／JavaScript loader，并从公开资源仓库的固定 commit SHA URL 取得远端 HTML 主体。

## 测试样本来源

用户提供的 `regex-[十个约定]状态栏.json`。本目录只抽取其 `replaceString` 中的 HTML 文档作为测试主体；不复制完整正则对象，不将它加入正式 `regex/` 或 `resources.lock.json`。

## 样本本身的技术特征

- 原始 `replaceString` 是 Markdown 的 `html` 围栏，包含完整 HTML、CSS 与一个 `type="module"` 脚本。
- 模块脚本从原样保留的 jsDelivr URL 导入 `registerMvuSchema`。
- 脚本还依赖运行环境提供的 `Mvu`、`waitGlobalInitialized`、`getAllVariables`、`errorCatched`、jQuery（`$`）与 Lodash（`_`）。
- 本测试不复制、托管或改写第三方模块，也不把这些依赖升级为正式角色卡的必要依赖。

## 文件

- `index.html`：从原始正则 `replaceString` 提取的 HTML 主体，内容保持原样。
- `replace-string.txt`：第二阶段生成的最小远端载入框架示例。URL 固定到测试主体所在的 commit SHA：`4ce61772df018ee107a696906ea63ee17c9e6ceb`。

## 当前测试 loader 的工作方式

1. 使用浏览器 `fetch()` 请求固定 SHA 的 raw HTML。
2. 使用 `DOMParser` 解析文档。
3. 将远端 `<style>` 注入当前页面的 `<head>`。
4. 将远端 `<body>` 内容放入测试根节点。
5. 重新创建远端 `type="module"` 脚本节点，使模块脚本有机会执行。
6. 请求失败时显示错误提示，并写入控制台。

## 验证边界

仓库提交和静态检查只能证明文件已发布、路径可读取及代码语法可解析。Markdown 渲染、正则替换时机、浏览器跨域请求、模块导入、酒馆全局对象、重复触发与清理行为，必须在实际 SillyTavern／酒馆助手环境中测试；未实机测试前不得标记为通过。

该 loader 是隔离测试实现，不是正式构建器模板。测试时尤其要确认平台是否允许该位置的内嵌脚本执行，以及 module script 是否能访问酒馆环境提供的全局对象。
