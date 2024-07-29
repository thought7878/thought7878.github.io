

首先，调试配置文件不用自己创建，可以直接点击 Debug 窗口的 `create a launch.json file` 快速创建：

![[debug/前端调试通关秘籍/media/d61b541a7287d3b64e590a7a79d299ff_MD5.gif]]

## launch/attach

创建 Chrome Debug 配置有两种方式：launch 和 attach：

![[debug/前端调试通关秘籍/media/08d256106893177827f2d7d7bc2e37a5_MD5.png]]
 
**它们只是 request 的配置不同：**

![[debug/前端调试通关秘籍/media/6a4d9d71b1e725651228360acdf8ff1c_MD5.png]]

调试就是把浏览器跑起来，访问目标网页，这时候会有一个 ws 的调试服务，我们用 frontend 的 ws 客户端连接上这个 ws 服务，就可以进行调试了。

![[debug/前端调试通关秘籍/media/fea905026e54e27dad4ee866da9ad6f8_MD5.png]]

VSCode 的 Debugger 会多一层适配器协议的转换，但是原理差不多。

### launch

**launch** 是把 url 对应的网页launch起来/跑起来，指定调试端口，然后 frontend 自动 关联（attach） 到这个端口。

### attach

- **What**

但如果你已经有一个在调试模式跑的浏览器了，那直接连接上就行，这时候就直接 **attach**。

- **执行命令**

比如我们手动把 Chrome 跑起来，指定调试端口 remote-debugging-port 为 9222，指定用户数据保存目录 user-data-dir 为你自己创建一个目录。

在命令行执行下面的命令：
```
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222 --user-data-dir=你自己创建的某个目录
```

Chrome 跑起来之后，你可以打开几个网页，比如百度、掘金，然后你访问 localhost:9222/json，这时候你就会发现所有的 ws 服务的地址了：

![[debug/前端调试通关秘籍/media/67ca27934bf7424c0b111650149a40a5_MD5.png]]

为什么每个页面有单独的 ws 服务呢？这个很正常呀，每个页面的调试都是独立的，自然就需要单独的 ws 服务。

- **创建配置**

然后你创建一个 attach 的 Chrome Debug 配置：

![[debug/前端调试通关秘籍/media/ae5aec6f0183a20cadfa303f73bc37ca_MD5.png]]

- **启动**

点击启动，就会看到 VSCode Debugger 和每一个页面的 ws 调试服务建立起了链接：

![[debug/前端调试通关秘籍/media/93f2998306236f86895240d325f61cb5_MD5.png]]

- **调试**

比如访问之前的 React 项目，就可以进行调试了：

![[debug/前端调试通关秘籍/media/f0109d100fc9c98b98be6897e28944e8_MD5.png]]

**可以多个页面一起调试，每个页面都有独立的调试上下文。**

## userDataDir

### attach模式

刚才手动启动 Chrome 的时候，除了指定调试端口 remote-debugging-port 外，还指定了用户数据目录 user-data-dir。

*为什么要指定这个呢？* user data dir 是保存用户数据的地方，比如你的浏览记录、cookies、插件、书签、网站的数据等等。在 macOS 下是保存在这个位置：

```
~/Library/Application\ Support/Google/Chrome
```

比如你打开 Default/Bookmarks 看一下，是不是都是你保存的书签？
```
open ~/Library/Application\ Support/Google/Chrome/Default/Bookmarks
```

![[debug/前端调试通关秘籍/media/17f9d802412db189b377f695e070cb90_MD5.png]]

你还可以删掉 Deault/Cookies，之后再访问之前登陆过的网站试一下，是不是都需要登录了？*这就是用户数据目录的作用。*

那为什么启动 Chrome 要手动指定这个呢？都用默认的不行么？用户数据目录有个特点，就是只能被一个 Chrome 实例所访问，如果你之前启动了 Chrome 用了这个默认的 user data dir，那就不能再启动一个 Chrome 实例用它了。

*如果用户数据目录已经跑了一个 Chrome 实例，再跑一个候会报这样的错误：*

![[debug/前端调试通关秘籍/media/c04e016a50241cd7fcd600bde9328002_MD5.png]]

所以我们用调试模式启动 Chrome 的时候，需要单独指定一下 user data dir 的位置。或者你也把之前的 Chrome 实例关掉，这样才能用默认的。

### launch模式

launch 的配置项里也有 userDataDir 的配置：

![[debug/前端调试通关秘籍/media/db63908bd56fa026b39181450f8eb355_MD5.png]]

