# Why
- **提升用户体验**
    - **减少延迟感知**：在许多应用中，特别是涉及网络请求的操作（如发送消息、点赞、评论等），用户需要等待服务器响应才能看到操作的结果。这个等待过程可能会让用户感觉应用响应迟缓。例如，在一个社交网络应用中，当用户点击 “点赞” 按钮时，如果需要等待服务器确认点赞操作后才更新点赞数显示，用户可能会感觉到明显的延迟。通过`useOptimistic`进行乐观更新，可以在用户点击 “点赞” 按钮后立即增加点赞数的显示，让用户感觉点赞操作是即时生效的，减少了用户对延迟的感知，提供了更流畅、即时的交互体验。
    - **增强交互的反馈感**：乐观更新为用户提供了更积极的反馈。当用户执行一个操作时，立即看到 UI 上的变化会让用户更加自信地认为操作已经成功，即使后台可能还在处理这个操作。这对于一些重要的用户操作，如提交表单、执行交易等，可以增加用户的信任感和满意度。例如，在一个电商应用中，用户点击 “购买” 按钮后，立即更新购物车状态和订单状态显示，让用户感觉购买流程已经顺利启动，而不是等待服务器处理后才显示变化。
- **处理异步操作的不确定性**
    - **应对网络或服务器延迟**：在网络环境不稳定或者服务器负载较高的情况下，操作的完成时间可能会有较大的波动。`useOptimistic`允许应用在这种不确定性下保持良好的用户体验。即使网络请求最终失败，也可以通过适当的回滚机制（通常在收到服务器的错误响应后）来调整 UI 回到正确的状态。例如，在一个内容发布应用中，用户发布新内容后，立即在本地更新内容列表显示新内容，但如果服务器返回发布失败的消息，可以将新内容从列表中移除，这种先展示乐观结果再根据实际情况调整的方式能够更好地应对网络和服务器的不确定性。

# What
`useOptimistic`是 React 提供的一个实验性的 Hook，主要用于在用户交互时提供乐观更新（optimistic updates）。**乐观更新**是指在执行一个可能需要一些时间才能完成的操作（如网络请求）之前，*先在 UI 上展示操作成功后的预期状态，让用户感觉操作是即时完成的，而不是等待操作真正完成后再更新 UI*。

# How
**基本语法**：
```jsx
const [state, dispatch] = useOptimistic(initialState, reducer, optimisticUpdate);
```
- `initialState`：是状态的初始值，它可以是任何 JavaScript 数据类型，如对象、数组等，代表了应用的初始状态。
- `reducer`：是一个函数，类似于 Redux 中的 Reducer，*它接收当前状态和一个动作（Action）作为输入，然后返回一个新的状态*。这个函数用于*根据实际的操作结果（如网络请求的响应）来更新状态*。
- `optimisticUpdate`：也是一个函数，*它接收当前状态和一个动作作为输入，返回一个乐观的新状态*。这个函数用于*在操作发起时（如网络请求发送之前）立即更新 UI 显示乐观的结果*。
   
**示例**：

```jsx
import React, { useOptimistic } from'react';
function OptimisticLikeButton() {
  const initialLikes = 0;
  //
  const [likes, dispatch] = useOptimistic(
    initialLikes,
    // 根据实际的操作结果（如网络请求的响应）来更新状态
    (state, action) => {
      switch (action.type) {
        case 'LIKE_SUCCESS':
          return state + 1;
        case 'LIKE_FAILURE':
          return state;
        default:
          return state;
      }
    },
    // 在操作发起时（如网络请求发送之前）立即更新 UI 显示乐观的结果
    (state, action) => {
      if (action.type === 'LIKE_REQUEST') {
        return state + 1;
      }
      return state;
    }
  );
  
  const handleLike = async () => {
    dispatch({ type: 'LIKE_REQUEST' });
    try {
      // 模拟网络请求
      await new Promise((resolve) => setTimeout(resolve, 1000));
      dispatch({ type: 'LIKE_SUCCESS' });
    } catch (error) {
      dispatch({ type: 'LIKE_FAILURE' });
    }
  };
  return (
    <div>
      <p>点赞数: {likes}</p>
      <button onClick={handleLike}>点赞</button>
    </div>
  );
}
```

**示例说明**：

在`OptimisticLikeButton`中，`useOptimistic`用于管理点赞按钮的状态。初始点赞数为`0`。
`reducer`函数根据不同的动作类型（`LIKE_SUCCESS`、`LIKE_FAILURE`）*来更新点赞数的实际状态*。
`optimisticUpdate`函数在收到`LIKE_REQUEST`动作（即用户点击点赞按钮，网络请求发送之前）时，*立即将点赞数加`1`进行乐观更新*。当用户点击点赞按钮后，通过`dispatch`发送`LIKE_REQUEST`动作，UI 立即更新点赞数加`1`。
然后模拟网络请求，请求成功后发送`LIKE_SUCCESS`动作，状态更新保持点赞数加`1`；*如果请求失败*，发送`LIKE_FAILURE`动作，*点赞数恢复到之前的状态*，从而实现了乐观更新和根据实际情况调整状态的功能。