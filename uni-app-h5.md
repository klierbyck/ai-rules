---
trigger: always_on
---

# Uni-app H5 项目开发规范

本规范旨在整合 **uni-app** 与 **Vue 3** 的最佳实践，确保 H5 项目在多人协作、设计还原及性能优化方面保持高质量与高一致性。

---

## 一、 SFC 代码文件结构顺序规范

所有页面及组件文件（.vue）必须严格遵循以下单文件组件 (SFC) 块排列顺序：

1. `<template>`
2. `<script setup lang="ts">`
3. `<style scoped lang="scss">`

---

## 二、 项目核心架构规范

### 1. 目录结构 (模块化规范)
项目采用模块化管理方式，各资源按业务模块进行拆分，确保解耦与清晰：

| 资源类型 | 存放路径 | 说明 |
| :--- | :--- | :--- |
| **公共组件** | `src/components/模块名/` | 全局可复用的基础或业务组件。 |
| **业务页面** | `src/pages/模块名/` | 各业务模块的入口页面。 |
| **业务组件** | `src/pages/模块名/components/` | 仅限该页面模块内部使用的局部组件。 |
| **工具函数** | `src/utils/` | 通用的逻辑处理封装（如正则、存储等）。 |
| **接口定义** | `src/api/模块名/` | API 请求定义，按模块拆分 `index.ts`。 |
| **状态管理** | `src/store/模块名/` | Pinia Store，按业务模块划分状态。 |
| **全局 TS 定义** | `src/types/模块名/` | 全局 TypeScript 接口与类型定义。 |
| **静态资源** | `src/static/` | 公共图片、图标与字体文件。 |

---

## 三、 技术栈约束

| 维度 | 规范要求 |
| :--- | :--- |
| **框架核心** | Vue 3，必须使用 `<script setup>` 语法糖。 |
| **类型安全** | TypeScript，严禁使用 `any`；API 请求和组件 Props 必须定义 `interface`。 |
| **状态管理** | Pinia，遵循模块化定义，推荐使用 `setup` 模式。 |
| **UI 组件库** | **Vant** (H5 标准版)。 |
| **样式语言** | 使用 SCSS 和嵌套语法，利用全局变量与 Mixins 提高开发效率。 |

---

## 四、 设计还原与样式规则 (Figma 优先)

### 1. 设计还原策略
在使用 Figma MCP 辅助开发时，遵循以下优先级：

1. **精确解析**：通过 MCP 提取图层的精确数值（Padding, Margin, Color, Font-size, Border-radius）。
2. **组件优先**：如果设计稿中的组件与 Vant 核心功能一致，必须优先使用 Vant 组件。
3. **样式覆盖**：使用 SCSS `:deep()` 或 CSS 变量覆盖 Vant 默认样式以符合设计稿。
4. **手动封装**：若 Vant 组件无法满足复杂交互或特殊结构，则手动编写自定义组件。

### 2. 样式规范 (SCSS)
- **变量化**：将 Figma 中的主题色、辅助色、间距定义在 `uni.scss` 或全局变量中。
- **单位标准**：
  - **字号 (font-size)**、**外边距 (margin)**、**内边距 (padding)**、**宽高 (width/height)** 必须使用 `rpx` 单位。
  - **转换比例**: 设计稿 1px -> 代码 2rpx (针对 375px 设计稿)。
  - **1px 豁免**: 涉及到 **1px** 的数值（通常为边框或极细线）**直接使用 `px`**，无需转换，以确保在不同屏幕上渲染清晰，避免出现 0.5rpx 渲染问题。
- **布局指南**：
  - **优先使用 Grid (格点布局)** 处理复杂容器或重复项，提高代码可读性和布局灵活性。
  - **示例代码**:
    ```css
    .container {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 20rpx;
      padding: 24rpx; /* 使用 rpx */
      margin: 16rpx;  /* 使用 rpx */
      font-size: 28rpx; /* 使用 rpx */
      border: 1px solid #ccc; /* 边框保持 px */
    }
    ```
- **BEM 命名**：自定义组件遵循 `block__element--modifier` 命名法，避免样式冲突。

### 3. CSS 变量使用规范

> [!IMPORTANT]
> 页面中如有与 `styles/vant-theme.scss` 中定义的色值相同的颜色，**必须使用 CSS 变量**，禁止硬编码色值。

**变量对照表**：

| 色值 | CSS 变量 | 用途 |
| :--- | :--- | :--- |
| `#FBFBFB` | `var(--cus-bg)` | 自定义背景色 |
| `#000` | `var(--van-black)` | 黑色 |
| `#fff` | `var(--van-white)` | 白色 |
| `#f7f8fa` | `var(--van-gray-1)` | 灰度 1 |
| `#f2f3f5` | `var(--van-gray-2)` | 灰度 2 |
| `#EEEEEE` | `var(--van-gray-3)` | 灰度 3 |
| `#dcdee0` | `var(--van-gray-4)` | 灰度 4 |
| `#c8c9cc` | `var(--van-gray-5)` | 灰度 5 |
| `#999999` | `var(--van-gray-6)` | 灰度 6（辅助文字） |
| `#666666` | `var(--van-gray-7)` | 灰度 7（次要文字） |
| `#333333` | `var(--van-gray-8)` | 灰度 8（主要文字） |
| `#F66060` | `var(--van-red)` | 红色（危险/错误） |
| `#0E92F8` | `var(--van-blue)` | 蓝色（主色） |
| `#F4A241` | `var(--van-orange)` | 橙色（警告） |
| `#29B597` | `var(--van-green)` | 绿色（成功） |

