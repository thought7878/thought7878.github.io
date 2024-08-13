当你写完一个 React 组件，如何保证它的功能是正常的呢？

在浏览器里渲染出来，手动测试一遍就好了啊。

那如果这个组件交给别人维护了，他并不知道这个组件的功能应该是什么样的，怎么保证他改动代码之后，组件功能依然正常？

这种情况就需要单元测试了。

单元测试可以测试函数、类的方法等细粒度的代码单元，保证功能正常。

有了单元测试之后，后续代码改动只需要跑一遍单元测试就知道功能是否正常。

但很多同学觉得单元测试没意义，因为代码改动比较频繁，单元测试也跟着需要频繁改动。

确实，如果代码改动特别频繁，就没必要单测了，手动测试就好。

因为如果手动测试一遍需要 5 分钟，写单元测试可能需要一个小时。

但如果代码比较稳定，那单测还是很有必要的，比如组件库里的组件、hooks 库里的 hooks、一些工具函数等。

手动测试 5 分钟，每次都要手动测试，假设 20 次，那就是 100 分钟的成本，而且还不能保证测试是可靠的。

写单测要一个小时，每次直接跑单测自动化测试，跑 100 次也是一个小时的成本，而且还是测试结果很可靠。

综上，**单元测试能保证函数、类的方法等代码单元的功能正常，把手动测试变成自动化测试。**

**但是写单元测试成本还是挺高的，如果代码改动频繁，那手动测试更合适。一些比较稳定的代码，还是有必要写单测的，写一次，自动测试 n 次，收益很大。**

那 React 的组件和 hooks 怎么写单测呢？

这节我们一起来写几个单测试试。

用 create-react-app 创建个 react 项目：

```
npx create-react-app --template=typescript react-unit-test
```

![[react/react 通关秘籍/media/8074c994eb6e7d85f73ede30ad106630_MD5.png]]

测试 react 组件和 hooks 可以使用 @testing-library/react 这个包，然后测试用例使用 jest 来组织。

这两个包 cra 都给引入了，我们直接跑下 npm run test 就可以看到单测结果。

![[react/react 通关秘籍/media/03fb85eae4fff5e3bd2bd1f1dbe35d83_MD5.png]]

App 组件是这样的：

![[react/react 通关秘籍/media/872a34b13a6066008b8883e3df0cdae2_MD5.png]]

它的单测是这么写的：

![[react/react 通关秘籍/media/107b62e36f3d596104878ed696806b90_MD5.png]]

通过 @testing-library/react 的 render 函数把组件渲染出来。

通过 screen 来查询 dom，查找文本内容匹配正则 /learn react/ 的 a 标签。

然后断言它在 document 内。

你也可以这么写：
```javascript
test('renders learn react link 2', () => {
  const { container } = render(<App />);
  const linkElement = container.querySelector('.App-link');

  expect(linkElement?.textContent).toMatch(/learn react/i)
});
```
render 会返回组件挂载的容器 dom，它是一个 HTMLElement 的对象，有各种 dom 方法。

![[react/react 通关秘籍/media/b2751caf98506a503d1424c955dce61b_MD5.png]]

可以用 querySelector 查找到那个 a 标签，然后判断它的内容是否匹配正则。

这两种写法都可以。

第二种方法更容易理解，就是拿到渲染容器的 dom，再用 dom api 来查找 dom。

第一种方法的 screen 是 @testing-library/react 提供的 api，是从全局查找 dom，可以直接根据文本查（getByText），根据标签名和属性查（getByRole） 等。

antd 组件的测试也是用的第二种来查找 dom 的：

![[react/react 通关秘籍/media/7783d927610e0fac475e6dc6d85de639_MD5.png]]

那如果有 onClick、onChange 等事件监听器的组件，怎么测试呢？

我们写个组件 Toggle.tsx：

```javascript
import { useCallback, useState } from 'react';

function Toggle() {

    const [status, setStatus] = useState(false);

    const clickHandler = useCallback(() => {
        setStatus((prevStatus) => !prevStatus);
    }, []);

    return (
        <div>
            <button onClick={clickHandler}>切换</button>
            <p>{status ? 'open' : 'close' }</p>
        </div>
    );
}

export default Toggle;
```
有个 state 来存储 open、close 的状态，点击按钮切换。

渲染出来是这样的：

![[react/react 通关秘籍/media/50bb635fac5b0148c49e31c1c4ff8da4_MD5.gif]]

这个组件如何测试呢？

单测里触发事件需要用到 fireEvent 方法了。

改下 App.test.tsx

```javascript
import { render, fireEvent } from '@testing-library/react';
import Toggle from './Toggle';

test('toggle', () => {
  const { container } = render(<Toggle/>);

  expect(container.querySelector('p')?.textContent).toBe('close');

  fireEvent.click(container.querySelector('button')!)
  
  expect(container.querySelector('p')?.textContent).toBe('open');
})
```
用 render 方法把组件渲染出来。

用 container 节点的 dom api 查询 p 标签的文本，断言是 close。

然后用 fireEvent.click 触发 button 的点击事件。

断言 p 标签的文本是 open。

跑一下：

```
npm run test
```
测试通过了：

![[react/react 通关秘籍/media/593cdf0aba6ddb4a73cf83af80c221a7_MD5.png]]