**默认是 true**，*代表创建一个临时目录来保存用户数据*。

**设置为 false**，使用默认 user data dir 启动 chrome。*这样的好处就是登录状态、历史记录啥的都有*：

![[debug/前端调试通关秘籍/media/31258ad7f415ddc202a7f70b1503ab38_MD5.gif]]

把 userDataDir 设置为 true 就每次都需要登录了：

![[debug/前端调试通关秘籍/media/4b719efc686ba9b58ff06b168bfca39c_MD5.gif]]

- **自定义路径**

**也可以指定一个自定义的路径**，这样用户数据就会保存在那个目录下：

![[debug/前端调试通关秘籍/media/119ea2108b692fd0d212901779f24da0_MD5.png]]

#### React DevTools等插件

更重要的是，你安装的 React DevTools、Vue DevTools 插件都是在默认用户数据目录的，要是用临时数据目录跑调试，那这些不都没了？

比如你 userDataDir 设置为 true 的时候，*React DevTools 插件是没有的*，需要再安装：

![[debug/前端调试通关秘籍/media/d74d0a7c474d55ba36883f6d4f251491_MD5.png]]

userDataDir 设置为 false 的时候，*安装过的插件都可以直接用：*

![[debug/前端调试通关秘籍/media/493ffab560614679a55362b160144d91_MD5.png]]



## runtimeExecutable
###  问题

*但是除了调试用之外，平时也会用到 Chrome 呀，同一个 user data dir 只能跑一个 Chrome 实例的话，那不就冲突了？* 这个问题可以用下面的配置解决。

调试网页的 JS，需要先把 Chrome 跑起来，默认跑的是 Google Chrome，其实它还有另外一个版本 Canary：

![[debug/前端调试通关秘籍/media/0b9ac0561e3a7740ebe9c483682b2aec_MD5.png]]

这是给开发者用的每日构建版，能够快速体验新特性，但是不稳定。

而 Google Chrome 是给普通用户用的，比较稳定。

这俩是独立的，相互之间没影响，可以都用同一个 user data dir 来启动。

