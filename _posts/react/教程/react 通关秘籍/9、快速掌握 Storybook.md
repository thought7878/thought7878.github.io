我们每天都在写各种组件，一般的组件不需要文档，但当你写组件库里的组件，或者项目里的一些公共组件的时候，是需要提供文档的。

这时候我们一般都会用 Storybook。

Storybook 是非常流行的用来构建组件文档的工具。

现在有 80k 的 star 了：

![[794f41e4011ceee5e5da7bd4c63bda1a_MD5.png]]

那 Storybook 都提供了啥功能呢？

我们试一下就知道了：

```
npx create-react-app --template typescript sb-test
```

![[5762b74be0cb1298c43f1506e7ff16aa_MD5.png]]

用 cra 创建个 react 项目。

然后进入项目，执行 storybook 的初始化：

```
npx storybook@latest init
```
![[5668655f89820707a1239d0f742d70b2_MD5.png]]

打印的日志告诉你 storybook init 是在你的项目里添加 storybook 的最简单方式。

它会在你的 package.json 添加一个 storybook 命令：

![[18233d428344e9a9c7513cbbce13880a_MD5.png]]

执行 npm run storybook，就可以看到这样文档：

```
npm run storybook
```

![[a6151d3eb70d4501399e11cae680f2dd_MD5.png]]

这就是 storybook 生成的组件文档。

这三个组件不是我们自己写的，是 storybook 初始化的时候自带了三个 demo 组件。

我们可以用它来了解下 storybook 的功能。

![[68c54d21ceb736f52a311fe635dd7f49_MD5.png]]

storybook init 在项目里加了 2 个目录： .storybook 和 src/stories

.storybook 下的是配置文件， src/stories 下的是展示文档用的组件。

Button.tsx 就是传入几个参数，渲染出一个 button：

![[55b40e5699d70d9772c9821da5d830a8_MD5.png]]

然后 Button.stories.tsx 里导出了几种 Button 的 props：

![[2f5c8925d4b74545349bf032f4a4fe73_MD5.png]]

导出的这几个 Story 类型的对象是啥呢？

是用来渲染不同 story 的：

![[23bcc43fdac40ba981802f58129c4067_MD5.gif]]

也就是 Button 组件传入不同参数的时候渲染的结果。

我们加一个 Story 试试：

![[089c5881af94d3312fe264da97706c4f_MD5.png]]

```javascript
export const Guang: Story = {
  args: {
    label: '光光光',
    size: 'large',
    backgroundColor: 'green'
  }
}
```

页面多了一个 Button 的类型：

![[890dc9cfeff1a4a60755d176ca115362_MD5.png]]

也就是说，Storybook 把同一个组件传入不同 props 的情况，叫做一个 Story。

别的地方可能叫做用例或变体，而在 Storybook 里叫做 story。

一个组件包含多个 Story，一个文档里又包含多个组件，和一本书的目录差不多。

所以把这个工具叫做 Storybook。

除了 story 外，上面还有生成的组件文档：

![[96245ece9bf42be36b0653683c79cf2b_MD5.png]]

可以看到，列出了每个 props 和描述。

是从注释里拿到的：

![[7a3aaedf89eba1b4158affa82e350fed_MD5.png]]

我们改了一下注释，刷新下，可以看到文档变了：

![[0af8b6dae5c66520215cd57829e6b39b_MD5.png]]

这样就可以方便的生成组件文档了。

而且，这些参数都是可以调的：

![[150a180f372f4657646aa9c30f02e594_MD5.gif]]

可以直接修改 props 看组件渲染结果，就很方便。

而且你还可以直接复制它的 jsx 代码：

![[519691c2dac04dafcc72fbe3166d93d7_MD5.png]]

之前我们是 args 传入参数渲染，你还可以用 render 函数的方式自己渲染：

![[83c03001b6057e48319f3010e3bd01e1_MD5.png]]

```javascript
export const Guang: Story = {
  args: {
    label: '光光光',
    size: 'large',
    backgroundColor: 'green'
  },
  render(args) {
    return <div>
      <button>aaaa</button>
      <Button {...args}/>
      <button>bbb</button>
    </div>
  }
}
```

render 函数的参数就是 args，你可以自己返回 jsx（这时要把文件后缀名改为 tsx）。

这样，渲染内容就是自己控制的：

![[5fbef788b639e14b75b813950f59db40_MD5.png]]

而且有的组件不只是传入 props 就可以了，还需要一些点击、输入等事件。

storybook 支持写这类脚本：

