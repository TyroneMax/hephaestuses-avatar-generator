# DiceBear 头像生成器 (PrimeVue + TailwindCSS v4)

Click to switch to English documentation: [README.md](README.md)

## 项目概述

这是一个基于 DiceBear API 的交互式头像生成器，集成了 PrimeVue 和 TailwindCSS
v4。项目允许用户选择不同的头像风格、自定义颜色和各种属性，实时预览并生成可用的头像。背景颜色会随着头像颜色动态变化，提供和谐的视觉体验。

## 功能特点

- 支持 30 多种 DiceBear 头像风格
- 实时头像预览和生成
- 动态背景颜色调整
- 高度可定制的头像参数
    - 背景颜色选择
    - 颜色选择器支持
    - 各种头像属性调整
- 响应式设计
- 生成可分享的头像 URL

## 技术栈

- [Vue.js](https://vuejs.org/) - 渐进式 JavaScript 框架
- [PrimeVue](https://primevue.org/) - Vue UI 组件库
- [TailwindCSS v4](https://tailwindcss.com/) - 实用优先的 CSS 框架
- [DiceBear](https://dicebear.com/) - 开源头像生成 API
- [Vite](https://vitejs.dev/) - 下一代前端工具

## 组件结构

项目主要包含以下组件：

- `App.vue` - 根组件，管理应用程序状态和背景色
- `AvatarSelector.vue` - 头像选择和配置的主要组件
    - 支持样式选择
    - 背景颜色选择
    - 特定风格的属性自定义

## 开始使用

1. 克隆仓库：

```bash
git clone https://github.com/yourusername/dicebear-avatar-generator.git
cd dicebear-avatar-generator
```

2. 安装依赖：

```bash
npm install
```

3. 启动开发服务器：

```bash
npm run dev
```

4. 构建生产版本：

```bash
npm run build
```

## 使用指南

1. 在样式选项卡中选择头像风格
2. 使用背景颜色选项卡自定义背景
3. 根据所选风格，在其他选项卡中调整具体属性
4. 点击生成按钮获取可分享的 URL

## 依赖项

- @dicebear/collection - DiceBear 头像集合
- @dicebear/core - DiceBear 核心库
- PrimeVue 组件 (Tabs, Button, ColorPicker, Slider 等)
- TailwindCSS v4 用于样式

## 贡献

欢迎提交问题和功能请求！如需贡献，请先开 issue 讨论您想要更改的内容。

## 许可

[MIT License](LICENSE)
