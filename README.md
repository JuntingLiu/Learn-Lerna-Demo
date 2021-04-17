# Lerna 学习

通过简单的项目 Demo 熟悉 Lerna 管理项目的流程和基本使用。

## 目录

```js
├── README.md
├── lerna.json        // 配置文件
├── package.json
└── packages          // 包目录
   ├── common         // 公共库
   ├── mobile-component // 移动端组件库
   └── web-component    // web 端组件库
```

## 流程

### 初始化项目

```shell
lerna init
```

### 子项目创建
#### common

内部公共使用通用库

```shell
lerna create common
```

#### web-component

采用的技术栈为 `React` + `TypeScript`。

```shell
cd packages

npx create-react-app web-component --template typescript
```

#### mobile-component

采用技术栈为 `Vue3` + `TypeScript`, 采用 `Vite` 来进行构建。

```shell
yarn create @vitejs/app mobile-component
```

> 当前项目都是基于 TypeScript 进行开发，但项目都是通过脚手架配套生成，所以相关依赖都安装了。但 common 库还是 js 的，就需要额外的安装依赖了。其typescript、@types/node 被各个项目都依赖，所以把其提取到根 node_modules 下。

```shell
yarn add typescript @types/node
```
