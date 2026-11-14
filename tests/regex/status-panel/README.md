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
- `replace-string.txt`：最小远端载入框架示例。URL 固定到测试主体所在的 commit SHA：`4ce61772df018ee107a696906ea63ee17c9e6ceb`。
- `test-regex.json`：可导入 SillyTavern 的完整测试正则对象，保留原样本参数，仅更换测试名称、测试 ID 与 `replaceString`。

## 当前测试 loader 的工作方式

1. 使用浏览器 `fetch()` 请求固定 SHA 的 raw HTML。
2. 使用 `DOMParser` 解析文档。
3. 将远端 `<style>` 注入当前页面的 `<head>`。
4. 将远端 `<body>` 内容放入测试根节点。
5. 重新创建远端 `type="module"` 脚本节点，使模块脚本有机会执行。
6. 请求失败时显示错误提示，并写入控制台。


## 静态检查结果

- 原始 JSON 可解析。
- HTML 文档经 HTMLParser 检查，未发现未匹配的结束标签或未闭合标签。
- 内嵌 module JavaScript 通过 Node.js 语法检查。
- 测试 loader JavaScript 通过 Node.js 语法检查。
- 远端固定 SHA 的 `index.html` 已通过 GitHub raw URL 读取，并确认包含 `.status-panel` 与 module script。
- 原始 HTML 主体约 33.7 KB；内嵌 module 脚本约 18.0 KB。

## 尚未完成的实机验证

当前执行环境无法启动用户的 SillyTavern 页面，也无法代替浏览器完成运行时测试。因此以下项目**尚未通过验证**：

- `replaceString` 输出是否被目标版本正确解析为可执行 HTML，而不是显示成代码块。
- 正则替换内容中的内嵌 `<script>` 是否会执行。
- 浏览器对 `raw.githubusercontent.com` 的 CORS 请求是否成功。
- 重新创建的 module script 是否能成功导入原有 jsDelivr 模块。
- 目标环境是否提供样本依赖的酒馆全局对象与 jQuery／Lodash。
- 多次触发、消息重绘、清理旧节点与加载失败后的恢复行为。

特别注意：本测试 loader 使用 `fetch + DOMParser`，并主动重新创建 module script；它不是对原始样本的直接复制，也尚未证明能在 SillyTavern 正则替换输出中运行。

## 在 SillyTavern 中进行实机验证

1. 下载并导入 `test-regex.json`，确认正则名称显示为「[测试]状态栏远端载入」。
2. 原样本的 `placement` 为 `[2]`（AI 输出），因此请让 AI 在回复中原样输出 `<StatusPlaceHolderImpl/>`，确认正则确实触发；不要只把占位符发在用户消息中。
3. 检查结果是否显示状态栏内容，而不是只显示 HTML／JavaScript 代码。
4. 打开浏览器开发者工具 Console／Network，检查 raw HTML 请求与 jsDelivr module 请求是否成功；特别留意 CORS、模块加载与全局对象错误。
5. 再次触发、编辑消息及重绘，观察是否出现重复界面或旧节点残留。
6. 暂时断网或将测试 URL 改成无效地址，仅在本地测试副本中操作，确认失败提示是否出现；不要修改公开仓库中的固定测试配置。

若页面未显示内容，请提供 Console 错误与 Network 请求状态；这比单凭画面更容易定位是正则执行、HTML 注入、CORS 还是模块依赖问题。

## 验证边界

仓库提交和静态检查只能证明文件已发布、路径可读取及代码语法可解析。Markdown 渲染、正则替换时机、浏览器跨域请求、模块导入、酒馆全局对象、重复触发与清理行为，必须在实际 SillyTavern／酒馆助手环境中测试；未实机测试前不得标记为通过。

该 loader 是隔离测试实现，不是正式构建器模板。测试时尤其要确认平台是否允许该位置的内嵌脚本执行，以及 module script 是否能访问酒馆环境提供的全局对象。
