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

### Lerna bootstrap

两个 `web-component` 和  `mobile-component` 项目创建后，二者会由很多重复的部分，并且会占用大量硬盘空间，通过此命令可以**将子项目的依赖包提升到最顶层**。

```shell
# 先清除下子项目的 node_modules
lerna clean

# 再执行,将 `packages` 目录下的公共模块包抽到最顶层。
lerna bootstrap --hoist
```

**缺点：** 针对公共模块抽离到最顶层，**不同版本号只会保留使用最多的**， 这种配置不太好，当项目中有些功能需要依赖老版本时，这种方式就会出问题。
**解决办法：** 使用命名空间的方式，来解决不同版本的问题。`yarn workspaces` 会检查每个子项目里面的依赖及其版本，如果版本不一致都会保留到自己的 `node_modules` 中，只有依赖版本号一致的时候才会被提升到顶层。

1. 在 `lerna.json` 中增加配置。

```json
"npmClient": "yarn",   // 指定 npm client
"useWorkspaces": true // 开启命名空间
```

2. 根目录 package.json 中增加配置。


```json
{
   "workspaces": [
      "packages/*"
   ]
}
```

配置完后，不再需要 `lerna bootstrap` 来安装依赖了，可以直接使用 `yarn install` 进行安装。注意，`yarn install` 无论在根目录还是项目里执行效果都是一样的。

### 启动命令

根目录 `package.json` 里进行配置, 这样就不需要跑到该项目下执行特定的命令了。

```json
  "scripts": {
    "web": "lerna exec --scope web-component yarn start",
    "mobile": "lerna exec --scope mobile-component yarn dev"
  },
```

### 引用公共库中的函数

```shell
lerna add common --scope=web-component
```

执行完后，在 `web-component` 项目下的 `package.json` 就会出现`common`依赖，已被成功引入。就可以在 `web-component` 库中尽情使用了。
