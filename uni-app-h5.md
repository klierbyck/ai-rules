# Uni-app H5 项目开发规范

本规范旨在整合 **uni-app** 与 **Vue 3** 的最佳实践，确保 H5 项目在多人协作、设计还原及性能优化方面保持高质量与高一致性。

---

## 一、 项目核心架构规范

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

## 二、 技术栈约束

| 维度 | 规范要求 |
| :--- | :--- |
| **框架核心** | Vue 3，必须使用 `<script setup>` 语法糖。 |
| **类型安全** | TypeScript，严禁使用 `any`；API 请求和组件 Props 必须定义 `interface`。 |
| **状态管理** | Pinia，遵循模块化定义，推荐使用 `setup` 模式。 |
| **UI 组件库** | **Vant** (H5 标准版)。 |
| **样式语言** | SCSS，利用全局变量与 Mixins 提高开发效率。 |

---

## 三、 设计还原与样式规则 (Figma 优先)

### 1. 设计还原策略
在使用 Figma MCP 辅助开发时，遵循以下优先级：

1.  **精确解析**：通过 MCP 提取图层的精确数值（Padding, Margin, Color, Font-size, Border-radius）。
2.  **组件优先**：如果设计稿中的组件与 Vant 核心功能一致，必须优先使用 Vant 组件。
3.  **样式覆盖**：使用 SCSS `:deep()` 或 CSS 变量覆盖 Vant 默认样式以符合设计稿。
4.  **手动封装**：若 Vant 组件无法满足复杂交互或特殊结构，则手动编写自定义组件。

### 2. 样式规范 (SCSS)
- **变量化**：将 Figma 中的主题色、辅助色、间距定义在 `uni.scss` 或全局变量中。
- **单位使用**：
  - H5 端建议使用 `rpx` 以便 uni-app 自动转换，或根据项目需求使用 `rem` / `px`。
- **BEM 命名**：自定义组件遵循 `block__element--modifier` 命名法，避免样式冲突。

---

## 四、 代码示例与最佳实践

### 1. 组件开发模版 (Vue 3 + TS)

```vue
<script setup lang="ts">
import { ref } from 'vue';

// 1. 定义 Props 和 Emits
interface Props {
  title: string;
  status?: 'active' | 'inactive';
}

const props = withDefaults(defineProps<Props>(), {
  status: 'active'
});

const emit = defineEmits<{
  (e: 'click', id: number): void;
}>();

// 2. 逻辑处理
const handleClick = () => {
  emit('click', Date.now());
};
</script>

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

<style lang="scss" scoped>
.custom-card {
  padding: 30rpx; 
  border-radius: 16rpx;
  background: #fff;
  
  &__title {
    font-size: 32rpx;
    color: $uni-text-color; 
    font-weight: bold;
  }

  :deep(.van-button) {
    margin-top: 20rpx;
    background: linear-gradient(to right, #4facfe, #00f2fe);
    border: none;
  }
}
</style>
```

---

## 五、 H5 性能与适配优化

1.  **按需引入**：使用 `unplugin-vue-components` 实现 Vant 自动按需引入，减少首屏体积。
2.  **路由懒加载**：确保大项目模块拆分合理，减少初始 JS 加载压力。
3.  **移动端适配**：
    - 针对刘海屏、底栏安全区域，使用 `safe-area-inset-*` 相关 CSS 变量。
    - 确保手势交互（滑动、点击放缩）在主流移动端浏览器（Safari, Chrome）中表现一致。
4.  **资源压缩**：静态资源（尤其是图片）必须经过压缩，或托管于 CDN。

---

## 六、 开发工作流总结

1.  **设计输入**：通过 Figma 解析组件参数。
2.  **组件拆解**：优先 Vant -> 覆盖样式 -> 无法满足则自定义。
3.  **类型先行**：先定义 `interface` 和 API 数据结构，再写业务逻辑。
4.  **调试验证**：
    - **Chrome 模拟器**：主要开发与调试环境。
    - **真机扫码**：确保真机浏览器下的渲染与手势无误。
5.  **还原度检查**：检查设计稿 1:1 还原情况，特别是在不同屏幕宽度下的适配效果。

---

## 七、 AI 维护与规范演进说明

> [!IMPORTANT]
> **AI 维护规范**：
> 1.  当用户提到“规范”、“约定”或“开发规则”相关的修改建议时，AI 助理必须**优先更新并同步**此文档。
> 2.  本文件作为项目开发的核心契约，AI 在生成代码或建议架构时应严格遵循此处定义的规范。
> 3.  若规范发生冲突，以本文件的最新版本为准。