fireEvent 可以触发任何元素的任何事件：

![[react/react 通关秘籍/media/79a2e90ab86564ae164cc5d0e6343bf5_MD5.png]]

那如何触发 change 事件呢？

这样写：

![[react/react 通关秘籍/media/1c47d863f18f76492f00fdd344620461_MD5.png]]

第二个参数传入 target 的 value 值。

此外，如果我有段异步逻辑，过段时间才会渲染内容，这时候怎么测呢？

比如 Toggle 组件里点击按钮之后，过了 2s 才改状态：

![[react/react 通关秘籍/media/563a0290a585a4c189d88ff72d69b236_MD5.png]]

```javascript
setTimeout(() => {
    setStatus((prevStatus) => !prevStatus);
}, 2000);
```

这时候测试用例就报错了：

![[react/react 通关秘籍/media/c12f8b0f87bc44a767f6bbd160d78305_MD5.png]]

这种用 waitFor 包裹下，设置 timeout 的时间就好了：

![[react/react 通关秘籍/media/17eabaffd02e1cd48b5238e676a5d426_MD5.png]]

```javascript
await waitFor(() => expect(container.querySelector('p')?.textContent).toBe('open'), {
    timeout: 3000
});
```

测试通过了：

![[react/react 通关秘籍/media/e45f9cbb3105d20ebe0efb2722f2d2aa_MD5.png]]

除了这些之外，还有一个 api 比较常用，就是 act

它是 react-dom 包里的，@testing-library/react 对它做了一层包装。

![[react/react 通关秘籍/media/870b0d539b890754c9180aef79698278_MD5.png]]

就是可以把所有浏览器里跑的代码都包一层 act，这样行为会和在浏览器里一样。

文档里的例子是这样的：

![[react/react 通关秘籍/media/2cff474e8a3b09f773e090ecff019185_MD5.png]]

把单测里的 fireEvent 用 act 包一层：

```javascript
import { render, fireEvent, waitFor } from '@testing-library/react';
import { act } from 'react-dom/test-utils';
import Toggle from './Toggle';

test('toggle', async () => {
  const { container } = render(<Toggle/>);
  expect(container.querySelector('p')?.textContent).toBe('close');

  act(() => {
    fireEvent.click(container.querySelector('button')!)
  })

  await waitFor(() => expect(container.querySelector('p')?.textContent).toBe('open'), {
    timeout: 3000
  });
})
```

结果一样：

![[react/react 通关秘籍/media/f5a5710400df0d28664eeb09b554f22f_MD5.png]]

组件测试我们学会了，那如果我想单独测试 hooks 呢？

这就要用到 renderHook 的 api 了。

我们写个 useCounter 的 hook：

```javascript
import { useState } from 'react';

type UseCounterReturnType = [
  count: number, 
  increment: (delta: number) => void, 
  decrement: (delta: number) => void
];

export default function useCounter(initialCount: number = 0): UseCounterReturnType {
  
  const [count, setCount] = useState(initialCount);

  const increment = (delta: number) => {
    setCount(count => count + delta);
  };

  const decrement = (delta: number) => {
    setCount(count => count - delta);
  };

  return [count, increment, decrement];
}
```
先在 App.tsx 里用一下：

```javascript
import useCounter from './useCounter';

function App() {

  const [count, increment, decrement] = useCounter();

  return (
    <div>
      <div>
        {count}
      </div>
      <div>
        <button onClick={() => increment(1)}>加一</button>
        <button onClick={() => decrement(2)}>减二</button>
      </div>
    </div>
  );
}

export default App;
```
跑一下：

```
npm run start
```

![[react/react 通关秘籍/media/0137d2c9487a9c1bc5e5c6b5669a7895_MD5.gif]]

没啥问题。

然后来写下这个 hook 的单测：

```javascript
test('useCounter', async () => {
  const hook = renderHook(() => useCounter(0));
  
  const [count, increment, decrement]  = hook.result.current;

  act(() => {
    increment(2);
  });
  expect(hook.result.current[0]).toBe(2);

  act(() => {
    decrement(3);
  });
  expect(hook.result.current[0]).toBe(-1);

  hook.unmount();
});
```

renderHook 返回的 result.current 就是 hook 的返回值。

![[react/react 通关秘籍/media/9037b599a56189d3be59fe6560971afe_MD5.png]]

这就是 hook 的单测写法。

案例代码上传了[小册仓库](https://github.com/QuarkGluonPlasma/react-course-code/tree/main/react-unit-test)。

## 总结

单元测试能保证函数、类的方法等代码单元的功能正常，把手动测试变成自动化测试。

变更不频繁的代码，还是有必要写单测的，写一次，自动测试 n 次，收益很大。

我们学了 react 组件和 hook 的单测写法。

主要是用 @testing-library/react 这个库，它有一些 api：

- render：渲染组件，返回 container 容器 dom 和其他的查询 api
- fireEvent：触发某个元素的某个事件
- createEvent：创建某个事件（一般不用这样创建）
- waitFor：等待异步操作完成再断言，可以指定 timeout
- act：包裹的代码会更接近浏览器里运行的方式
- renderHook：执行 hook，可以通过 result.current 拿到 hook 返回值

其实也没多少东西。

jest 的 api 加上 @testing-libary/react 的这些 api，就可以写任何组件、hook 的单元测试了。