**正确示例**：
```scss
.card {
  background: var(--van-white);
  color: var(--van-gray-8);
  border: 1px solid var(--van-gray-3);
  
  &__hint {
    color: var(--van-gray-6);
  }
}
```

**错误示例**：
```scss
// ❌ 禁止硬编码已定义的色值
.card {
  background: #fff;
  color: #333333;
  border: 1px solid #EEEEEE;
}
```

---

## 五、 代码示例与最佳实践

### 1. 组件开发模版 (Vue 3 + TS)

```vue
<template>
  <view class="custom-card">
    <text class="custom-card__title">{{ props.title }}</text>
    <van-button 
      class="custom-card__btn" 
      type="primary" 
      @click="handleClick"
    >
      确认 ({{ status }})
    </van-button>
  </view>
</template>

<script setup lang="ts">
import { ref } from 'vue'

// 1. 定义 Props 和 Emits
interface Props {
  title: string
  status?: 'active' | 'inactive'
}

const props = withDefaults(defineProps<Props>(), {
  status: 'active'
})

const emit = defineEmits<{
  (e: 'click', id: number): void
}>()

// 2. 逻辑处理
const handleClick = () => {
  emit('click', Date.now())
}
</script>

<style scoped lang="scss">
.custom-card {
  padding: 15px; 
  border-radius: 8px;
  background: var(--van-white);
  
  &__title {
    font-size: 16px;
    color: var(--van-gray-8); 
    font-weight: bold;
  }

  :deep(.van-button) {
    margin-top: 10px;
    background: linear-gradient(to right, #4facfe, #00f2fe);
    border: none;
  }
}
</style>
```

---

## 六、 H5 性能与适配优化

1. **按需引入**：使用 `unplugin-vue-components` 实现 Vant 自动按需引入，减少首屏体积。
2. **路由懒加载**：确保大项目模块拆分合理，减少初始 JS 加载压力。
3. **移动端适配**：
    - 针对刘海屏、底栏安全区域，使用 `safe-area-inset-*` 相关 CSS 变量。
    - 确保手势交互（滑动、点击放缩）在主流移动端浏览器（Safari, Chrome）中表现一致。
4. **资源压缩**：静态资源（尤其是图片）必须经过压缩，或托管于 CDN。

---

## 七、 开发工作流总结

1. **设计输入**：通过 Figma 解析组件参数。
2. **组件拆解**：优先 Vant -> 覆盖样式 -> 无法满足则自定义。
3. **类型先行**：先定义 `interface` 和 API 数据结构，再写业务逻辑。
4. **调试验证**：
    - **Chrome 模拟器**：主要开发与调试环境。
    - **真机扫码**：确保真机浏览器下的渲染与手势无误。
5. **还原度检查**：检查设计稿 1:1 还原情况，特别是在不同屏幕宽度下的适配效果。
6. **git**：没有明确要求不要进行 git 操作。

---

## 八、 AI 维护与规范演进说明

> [!IMPORTANT]
> **AI 维护规范**：
> 1. 当用户提到“规范”、“约定”或“开发规则”相关的修改建议时，AI 助理必须**优先更新并同步**此文档。
> 2. 本文件作为项目开发的核心契约，AI 在生成代码或建议架构时应严格遵循此处定义的规范。
> 3. 若规范发生冲突，以本文件的最新版本为准。

---

## 九、 接口数据与 YAPI 调试规范

### 1. 获取 YAPI 接口定义的方法
- 接口详情查询地址格式：`https://yapi.jufulaw.com/api/interface/get?id=<id>&token=<token>`，通过更换接口 `id` 获取具体的请求与响应参数信息。
- **关于 token 的使用与配置规范**：
  1. 严禁在任何执行代码、拉取脚本或工具命令中硬编码（写死）`token` 值。
  2. 接口拉取所用的 `token` 必须统一在外部配置文件中进行存储读取，或从环境变量中获取。
  3. 当执行接口拉取遇到未授权报错（或 `token` 缺失/过期失效）时，Agent 必须主动通过询问向用户索要最新有效的 `token`，并在得到输入后更新配置。
- 当用户提供了类似 `https://yapi.jufulaw.com/project/209/interface/api/12628` 的接口页面地址，或者在页面源码 `<a class="interface-item" href="/project/209/interface/api/12628">` 中，`api/` 之后的数字（例如 `12628`）即代表该接口的 `id`，可用以拼接查询具体的接口定义信息。

### 2. 接口传参的数据类型处理规范
- **禁止随意进行类型强转**：严禁在调用 API 接口并发送数据载荷之前，擅自对参数字段进行 `Number()` 或 `String()` 等强制类型转换，除非后端接口有明确指明或用户特别要求。
- **保持原始类型**：应当保持参数的原始取值类型（保留 `v-model` 或响应式绑定得到的原本类型），防止改变数据的真实结构导致后端接收和处理出错。