你可以在[官网](https://www.google.com/intl/zh-CN/chrome/canary/)把 canary 下载下来。

然后指定 runtimeExecutable 为 canary，使用默认的用户数据目录启动：

![[debug/前端调试通关秘籍/media/defb577c0ece8a9fa9df0fcfb2d39490_MD5.png]]


![[debug/前端调试通关秘籍/media/7a692eb6e1490963e396bb803003461f_MD5.gif]]

这样你就可以调试用 canary，平时用 chrome 了，两者都有各自的默认数据目录。

（注意，一定要先安装了 canary，才能指定 canary 跑）

当然，runtimeExecutable 还可以指定用别的浏览器跑：

![[debug/前端调试通关秘籍/media/979d2c46bcfd3d8434484fe3ce9dac89_MD5.png]]

可以是 stable，也就是稳定的 Google Chrome，或者 canary，也就是每日构建版的 Google Chrome Canary，还可以是 custom，然后用 CHROME_PATH 环境变量指定浏览器的地址。

不过常用的还是 Chrome 和 Canary。

### runtimeArgs

启动 Chrome 的时候，可以指定启动参数，比如每次打开网页都默认调起 Chrome DevTools，就可以加一个 --auto-open-devtools-for-tabs 的启动参数：

![[debug/前端调试通关秘籍/media/fe8b577eec3d2812afe1e95b9f1d68fe_MD5.png]]

效果就是这样的：

![[debug/前端调试通关秘籍/media/287c9953c4836cbc407ef60b9fef171a_MD5.gif]]

想要无痕模式启动，也就是不加载插件，没有登录状态，就可以加一个 --incognito 的启动参数：

![[debug/前端调试通关秘籍/media/53e3d43eb3d80fe083777cc5adca26cb_MD5.png]]

调试用的浏览器就会以无痕模式启动了：

![[debug/前端调试通关秘籍/media/b93bce982f1eed0c0d60e532d0819077_MD5.gif]]

其实我们设置的 userDataDir 就是指定了 --user-data-dir 的启动参数。

## sourceMapPathOverrides

代码是经过编译打包然后在浏览器运行的，比如这样：

![[debug/前端调试通关秘籍/media/b5f353630f8dd8b7108fc8ea83fa3e05_MD5.png]]

但我们却可以直接调试源码，这是通过 sourcemap 做到的。

调试工具都支持 sourcemap，并且是默认开启的：

![[debug/前端调试通关秘籍/media/e10825279d8847645a5750ff8231c022_MD5.png]]

当然也可以关掉:

Chrome DevTools 里这么关（command + shift + p）：

![[debug/前端调试通关秘籍/media/5e2de7151a21240197d3e0b65eda833c_MD5.png]]

VSCode Debugger 这么关：

![[debug/前端调试通关秘籍/media/abcb9a7ad47cffb81c9574e665e93b98_MD5.png]]

这样调试的就是编译后的代码了：

![[debug/前端调试通关秘籍/media/8de7d66fb3fab57889719ac46f8f21aa_MD5.png]]

在开启 sourcemap 的情况下，用 Chrome DevTools 可以看到，源文件的路径是 /static/js/bundle.js

![[debug/前端调试通关秘籍/media/9119cfc1658b5469210be6c2af575efa_MD5.png]]

被 sourcemap 到了 /Users/guang/code/test-react-debug/src/index.js

![[debug/前端调试通关秘籍/media/9a4a56ea21a17296107dc9fe381f967d_MD5.png]]

而在 VSCode 里，这个路径是有对应的文件的，所以就会打开对应文件的编辑器，这样就可以边调试边修改代码。

但有的时候，sourcemap 到的文件路径在本地里找不到，这时候代码就只读了，因为没有地方保存：

![[debug/前端调试通关秘籍/media/0d7de76667b634ce738c949d1f4bd22d_MD5.png]]

这种情况就需要对 sourcemap 到的路径再做一次映射：

![[debug/前端调试通关秘籍/media/ec9c8348b3052eb4705b14b9c3c78e6f_MD5.png]]

通过 sourceMapPathOverrides 这个配置项。

默认有这么三个配置：

![[debug/前端调试通关秘籍/media/8cf4721904da0f787a01348d5fb05c30_MD5.png]]

分别是把 meteor、webpack 开头的 path 映射到了本地的目录下。

其中 `?:*` 代表匹配任意字符，但不映射，而 * 是用于匹配字符并映射的。

比如最后一个 webpack://?:\*/\* 到 ${workspaceFolder}/\* 的映射，就是把 webpack:// 开头，后面接任意字符 + / 然后是任意字符的路径映射到了本地的项目目录。（workspaceFolder 是一个内置变量，代表项目根目录）

把调试的文件 sourcemap 到的路径映射到本地的文件，这样调试的代码就不再只读了：

![[debug/前端调试通关秘籍/media/b738cc3381ab8815e8e840a073c406fa_MD5.gif]]

## file

除了启动开发服务器然后连上 url 调试之外，也可以直接指定某个文件，VSCode Debugger 会启动静态服务器提供服务：

![[debug/前端调试通关秘籍/media/4053c2957fd671464e9bcfb1f7c70df3_MD5.png]]

index.html 的内容如下：

![[debug/前端调试通关秘籍/media/34986eafdd0b76fcaf61b9cfd607dd1d_MD5.png]]

打了个断点，然后启动调试：

![[debug/前端调试通关秘籍/media/39f2d3531b229e5db3a4c90d5b9a6166_MD5.png]]

这样就可以直接调试静态网页了。

同样，要修改调试的内容需要把 url 映射到本地文件才行，所以有这样一个 pathMapping 的配置：

![[debug/前端调试通关秘籍/media/5a85f247083c1503dfa17567b9120f89_MD5.png]]

webRoot 其实就相当于把 / 的 url 映射到了 ${workspaceFoder}/。
 
这些配置倒是很少用，一般我们还是启动 dev server，再调试某个 url 更多一些。

## 总结

这节我们过了一遍 VSCode Chrome Debugger 的配置：

- launch：调试模式启动浏览器，访问某个 url，然后连上进行调试
- attach：连接某个已经在调试模式启动的 url 进行调试
- userDataDir： user data dir 是保存用户数据的地方，比如浏览历史、cookie 等，一个数据目录只能跑一个 chrome，所以默认会创建临时用户数据目录，想用默认的目录可以把这个配置设为 false
- runtimeExecutable：切换调试用的浏览器，可以是 stable、canary 或者自定义的
- runtimeArgs：启动浏览器的时候传递的启动参数
- sourceMapPathOverrides：对 sourcemap 到的文件路径做一次映射，映射到 VSCode workspace 下的文件，这样调试的文件就可以修改了
- file：可以直接指定某个文件，然后启动调试

这些配置后面调试的时候经常会用到，要理解它们的含义。

