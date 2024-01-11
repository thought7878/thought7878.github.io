## 前言

相信很多人在业余时间都会去 Github 上看一些比较优秀的开源项目，通过学习这些项目的源码来提升自己，但是优秀的开源项目大部分都具有 **流程复杂** 和 **代码量大** 两大特点，如果没有一定的技巧，相信很多开发者会和笔者初期一样，在代码的海洋里迷失自己～

先上结论：**看代码最高效的方法是边调试、边记录、边看**。

由于不同的人具有不同的记录习惯，本文不会讨论这个话题。

## 调试是看源码的银弹

我们看源码时，通常会遇到以下的问题：

- 文件太多，在各个文件之间来回看代码导致混乱
- 代码分支太多，没有数据仅凭记忆和想象难以判断当前代码执行的分支
- 无法判断某个方法返回的结果，必须要查看方法源码才能有基本判断，导致需要消化大量代码才能了解流程
- ......

### 调试使我们更快了解整体流程

通过给定输入进行调试，我们可以轻松得知每一步执行返回的结果，而不需要关注方法内部的实现，这使得我们只需要关注少部分文件即可以了解代码整体执行流程。

### 调试使我们可以只关注局部实现

如果我们想了解某个方法的内部实现，可以直接在方法内部进行断点调试：通过给定数据进行输入并开始调试，编辑器会自动定位到断点位置，然后通过单步调试不断跟踪方法体每一行代码，知晓每一步代码执行的结果。

## 如何调试

由于笔者平时的开发编辑器是 VS Code，因此下文将主要描述在 VS Code 下的一些调试操作。