![[1f771640f6f915b0e4d53eb88d220660_MD5.png]]
```javascript
export const Guang: Story = {
  args: {
    label: '光光光',
    size: 'large',
    backgroundColor: 'green'
  },
  render(args) {
    return <div>
      <button>aaaa</button>
      <Button {...args}/>
      <button>bbb</button>
    </div>
  },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    const btn = await canvas.getByRole('button', {
      name: /光光光/i,
    });
    await userEvent.click(btn);

    btn.textContext = '东';
  },
}
```

比如我写了找到内容为光光光的 button，点击，然后把它的内容改为东。

组件渲染完就会自动执行 play 函数：

![[6fad8b4d2c5b99217be83e6afeb7065d_MD5.gif]]

当然，这个案例不大好，用表单来测试 play 功能会更好点：

![[0a9cd10aefced218ac4c2cf3a23fcfec_MD5.gif]]

此外，你还可以在渲染组件之前请求数据，然后把数据传入 render 函数再渲染：

![[5893a1b1c19552ab8ac339370358a0b4_MD5.png]]

```javascript

export const Guang: Story = {
  args: {
    label: '光光光',
    size: 'large',
    backgroundColor: 'green'
  },
  render(args, meta) {
    const list = meta.loaded.list;

    return <div>
      <div>{list.join(',')}</div>
      <Button {...args}/>
    </div>
  },
  loaders: [
    async () => {
      await '假装 fetch'
      return {
        list: [
          111,
          222,
          333
        ]
      }
    },
  ]
}
```

渲染出来是这样的：

![[4eeeea07c2c01ab30a617ad94e80592b_MD5.png]]

感受到 Storybook 的强大了么？

不只是自动生成组件文档这么简单，你可以定义不同的 Story，这些 Story 可以传入不同 props、可以请求数据、可以自定义渲染内容、还可以定义自动执行的脚本。

有同学会觉得，这个自动执行的 play 函数其实和测试脚本差不多。

确实，play 函数是可以当作测试脚本来用的。

安装用到的包：

```
npm install @storybook/jest
```

使用 expect 来断言：

![[46a2ee557950cc2e663a94911b1124db_MD5.png]]

![[939368d82d02f748734104b5f63ef0e9_MD5.png]]

```javascript
await expect(btn.textContent).toEqual('光光光');

await expect(btn.style.backgroundColor).toEqual('green');
```

这样一打开组件会自动跑 play 函数，也就会自动执行断言：

![[a53dea2980064a514224376832a2b977_MD5.png]]

改下 expect，断言失败就是这样：

![[618db2b995a6f3d645593f9936be19a7_MD5.png]]

![[f08007553d9168e8b88e09965b13bcf0_MD5.png]]

这样，组件有没有通过测试用例，打开一看就知道了。

就很方便。

但是，组件多了的话，这样一个个点开看也挺麻烦的，这时候就可以用 cli 跑了：

安装用到的包：
```
npm install @storybook/testing-library
```
然后：

```
npx test-storybook
```
![[d441b22652d93a15dea85592190d2e80_MD5.png]]

xx.stories.tsx 文件里除了 Story 外，还会导出 meta 信息：

![[1fe126c921446ae352d3e599f3155a55_MD5.png]]

这些都很简单，改一下就知道了：

![[5c08b909906bf1add92ffdd74d32e072_MD5.png]]

title 是这个：

![[fbfa598e0960ca24b89f361e95a205d3_MD5.png]]

paremeters 的 layout 是这个：

![[b1f9e1c2f51bb8fb6ecf2c3fde00d9c2_MD5.png]]

![[dce0722b440c9350467573afaa7d7686_MD5.png]]

![[c4ffac69ec4970af3030f3a2ea2f5722_MD5.png]]

这里还可以配置背景色：

![[25d848240e018e8f75505265a4f84e1b_MD5.png]]

![[602be9922b4f2019faaf827b3c98d464_MD5.gif]]

然后 argTypes 是这个：
![[49fa89885f2f00456441b3a9ddfaba3a_MD5.png]]


![[7b3ecf05d5a0614b213b1b1e74480cc0_MD5.png]]


![[30ee74be94387981c503b6443f555c7b_MD5.png]]

![[1b46cfa74edb95716e3027ffce78346a_MD5.png]]

