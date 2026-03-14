这段代码定义了一个导出函数，用于标记工作进行中的更新已接收。其核心功能是*将全局变量`didReceiveUpdate`设置为`true`，表示组件或系统已经接收到更新信号*。 

**关于didReceiveUpdate：** 这是一个*布尔类型的全局标志位*，用于*跟踪是否收到了更新请求*，在React等框架的*调度系统中用来判断当前工作单元是否有待处理的更新*。

## 源码
`packages/react-reconciler/src/ReactFiberBeginWork.old.js`

```ts
export function markWorkInProgressReceivedUpdate() {
  didReceiveUpdate = true;
}
```