VS Code 已经集成了浏览器、Node.js 和 TypeScript 调试的能力，详细操作可以查看官方文档链接：[browser-debugging](https://link.juejin.cn?target=https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fnodejs%2Fbrowser-debugging "https://code.visualstudio.com/docs/nodejs/browser-debugging")、[nodejs-debugging](https://link.juejin.cn?target=https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fnodejs%2Fnodejs-debugging "https://code.visualstudio.com/docs/nodejs/nodejs-debugging")、[typescript-debugging](https://link.juejin.cn?target=https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Ftypescript%2Ftypescript-debugging "https://code.visualstudio.com/docs/typescript/typescript-debugging")。

接下来将以一些具体例子来带大家领略下调试的魅力。

### vscode调试预备知识

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2fe1cd7782e14904ad6f9954e4771009~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

VS Code 进行前端调试非常简单，主要需要以下四个步骤：

- 选中编辑器中的调试（如上图步骤1）
- 创建 `launch.json` 文件（如上图步骤2），完成这步操作后将会在源码根目录创建 `.vscode/launch.json`
- 配置 `launch.json`
- 启动调试

#### `launch.json`配置介绍

![QQ20210724-224928-HD.gif](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5dfcab17b7b24ad6b9d3e55205b29b0a~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

上图操作创建的 `launch.json` 如下所示：

```json
{
  // 使用 IntelliSense 了解相关属性。 
  // 悬停以查看现有属性的描述。
  // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "启动程序",
      "skipFiles": [
        "<node_internals>/**"
      ],
      "program": "${file}"
    }
  ]
}

```

我们只需要关注 `configurations` 里面的每一项配置：

- `type`：调试器类型，这里表示使用 `node` 来执行程序
- `request`: 代表如何启动调试器，有 `attach` 和 `launch` 两个值
  - `attach`: 如果 node 程序已经启动，则将调试附加到已经启动的程序中
  - `launch`: 使用调试器直接启动程序并且开始调试
- `name`: 该项配置的名称
- `skipFiles`: 调试忽略的文件，配置后单步调试将不会进入匹配的文件
- `program`: 程序启动入口

除了上述配置项，笔者认为还有还有几个配置项是值得大家关注的：

- `runtimeExecutable`: 使用什么命令启动调试，比方说上面的配置默认使用 `node` 来执行 `program` 的文件
- `args`: 启动时的参数
- `console`: 建议设置为 `integratedTerminal`，这样调试过程的信息会在 VS Code 自带终端中显示
- `restart`: 设置为 `true` 时，修改代码并保存后会自动重启调试
- `stopOnEntry`: 调试器启动后，是否在第一行代码处暂停，很方便的一个配置
- `port`: 调试使用的端口
- `cwd`: 调试器工作的根目录
- ...

更多参数可以查阅官方文档：

- [nodejs-debugging#_launch-configuration-attributes](https://link.juejin.cn?target=https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fnodejs%2Fnodejs-debugging%23_launch-configuration-attributes "https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_launch-configuration-attributes")
- [debugging#_launchjson-attributes](https://link.juejin.cn?target=https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Feditor%2Fdebugging%23_launchjson-attributes "https://code.visualstudio.com/docs/editor/debugging#_launchjson-attributes")

`launch.json` 中还包含了一些特定的模版变量，如上述 `program` 属性的值 `${file}`，这里也罗列一下各模版变量的意义：

- `${workspaceFolder}`: VS Code 中打开的文件夹目录 （通常是项目的位置）
- `${workspaceFolderBasename}`: 没有任何斜杠 (/) 的 VS Code 中打开的文件夹目录
- `${file}`: 目前打开文件的绝对位置
- `${relativeFile}`: 目前打开文件相对于 workspaceFolder 的相对位置
- `${fileBasename}`: 目前打开文件的文件名（有拓展名，如 `test.js`）
- `${fileBasenameNoExtension}`: 目前打开文件的出去拓展名的文件名（无拓展名，如 `test`）
- `${cwd}`: 当前执行目录
- `${fileDirname}`: 目前打开文件的目录位置
- `${fileExtname}`: 目前打开文件的拓展名
- `${lineNumber}`: 文件中目前被选择的行数
- `${selectedText}`: 文件中目前被选择的内容

综上所属，上述 `launch.json` 配置的是使用 node 调试当前打开的文件，假设当前打开 `a.js`，并且设置了一个断点，开始调试如下图所示：

![QQ20210724-224456-HD.gif](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/10e5517226a34f0aa15f64fde2bfb61b~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

### Vue3源码调试

接下来用 Vue3 代码作为一个案例进行调试演示。

首先将 Vue3 代码拷贝下来并进入项目目录：

```bash
git clone https://github.com/vuejs/vue-next.git
cd vue-next

```

可以发现，Vue3 项目已经进行了一项调试配置：

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Jest",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/node_modules/.bin/jest",
      "stopOnEntry": false,
      "args": ["${fileBasename}", "--runInBand", "--detectOpenHandles"],
      "cwd": "${workspaceFolder}",
      "preLaunchTask": null,
      "runtimeExecutable": null,
      "runtimeArgs": ["--nolazy"],
      "env": {
        "NODE_ENV": "development"
      },
      "console": "integratedTerminal",
      "sourceMaps": true,
      "windows": {
        "program": "${workspaceFolder}/node_modules/jest/bin/jest",
      }
    }
  ]
}

```

这段配置的类型虽然为 `node`，但是 `runtimeExecutable` 为 `null`，因此不会用默认的 `node` 去执行文件，而是使用 `program` 中的可执行程序 `jest` 来执行文件。

那执行的文件是什么呢？答案在 `args` 中的 `${fileBasename}`，也就是当前打开的文件。因此，当我们打开一个 `*.spec.ts` 文件，便可以在这个文件中执行调试。

#### Vue3主流程调试

自带的 jest 调试只能进行局部方法的调试，如果我们需要了解了解 Vue3 执行的主流程应该怎么做呢？

打开 `packages` 目录，映入眼帘的肯定是 `vue` 目录，其目录结构如下所示：

```css
├── __tests__
│   ├── Transition.spec.ts
│   ├── TransitionGroup.spec.ts
│   ├── e2eUtils.ts
│   ├── index.spec.ts
│   ├── runtimeCompilerOptions.spec.ts
│   ├── svgNamespace.spec.ts
│   └── transition.html
├── api-extractor.json
├── examples
│   ├── __tests__
│   │   ├── commits.mock.ts
│   │   ├── commits.spec.ts
│   │   ├── grid.spec.ts
│   │   ├── markdown.spec.ts
│   │   ├── svg.spec.ts
│   │   ├── todomvc.spec.ts
│   │   └── tree.spec.ts
│   ├── classic
│   │   ├── commits.html
│   │   ├── grid.html
│   │   ├── markdown.html
│   │   ├── svg.html
│   │   ├── todomvc.html
│   │   └── tree.html
│   ├── composition
│   │   ├── commits.html
│   │   ├── grid.html
│   │   ├── markdown.html
│   │   ├── svg.html
│   │   ├── todomvc.html
│   │   └── tree.html
│   └── transition
│       ├── list.html
│       └── modal.html
├── index.js
├── node_modules
├── package.json
└── src
    ├── dev.ts
    ├── index.ts
    └── runtime.ts

```

`examples` 中的 `*.html` 就是 Vue3 各个特性的示例代码，通过调试这些文件，我们就能了解 Vue3 整体的执行流程。

这些 html 文件都引入了一个 `../../dist/vue.global.js` 文件，我们首先通过命令构建出来，回到项目根目录执行：

```shell
yarn dev --sourcemap

```

然后，在 VS Code 中安装 `Debugger from Chrome` 扩展：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6fc58cf704294a27851d7df199c48d8b~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

添加 `launch.json` 配置：

```json
{
      "type": "chrome",
      "request": "launch",
      "name": "Launch Chrome",
      "url": "http://localhost:8080",
      "webRoot": "${workspaceFolder}",
      "file": "${workspaceFolder}/packages/vue/examples/composition/${fileBasename}"
}

```

这个配置使得我们可以调试 `packages/vue/examples/composition/` 下的所有 `*.html` 文件，调试其他目录的文件可以通过修改 `file` 的路径实现。

接下来我们测试下调试配置是否生效，在 `packages/vue/examples/composition/commit.html` 打一个断点，在 `packages/compiler-core/src/compile.ts` 中的 `baseCompile` 函数打一个断点，执行调试：

![QQ20210724-235323-HD.gif](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f2b6061ccd3a413cb267669de6fb7534~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

可以看到，程序在 `debugger` 代码处暂停执行，通过单步跳过进入 `createApp`(位于 `packages/runtime-dom/src/index.ts`)，再通过 F5 进入到下一个断点，也就是 `packages/compiler-core/src/compile.ts` 中。

大功告成，愉快地阅读 `Vue3` 源码吧～

+

### `ts-node`调试TypeScript

TypeScript 的流行程度不必多言了，这里简单介绍直接在 `*.ts` 文件中进行调试的技巧：使用 `ts-node` 执行程序，配置如下所示：

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch TS",
      "runtimeArgs": ["-r", "ts-node/register"],
      "args": ["${workspaceFolder}/test.ts"]
    },
  ]
}

```

![QQ20210725-001011-HD.gif](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/249fac833e764dee839b940e931339f9~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

## 总结

调试不仅仅是我们排查问题的重要手段，也是我们高效查看项目源码的方法。

本文介绍了调试在查看源码中的优势以及 VS Code 中调试的相关知识，由于篇幅及笔者自身知识面受限，还有很多的知识点需要读者自行去探索，有错漏的地方也希望大家能在评论区指出。原创不易，希望大家多多点赞～～～

## 参考

[debug一下，你就学会高效阅读开源项目代码～ - 掘金](https://juejin.cn/post/6988528883540688926)