具体什么类型的参数用什么控件，可以用到的时候查一下[文档](https://storybook.js.org/docs/essentials/controls#annotation)。

![[4a0285eb544b8e94ab746bc68b75fb55_MD5.png]]

这些都是与 Story 无关的一些东西，所以放在 Meta 里。

此外，你还可以用 Storybook 写 MDX 文档。

mdx 是 markdown + jsx 的混合语法，用来写文档很不错。

在这个目录下的文档：

![[0963b4e5acf93a2dbe00f8b0cb24847a_MD5.png]]

都会被放到这里：

![[d354d08ed327e45160546cab7bb3ca91_MD5.png]]

我们加一个试试：

![[541385f23aad4c14bc19ef470eb6413e_MD5.png]]

![[55d6061c4735c6bb484ae5bd71c7afc5_MD5.png]]

这样，当你想在组件文档里加一些别的说明文档，就可以这样加。

而且，组件文档的格式也是可以自定义的。

可以在 .storybook 下的 preview.tsx 里配置这个：

![[b6dd9bac1f29c66835abd5d0bdf523a4_MD5.png]]

![[7d16d6c0c6a517ec770039b1cee125cd_MD5.png]]

![[3a9486f1c77e4ebb1eb50ad26fd7b84e_MD5.png]]

![[fd651ed14c272d70cf35c6cae7551b5f_MD5.png]]

大概过了一遍 Storybook 的功能之后，我们把上节的 Calendar 组件拿过来试一下。

把那个项目的 Calendar 目录复制过来：

![[9efa13d3d07941f46de2247b2bd5dbfb_MD5.png]]

然后在 stories 目录下添加一个 Calendar.stories.tsx

```javascript
import type { Meta, StoryObj } from '@storybook/react';
import Calendar from '../Calendar/index';
import dayjs from 'dayjs';

const meta = {
    title: '日历组件',
    component: Calendar,
    parameters: {
        layout: 'centered'
    },
    tags: ['autodocs']
} satisfies Meta<typeof Calendar>;

export default meta;

type Story = StoryObj<typeof meta>;

export const Value: Story = {
    args: {
        value: dayjs('2023-11-08')
    },
};

export const DateRender: Story = {
    args: {
        value: dayjs('2023-11-08'),
        dateRender(currentDate) {
            return <div>
                日期{currentDate.date()}
            </div>
        }
    },
};

export const DateInnerContent: Story = {
    args: {
        value: dayjs('2023-11-08'),
        dateInnerContent(currentDate) {
            return <div>
                日期{currentDate.date()}
            </div>
        }
    },
};

export const Locale: Story = {
    args: {
        value: dayjs('2023-11-08'),
        locale: 'en-US'
    },
};

```
我们添加了 4 个 story。

安装用到的 dayjs、classnames 和 node-sass

```
npm install --save classnames

npm install --save dayjs

npm install --save-dev node-sass
```
然后把 storybook 文档服务跑起来：

```
npm run storybook
```
![[c9c96f39ed4019da9b7624ab6dd0ff2f_MD5.png]]

![[1efa7a825691ed566e8221c1d462fde3_MD5.png]]

![[5d29454393398ac4dbf197ad7f68c758_MD5.png]]

![[08f214a5961657fe9c341f492f1ee410_MD5.png]]

都没啥问题。

不过 value 的控件类型不对：

![[214af7441eba075531bdaf736f775d14_MD5.png]]

但是现在我们要传入的是 dayjs 对象，就算是用了 date 的控件也不行。

先改成 date 类型试试：

![[38f88e5954f3ead12df624f015921c77_MD5.png]]

控件确实对了，但是修改日期点击刷新后，会报错：

![[ea49f34a5a18e4e44a15e733b2228757_MD5.gif]]

因为控件传入的是一个 date 的毫秒值。

那怎么办呢？

这时候就要把 story 改成 render 的方式了：

![[cec32991107f943514a9eeb95fa27af5_MD5.png]]

```javascript

const renderCalendar = (args: CalendarProps) => {
    if(typeof args.value === 'number') {
        return <Calendar {...args} value={dayjs(new Date(args.value))}/>
    }

    return <Calendar {...args}/>
}

export const Value: Story = {
    args: {
        value: dayjs('2023-11-08')
    },
    render: renderCalendar
};

```

再试试：

![[9386f8890f1529a95dc23a1b1865b3d9_MD5.gif]]

现在就可以了。

案例代码上传了[小册仓库](https://github.com/QuarkGluonPlasma/react-course-code/tree/main/sb-test)。

## 总结

写组件文档，我们一般都是用 Storybook。

它把不同 props 的渲染结果叫做一个 story，一个组件有多个 story。

story 可以通过 args 指定传入组件的参数，通过 loaders 请求数据，通过 render 函数自定义渲染内容、通过 play 指定自动执行的脚本等。

而且还可以渲染完组件直接跑测试用例，就很方便。

storybook 还会自动生成组件文档，而且也可以把项目里的 mdx 文件加到文档里。

用起来也很简单，首先 npx storybook init 初始化，之后执行 npm run storybook 就可以了。

总之，用 storybook 可以轻松的创建组件文档，可以写多个 story，直观的看到组件不同场景下的渲染结果，还可以用来做测试。

如果想给你的组件加上文档，storybook 基本是最好的选择。
