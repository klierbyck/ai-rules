# 平台前端通用开发规范

本规则用以规范 [d:/project/qz-platform-tenant-ui](file:///d:/project/qz-platform-tenant-ui) 项目中的开发约定，包含项目命令、构建校验、代码格式、TypeScript、组件开发、路由菜单、接口响应与通知提示、分页列表布局排版、弹窗与表单提示、YAPI 调试等全部规则。

## 1. 项目概览与开发环境规范

### 1.1 项目结构与依赖管理
- 包管理器：**pnpm**
- 架构体系：**pnpm workspace + lerna** monorepo 架构
- 主前端应用：[apps/qz-fe](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe)
- 项目根目录主要包含 workspace 工具链、git hooks 以及共享配置文件。
- 大部分的业务实现和修改发生在 [apps/qz-fe/src](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src) 目录下。

### 1.2 命令执行位置规范
- 依赖安装必须在**项目根目录**下执行：`pnpm install`。
- 运行应用脚本（如启动开发服务、构建等）必须在 [apps/qz-fe](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe) 路径下执行。
- 严禁在 [apps/qz-fe](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe) 目录下直接安装依赖。如果需要为某个子应用添加包，推荐在根目录下使用过滤参数进行安装。
- 总结规则：使用 `pnpm`，在根目录下安装，在 [apps/qz-fe](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe) 目录下运行脚本。

---

## 2. 构建、校验与测试规范

### 2.1 常用构建与校验命令
以下脚本定义在 [apps/qz-fe/package.json](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/package.json) 中，应在 [apps/qz-fe](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe) 路径下执行：
- `pnpm dev` — 启动 Vite 开发服务器
- `pnpm build` — 生产环境打包构建
- `pnpm build:test` — 以测试模式（test mode）进行打包构建
- `pnpm build:check` — 执行类型检查并打包构建
- `pnpm type-check` — 运行 `vue-tsc --build` 进行 TS 类型检查
- `pnpm lint` — 运行 `eslint . --fix` 自动修复 ESLint 错误
- `pnpm format` — 运行 `prettier --write src/` 格式化代码
- `pnpm preview` — 预览本地打包后的产物

在项目根目录下可执行的全局实用脚本：
- `pnpm run lint:lint-staged`
- `pnpm run clean`
- `pnpm run bootstrap`
- `pnpm run backup`

### 2.2 测试命令规范
- 目前项目没有在根目录或 [apps/qz-fe](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe) 目录中配置任何测试脚本（如 `test`）。
- 项目中未配置任何 Vitest/Jest/Playwright/Cypress 测试框架，也未包含任何 `*.test.*` 或 `*.spec.*` 测试文件。
- 当前不支持任何单测或集成测试的执行。若后续添加了测试运行器，请及时补充单测命令到此。

---

## 3. 代码格式化与校验规范

### 3.1 格式化与配置文件
项目使用的主要配置文件有：
- [eslint.config.ts](file:///d:/project/qz-platform-tenant-ui/eslint.config.ts)
- [.prettierrc.js](file:///d:/project/qz-platform-tenant-ui/.prettierrc.js)
- [.editorconfig](file:///d:/project/qz-platform-tenant-ui/.editorconfig)
- [.husky/lintstagedrc.js](file:///d:/project/qz-platform-tenant-ui/.husky/lintstagedrc.js)

### 3.2 格式化核心规则
- 缩进：统一使用 **2 个空格** 缩进。
- 换行符：统一使用 **LF** 换行符。
- 文件结尾：每个文件末尾必须保留一个空行。
- 引号：统一使用 **单引号**（`'`）。
- 分号：**禁用分号**（配置为 `semi: false`）。
- 尾随逗号：禁用尾随逗号。
- Prettier 单行最大字符数：`printWidth: 120`。
- EditorConfig 最大字符限制：`max_line_length: 160`。
- Vue 模板支持：允许 Vue 中的 `<script>` 和 `<style>` 标签内的内容进行缩进。

### 3.3 Cursor 与 Copilot 辅助开发规则
- 项目中包含 [.cursor/rules/vue3.mdc](file:///d:/project/qz-platform-tenant-ui/.cursor/rules/vue3.mdc) 规则文件，其中定义了广泛的 Vue 3 最佳实践（适用于 Vue 3 + TypeScript + Pinia + SCSS）。
- **冲突处理原则**：当辅助规则文件与项目实际的格式化配置文件（如 [.prettierrc.js](file:///d:/project/qz-platform-tenant-ui/.prettierrc.js)、[eslint.config.ts](file:///d:/project/qz-platform-tenant-ui/eslint.config.ts)）或邻近的现有代码风格发生冲突时（例如关于分号的使用），**必须以项目实际的格式化配置和现有代码风格为准**。

### 3.4 清理未使用变量与异常捕获规范
- **未使用变量与引入强制清理**：每次编写或修改完代码后，必须主动检查并清理所有未使用的 import 导入（如 `ref`, `computed`, `watch`, `nextTick`, 组件等）以及未使用的声明变量（局部变量、函数、参数等），确保没有冗余引入与未引用声明残留，防止触发 `@typescript-eslint/no-unused-vars` 等编译及校验报错。
- **try-catch 异常忽略**：当使用 `try-catch` 块捕获一些可被安全忽略的报错且不需要使用错误对象本身时，推荐直接使用不带变量绑定的 **可选 catch 绑定**（Optional Catch Binding）形式（即 `try { ... } catch { // ignore }`，而不是 `catch (error)`），以防止触发未使用变量 `error` 的编译报错。

### 3.5 代码干炼与纯粹取值规范
- **禁止滥用“或”等冗余兼容降级逻辑**：编写代码时保持最干净直接的赋值、比较与映射，严禁在没有明确需求时随意添加 `||` 兜底降级、多重兼容判断或后置备选（例如 `item.value === val || item.label === val` 或 `config?.label || val` 或 `label: matchedDict ? matchedDict.key : baseItem.label`）。
- **纯粹性原则**：代码只保留最纯粹精准的目标表达，除非有特殊业务场景确实需要兼容，否则禁止擅自加入冗余兜底与后置降级处理。

### 3.6 代码注释规范
- **必须添加关键中文注释**：编写新代码或重构代码时，必须在核心业务逻辑、关键数据取值、重要函数定义及 API 调用处加上清晰简炼的中文注释。
- **拒绝啰嗦**：注释内容必须准确直达要点，严格禁止编写与实际逻辑无关的冗长描述或无用啰嗦文本。

---

## 4. TypeScript 与模块导入规范

### 4.1 TypeScript 开发要求
- 本项目对 TypeScript 的使用**并非严格模式**（在 [apps/qz-fe/tsconfig.app.json](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/tsconfig.app.json) 中配置了 `strict: false`）。
- 别名配置：`@/*` 统一指向 `./src/*` 目录。
- **开发建议**：
  - 优先复用项目 [src/types](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/types) 中已有的类型定义。
  - 对于纯类型的导入，优先使用 `import type`。
  - 避免对不相关的代码进行无意义的类型重构或强行开启 strict 模式。
  - 在编写新代码时，应尽可能使用更精准的类型，但需与本地现有代码风格保持一致。
  - 注意：ESLint 中禁用了 `@typescript-eslint/no-explicit-any`，允许使用 `any`，但原则上不要引入不必要的 `any`。

### 4.2 模块导入路径约定
- 引用应用内模块时，统一使用 `@/` 别名从 `src` 目录开始引入（如 [src/main.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/main.ts) 中常见的引用方式）。
- 对于邻近的同级或子级模块，可以使用相对路径（如 `./` 或 `../`）进行引入。
- 纯类型文件引入时使用 `import type`。
- 样式入口文件和 [src/main.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/main.ts) 中允许包含副作用导入。

---

## 5. Vue 与组件开发规范

### 5.1 组件结构与位置
- 页面入口组件统一存放在 [src/views](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/views) 目录下（形如 `src/views/**/index.vue`）。
- 共享的全局组件存放在 [src/components](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/components) 目录下。
- 多数单文件组件（SFC）应使用 `<script setup lang="ts">` 语法糖。
- 组件的局部样式统一使用 `scss` 进行编写。
- 全局组件的自动注册在 [src/components/index.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/components/index.ts) 中处理。
- 项目中配置了 Vite 插件，实现了部分 Vue 和 Element Plus 常用 API 的自动导入。

### 5.2 Naming 命名规范
- 组件文件名应采用 **PascalCase** 风格，或者使用包含 `index.vue` 的目录命名。
- 自定义 Hooks/Composables 命名统一使用 `useXxx` 格式。
- Pinia Stores 命名统一使用 `useXxxStore` 格式.
- 布局组件推荐使用 `L*` 前缀（如 `LHeader`）。

### 5.3 规则放宽
- 项目放宽了 `vue/multi-word-component-names` 规则（允许单单词组件命名）。
- 项目放宽了 `vue/no-mutating-props` 规则（允许在子组件中修改 props，但如无必要仍不建议这么做）。

---

## 6. 状态管理规范

- 项目的状态管理采用 **Pinia**。
- 推荐使用选项式 Store 风格（定义 `state`, `getters`, `actions`）。
- 部分 Stores 支持数据持久化（配置了 `persist`）。
- 典型案例参考：
  - [menu.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/stores/layout/menu.ts)
  - [login/index.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/stores/login/index.ts)
  - [common.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/stores/common.ts)
- 在处理持久化数据和临时状态时，LocalStorage 和 SessionStorage 是常规使用手段。

---

## 7. 路由与菜单架构规范

- 项目采用**菜单驱动的动态路由**生成机制。
- 视图组件的扫描注册使用 `import.meta.glob('@/views/**/index.vue')` 实现。
- 所有的路由表均根据后端返回的菜单数据动态生成。
- 动态路由的核心注册逻辑在 [src/utils/route-utils.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/utils/route-utils.ts) 中统一处理，其中注册和清理路由均被包裹在 `try/catch` 块中以处理异常。
- **开发建议**：
  - 新增页面时，应在 [src/views](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/views) 目录下创建符合 `index.vue` 结构的文件。
  - 不要手动在本地硬编码无关的路由配置，以确保页面能够正常走后台菜单动态注册逻辑。

---

## 8. 接口响应与通知提示规范

### 8.1 接口请求封装
- 所有的 HTTP 请求逻辑应统一封装在 [src/service/request/index.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/service/request/index.ts) 中。
- 优先复用 `@/service` 中导出的现有请求实例，API 请求定义统一放在 `src/api/**/index.ts` 中。
- 当接口返回 `401` 状态码时，会清理本地存储并重定向到 `/login` 登录页面。
- 请勿引入额外的、不符合现有请求约定的网络库或自定义请求样式。

### 8.2 禁止直接使用原生的 `ElMessage`
- 所有的成功、失败、警告、常规消息提示，**严禁使用**原生的 `ElMessage`，必须统一使用从 [src/utils/response.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/utils/response.ts) 中引入的 `ElNotification`。
- 该 `ElNotification` 是经过全局统一封装的组件，默认会开启关闭按钮（`showClose: true`），并将自动关闭延时统一设为了 `2000ms`。

### 8.3 强制使用 `checkResponse` 进行业务校验
- 校验 API 接口返回状态时，**必须统一使用** [src/utils/response.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/src/utils/response.ts) 中的 `checkResponse` 函数。
- **严禁手动编写** `res.code === '0'` 或类似的比较逻辑。应直接将整个响应对象传入 `checkResponse(res)` 中进行判定。
- `checkResponse(res)` 会在接口返回失败（或者发生业务错误）时自动返回 `true`，并自动弹出包含后端 `msg` 的错误提示弹窗；而在接口成功时，该函数会返回 `false`，此时由前端继续编写成功的业务逻辑。

### 8.4 示例代码
#### 引入方式
```typescript
import { ElNotification, checkResponse } from '@/utils/response'
```
#### 列表查询示范
```typescript
const getTableData = async () => {
  const res = await getLeaveTypePage(pageParams)
  // 如果 checkResponse 返回 false，表示接口请求成功，进行数据绑定
  if (!checkResponse(res)) {
    tableData.value = res.data.data
  }
}
```
#### 表单提交保存示范
```typescript
const handleSubmit = async () => {
  try {
    const res = await createLeaveType(form.value)
    // 校验接口是否成功
    if (!checkResponse(res)) {
      ElNotification.success('添加成功')
      dialogVisible.value = false
      getTableData()
    }
  } catch (err: any) {
    ElNotification.error(err?.msg || '操作失败')
  }
}
```

---

## 9. UI 界面布局与排版规范（仅适用于分页列表布局）

在实现标准的列表管理页面时，为保证多设备自适应和排版一致性，应当统一高度和结构配置：

### 9.1 外容器与弹性盒布局
- 页面最外层容器（例如 `.holiday-type-container` 或是 `.target-sales`）必须设置高度为 `100%`，且设置 `overflow: hidden;` 防止双滚动条：
  ```scss
  .page-container {
    height: 100%;
    display: flex;
    flex-direction: column;
    background-color: #ffffff; /* 页面背景色统一为白色 */
    overflow: hidden;
  }
  ```

### 9.2 搜索与分页组件的样式
- **不要** 在页面样式中为 `CusSearch` 搜索组件或 `CusPagination` 分页组件重写或硬编码任何样式（高度、边距等），直接使用其内置的默认外观。

### 9.3 数据表格自适应占满剩余高度
- 数据表格的外包裹容器（例如 `.table-wrap`）应当使用 flex 伸缩属性撑满页面剩余的所有垂直高度：
  ```scss
  .table-wrap {
    flex: 1;
    min-height: 0;
  }
  ```
- 在包裹容器内的 `CusTables` 表格组件属性中，高度必须统一传入 `height="100%"`：
  ```html
  <div class="table-wrap" v-loading="loading">
    <CusTables
      ref="tableRef"
      :data="tableData"
      :columns="columns"
      height="100%"
      row-key="id"
    >
      <!-- 列插槽内容 -->
    </CusTables>
  </div>
  ```

### 9.4 新增与修改弹窗组件 (`CusDialog`)
- 点击“新增”或“修改”等交互操作需要弹出对话框时，必须统一使用自定义组件 `CusDialog` 替代原生的 `el-dialog`。
- **不要** 为 `CusDialog` 传 `size="default"` 或是设置任何自定义宽度样式属性，也不要在组件页面内为其重写任何 CSS，直接使用组件的默认外观。
- **必须** 在 `CusDialog` 组件的默认插槽内部套上一个统一外容器 `<div style="padding: 24px 20px">`，表单等业务元素均放置于该容器内部以保证边距规范统一。
- **强制分列（一行两排）栅格布局**：若弹窗内有多项表单字段，必须在 `el-form` 上配置 `label-position="top"`（Label 位于输入框上方）。同时，使用 `el-row :gutter="20"` 和 `el-col :span="12"` 将字段划分为对称的两列布局（一行两个表单项），以提升视觉张力和空间利用率。
- **特别注意弹窗组件 Scoped 样式穿透限制**：由于自定义组件 `CusDialog` 会被 Teleport 挂载在 body 元素下（脱离了当前页面容器组件的子孙树结构），所以在当前页面的 `<style scoped>` 样式中，任何使用 `:deep()` 对弹窗子代组件的深度选择器都**不会**匹配生效。必须通过为 `CusDialog` 传入特定的自定义类名（如 `class="holiday-type-dialog"`），并在不带 scoped 的全局 `<style lang="scss">` 样式块内，对该类名下的子代组件样式进行重置，以避免样式穿透失效或污染其他全局弹窗。

### 9.5 字段规则辅助提示与公用组件 `CusTooltip`
- 若表单输入字段右侧（或单选选项后方）包含规则说明、示例文字或链接，**严禁** 在表单中平铺书写大段灰字文案。必须使用项目公用组件 `CusTooltip`（基于 `el-tooltip` 和 `SvgIcon` 封装）以问号图标悬停浮层的方式来呈现。
- 若提示文字属于某个特定单选按钮（`el-radio`），应将 `CusTooltip` 直接放入 `el-radio` 标签内部，紧跟文字后面展示：
  ```html
  <el-radio value="hour">
    <span>按小时</span>
    <CusTooltip display-tooltip="这里是说明文字内容" />
  </el-radio>
  ```
  同时，为使选项文字与问号图标在视觉重心中达到绝对水平对齐，`CusTooltip` 内部已对图标组件设置了 `transform: translateY(-1.5px)` 的微调位移。且在页面局部样式中，需增加对 `.el-radio__label` 的 flex 垂直居中控制：
  ```scss
  :deep(.el-radio) {
    .el-radio__label {
      display: inline-flex;
      align-items: center;
    }
  }
  ```
- **禁止单选组件携带 padding**：为了保持单选表单项紧贴表单基准线，严禁 `el-radio-group` 携带有任何内边距。在不带 scoped 的全局 `<style lang="scss">` 样式块中对特定 Dialog 下的 `el-radio-group` 显式进行重置并赋予最高优先级：
  ```scss
  .holiday-type-dialog {
    .el-radio-group {
      padding: 0 !important;
    }
  }
  ```
- 若提示文字属于普通输入项的单位或整体规则，推荐配合 `.flex-row-item` 进行横向弹性排版：
  ```html
  <el-form-item label="单位换算">
    <div class="flex-row-item">
      <span>1天 = </span>
      <el-input v-model="form.dayEquivalentHours" style="width: 80px; margin: 0 4px;" clearable oninput="value=value.replace(/[^\d]/g,'')" />
      <span> 小时</span>
      <CusTooltip display-tooltip="说明文字内容" />
    </div>
  </el-form-item>
  ```
  并在页面局部样式中使用统一的 CSS 类名控制：
  ```scss
  :deep(.flex-row-item) {
    display: flex;
    align-items: center;
    width: 100%;
    gap: 12px;
    .tip-text {
      font-size: 12px;
      color: var(--el-text-color-secondary);
      flex: 1;
    }
    .link-text {
      color: var(--el-color-primary);
      text-decoration: none;
    }
  }
  ```

### 9.6 严禁在模板中随意编写硬编码行内样式 (style)
- **硬编码行内样式限制**：在 Vue 模板中，严禁随意在 HTML / 组件标签上直接使用 `style="..."` 的硬编码行内样式（例如常见的 `style="margin-bottom: 0; flex: 1;"`）。所有界面排版、对齐、长宽比例控制以及外边距样式，必须统一抽取到 CSS / SCSS 样式块中，通过相应的 CSS 类名进行配置。
- **特例排除**：只有在用户有明确且特别的要求，或是属性完全依赖 Vue 的动态双向绑定（如 `:style="{ left: positionX + 'px' }"` 等纯动态计算定位场景）时才允许使用，其余静态排版样式一律禁止使用 style。

### 9.7 缺省空状态规范
- **标准空数据嵌套结构**：在实现或展示暂无数据的缺省状态（空状态）时，必须统一使用如下 HTML 嵌套结构：
  ```html
  <div class="empty-wrap">
    <img src="@/assets/images/common/no-data.svg" class="empty-img" />
  </div>
  ```
- **使用全局通用样式大小**：项目已经在全局 `common.scss` 中统一定义了 `.empty-wrap`（弹性垂直居中）样式，并在其内部嵌套了 `.empty-img` 类的样式（宽度 80px，高度 120px）。开发时使用对应的嵌套结构即可，严禁在各业务页面或组件样式中重复定义该类名或手写其样式。
- **禁止重复展示“暂无数据”文案**：由于该缺省图片中已经包含了“暂无数据”等文字信息，严禁在 HTML 模板中额外重复编写如 `<span class="empty-text">暂无数据</span>` 或其他文本说明标签，以防出现界面文字冗余和重复提示。

### 9.8 表格操作列 (Operations Column) 宽度控制规范
- **操作列宽度规则**：当操作栏 `isMore` 为 `false`（展示 2 个按钮，如编辑、删除）时，操作列宽度亦固定设置为 `112` (`112px`)；当 `isMore` 为 `true`（展示 3 个及以上操作或包含更多下拉）时，宽度亦固定为 `112` (`112px`)。

### 9.9 抽屉组件 (`CusDrawer`) 底部操作按钮规范
- **取消与确定按钮尺寸规范**：在 `CusDrawer` 的 `#footer` 底部插槽中，取消和确定按钮统一配置 `size="large"`，并通过 `.drawer-footer-btns` 容器包裹居中展示。
- **按钮样式排版（需在非 scoped 的 `<style lang="scss">` 或全局样式中书写，以支持 `append-to-body` 挂载穿透）**：
  ```scss
  .drawer-footer-btns {
    display: flex;
    justify-content: center;
    align-items: center;

    .footer-btn {
      width: 160px !important;
      height: 40px !important;
      font-size: 14px;
      border-radius: 4px;
    }
  }
  ```

### 9.10 文本域表单项 (Textarea) 交互与尺寸规范
- **通用规范（无特殊要求场景）**：在表单中使用 `el-input type="textarea"` 时，若无特殊业务场景要求，必须统一遵守以下设置：
  1. **高度固定为 140px**：配置 `:input-style="{ height: '140px' }"` 将输入框高度固定为 `140px`；
  2. **禁用右下角拖拽**：配置 `:resize="'none'"` 禁用拖拽改变文本域尺寸；
  3. **限制长度为 100 字符**：配置 `maxlength="100"` 并开启 `show-word-limit` 字数统计。

---

## 10. 接口数据与 YAPI 调试规范

### 10.1 获取 YAPI 接口定义的方法
- 接口详情查询地址格式：`https://yapi.jufulaw.com/api/interface/get?id=<id>&token=<token>`，通过更换接口 `id` 获取具体的请求与响应参数信息。
- **关于 token 的使用与配置规范**：
  1. 严禁在任何执行代码、拉取脚本或工具命令中硬编码（写死）`token` 值。
  2. 接口拉取所用的 `token` 必须统一在外部配置文件中进行存储读取，或从环境变量中获取。
  3. 当执行接口拉取遇到未授权报错（或 `token` 缺失/过期失效）时，Agent 必须主动通过询问向用户索要最新有效的 `token`，并在得到输入后更新配置。
- 当用户提供了类似 `https://yapi.jufulaw.com/project/209/interface/api/12628` 的接口页面地址，或者在页面源码 `<a class="interface-item" href="/project/209/interface/api/12628">` 中，`api/` 之后的数字（例如 `12628`）即代表该接口的 `id`，可用以拼接查询具体的接口定义信息。

### 10.2 接口传参的数据类型处理规范
- **禁止随意进行类型强转**：严禁在调用 API 接口并发送数据载荷之前，擅自对参数字段进行 `Number()` 或 `String()` 等强制类型转换，除非后端接口有明确指明或用户特别要求。
- **保持原始类型**：应当保持参数的原始取值类型（保留 `v-model` 或响应式绑定得到的原本类型），防止改变数据的真实结构导致后端接收和处理出错。

### 10.3 时间字段格式化处理规范
- **强制使用通用工具函数**：所有界面展示的日期与时间字段，必须统一使用 `@/utils/date` 中导出的 `formatDate` 方法进行格式化展示（例如 `formatDate(val)` 或 `formatDate(val, 'yyyy-MM-dd HH:mm:ss')`），严禁在组件中手写正则表达式、字符串截取或自定义 `new Date()` 解析逻辑。

---

## 11. 错误处理与日志规范

- 错误处理原则是实用而非严苛，常使用 `try/catch` 来包裹可恢复的路由或菜单加载操作。
- 应当使用守护性（guard）子句以及回退值机制，来规避数据可能缺失带来的崩溃。
- 将对用户可见的请求失败信息向上抛出，而不要默默吞掉那些周围代码已经准备妥当要报告的报错。
- `console` 调试语句允许存在于源码中，ESLint 不会拦截。但 [vite.config.ts](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe/vite.config.ts) 会在生成生产打包产物时移除 `console` 和 `debugger`。

---

## 12. 遗留/低规范领域开发须知

- 项目中部分与 JS 强关联的领域（特别是 BPMN 流程设计器、流程流转及部分老校验文件夹）的规范管理相对宽松，可能不符合最新的 lint 或 TS 校验规则。
- 在这些区域修改代码时：
  - 请先观察临近现有文件的编写习惯。
  - 优先采取改动最小的方案，避免随意重构。
  - 不要盲目假设当前目录已经具备了最现代化的模式。

---

## 13. 提交与 Hook 规范

- **Git 提交限制（核心原则）**：开发助手在开发完成后，**默认不要主动执行** `git add`、`git commit` 或 `git push` 等 Git 仓库提交推送操作。所有的代码提交流程和版本控制操作必须交由用户亲自执行，除非用户在指令中有明确的特别声明。
- 提交代码时，请遵循 [commitlint.config.ts](file:///d:/project/qz-platform-tenant-ui/commitlint.config.ts) 配置的 Conventional Commits 提交规范。常用的提交前缀类型包括：
  - `feat`, `fix`, `perf`, `docs`, `style`, `refactor`, `test`, `build`, `ci`, `chore`
- 挂钩（lint-staged）在 pre-commit 时会执行：
  - `*.{ts,tsx,js,jsx,vue}` 文件：先执行 `eslint --fix` 修复，再执行 `prettier --write` 格式化。
  - `*.{json,css,scss,less,md}` 文件：执行 `prettier --write` 格式化。

---

## 14. 针对未来 Agent 的实用开发指导

1. 优先阅读并复制与修改区域最近的现有文件开发模式，切勿过度设计。
2. 每次编写或修改完代码后，必须检查并清理未使用的 import 引入以及未使用的声明变量/参数，确保无冗余代码。
3. 运行应用启动、打包等命令必须进入 [apps/qz-fe](file:///d:/project/qz-platform-tenant-ui/apps/qz-fe) 路径，不要在项目根目录下调用。
4. 代码改动后，应通过 `pnpm type-check` 和 `pnpm lint` 校验其正确性。
5. 绝对路径导入请使用 `@/` 前缀。
6. 保持本项目的格式化风格：无分号、单引号、2 空格缩进。
7. 在造新的轮子前，先校验并复用现有的类型定义、Pinia 状态存储和请求工具。
8. 尊重项目菜单驱动的动态路由设计 and 统一的 API 请求拦截拦截层，不要另立炉灶。
9. 优先以实际配置文件和已有代码为准，[.cursor/rules/vue3.mdc](file:///d:/project/qz-platform-tenant-ui/.cursor/rules/vue3.mdc) 的规则仅作为辅助参考。

---

## 15. UI 设计 Token 与样式视觉规范

为保证平台各模块 UI 视觉语言的高度一致与精致统一，UI 开发必须遵守以下 Design Tokens 规范：

### 15.1 颜色 Token
- **主色 (Primary)**：`#0E92F8`
- **引导色 (Success/Guide)**：`#29B597`
- **提醒色 (Warning/Tip)**：`#FD9317`
- **警告色 (Danger/Error)**：`#F66060`
- **字体颜色**：`#333` (主要/标题)、`#666` (常规/次要)、`#999` (辅助/失效)、`#0E92F8` (高亮/选中/链接)
- **背景色**：`#fff` (卡片/容器/主体)、`#fdfdfd` (折叠面板展开内容区)、`#fafafa` (折叠面板 Header/浅灰背景)
- **边框色**：`#f4f4f4` (通用卡片与容器边框)

### 15.2 交互与选中/Hover 样式规范
- **选中状态 (Active/Selected)**：使用主色 `#0E92F8`
- **菜单/分组项 Hover 样式**：
  ```scss
  border-radius: 0 8px 8px 0;
  background: linear-gradient(90deg, #fafafa 0%, #e7ecf9 100%);
  color: #0E92F8;
  ```
- **蓝色垂直 Indicator 条 (blue-bar)**：
  - 尺寸：宽 `4px`、高 `14px` 或 `20px`
  - 背景色：主色 `#0E92F8`
  - 圆角：禁用圆角 (`border-radius: 0`)

### 15.3 字号排版规范
- **小号字 (Tag/Caption)**：`12px`
- **常规字 (Body/Label/Menu)**：`14px`
- **中大号字 (Title/Group Title)**：`16px`
