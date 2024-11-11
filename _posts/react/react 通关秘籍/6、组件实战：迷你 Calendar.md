日历组件想必大家都用过，在各个组件库里都有。比如 antd 的 Calendar 组件（或者 DatePicker 组件）：

![[react/react 通关秘籍/media/8c5134b153982bf8cc94f81d7836c230_MD5.png]]

那这种日历组件是怎么实现的呢？其实原理很简单，今天我们就来自己实现一个。

## Date 的 api

首先，要过一下 Date 的 api，创建 Date 对象时可以传入年月日时分秒。
比如 2023 年 7 月 30，就是这么创建：

```javascript
new Date(2023, 6, 30);
```

可以调用 toLocaleString 来转成当地日期格式的字符串显示：

![[react/react 通关秘籍/media/353758a8246b0d0c930e6767b3e7d8f0_MD5.png]]

### Date 的 month

_7 月为啥第二个参数传 6 呢？_ 因为 Date 的 month 是从 0 开始计数的，取值是 0 到 11：

![[react/react 通关秘籍/media/b4e80dbee8f9eefe47015d776daa0a3c_MD5.png]]

### Date 的 date

而日期 date 是从 1 到 31。_当 date 传 0 的时候，取到的是上个月的最后一天_；-1 就是上个月的倒数第二天；-2 就是倒数第三天这样。

![[react/react 通关秘籍/media/e9b39bbaa05c214b721f6afb847e2065_MD5.png]]

这个小技巧有很大的用处，**可以用这个来拿到每个月有多少天：** 2023 年一月 31 天、二月 28 天、三月 31 天。。。

![[react/react 通关秘籍/media/e845759135654b940274172919c586a5_MD5.png]]

### getFullYear / getMonth / getDate / getDay

除了日期外，也能通过 _getFullYear、getMonth、getDate 拿到年份、月份、日期_：

![[react/react 通关秘籍/media/27e4cf7d395410f83da4d13b15758fef_MD5.png]]

还可以通过 _getDay 拿到星期几_。比如今天（2023-7-19）是星期三：

![[react/react 通关秘籍/media/ae0a73ebb7d587648e34d801a8508a0b_MD5.png]]

就这么几个 api 就已经可以实现日历组件了。

## 创建 typescript 的 react 项目

用 cra 创建 typescript 的 react 项目：

```
npx create-react-app --template=typescript mini-calendar-test
```

![[react/react 通关秘籍/media/269cd03c7daf7f9da01f5da337570e1a_MD5.png]]

### 静态的布局

我们先来写下静态的布局。一个 header，然后是从星期日到星期六，再下面是从 1 到 31：

![[react/react 通关秘籍/media/1f51892afb5a8d3bd9be902e86efd50c_MD5.png]]

改下 App.tsx:

```javascript
import React from "react";
import "./index.css";

function Calendar() {
  return (
    <div className="calendar">
      <div className="header">
        <button>&lt;</button>
        <div>2023 年 7 月</div>
        <button>&gt;</button>
      </div>
      <div className="days">
        <div className="day">日</div>
        <div className="day">一</div>
        <div className="day">二</div>
        <div className="day">三</div>
        <div className="day">四</div>
        <div className="day">五</div>
        <div className="day">六</div>
        <div className="empty"></div>
        <div className="empty"></div>
        <div className="day">1</div>
        <div className="day">2</div>
        <div className="day">3</div>
        <div className="day">4</div>
        <div className="day">5</div>
        <div className="day">6</div>
        <div className="day">7</div>
        <div className="day">8</div>
        <div className="day">9</div>
        <div className="day">10</div>
        <div className="day">11</div>
        <div className="day">12</div>
        <div className="day">13</div>
        <div className="day">14</div>
        <div className="day">15</div>
        <div className="day">16</div>
        <div className="day">17</div>
        <div className="day">18</div>
        <div className="day">19</div>
        <div className="day">20</div>
        <div className="day">21</div>
        <div className="day">22</div>
        <div className="day">23</div>
        <div className="day">24</div>
        <div className="day">25</div>
        <div className="day">26</div>
        <div className="day">27</div>
        <div className="day">28</div>
        <div className="day">29</div>
        <div className="day">30</div>
        <div className="day">31</div>
      </div>
    </div>
  );
}

export default Calendar;
```

直接跑起来看下渲染结果再讲布局：

```
npm run start
```

![[react/react 通关秘籍/media/db7a0d0245055f884125e707920d1df5_MD5.png]]

这种布局还是挺简单的。

#### CSS

header 就是一个 space-between 的 flex 容器：

![[react/react 通关秘籍/media/d6e21a6c2d4417fe9e9e9ae846b227a3_MD5.png]]

下面是一个 flex-wrap 为 wrap，每个格子宽度为 100% / 7 的 flex 容器：

![[react/react 通关秘籍/media/e63268ba280e16ae8ea290a8e36e75ee_MD5.png]]

![[react/react 通关秘籍/media/6e62d124e66cc32b3bc460726546ef52_MD5.png]]

全部样式如下：

```css
.calendar {
  border: 1px solid #aaa;
  padding: 10px;
  width: 300px;
  height: 250px;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 40px;
}

.days {
  display: flex;
  flex-wrap: wrap;
}

.empty,
.day {
  width: calc(100% / 7);
  text-align: center;
  line-height: 30px;
}

.day:hover {
  background-color: #ccc;
  cursor: pointer;
}
```

### 逻辑

然后我们再来写逻辑。

#### 左右按钮

![[react/react 通关秘籍/media/691f52c742b36492466168f7952249a0_MD5.png]]

首先，我们肯定要有一个 state 来保存*当前的日期*，默认值是今天。

然后点击左右按钮，会切换到上个月、下个月的第一天。

```javascript
const [date, setDate] = useState(new Date());

const handlePrevMonth = () => {
  setDate(new Date(date.getFullYear(), date.getMonth() - 1, 1));
};

const handleNextMonth = () => {
  setDate(new Date(date.getFullYear(), date.getMonth() + 1, 1));
};
```

#### 渲染的年月

然后渲染的年月要改为当前 date 对应的年月：

![[react/react 通关秘籍/media/b40314e79c7c8f5b2d822aa16c3e449f_MD5.png]]

```javascript
const monthNames = [
  "一月",
  "二月",
  "三月",
  "四月",
  "五月",
  "六月",
  "七月",
  "八月",
  "九月",
  "十月",
  "十一月",
  "十二月",
];
```

我们试试看：

![[react/react 通关秘籍/media/08d31fc30a34ece5c3e8fa6d8acec1be_MD5.gif]]

年月部分没问题了。

#### 日期部分

再来改下日期部分。我们定义一个 renderDays 方法：

```tsx
//计算当前月有多少天
const daysOfMonth = (year: number, month: number) => {
  return new Date(year, month + 1, 0).getDate();
};
//再计算当前月的第一天是星期几
const firstDayOfMonth = (year: number, month: number) => {
  return new Date(year, month, 1).getDay();
};

const renderDays = () => {
  //存储渲染的内容
  const days = [];
  //计算当前月有多少天
  const daysCount = daysOfMonth(date.getFullYear(), date.getMonth());
  //再计算当前月的第一天是星期几，这样就知道从哪里开始渲染，渲染多少天了
  const firstDay = firstDayOfMonth(date.getFullYear(), date.getMonth());

  //然后先一个循环，渲染 day - 1 个 empty 的块
  for (let i = 0; i < firstDay; i++) {
    days.push(<div key={`empty-${i}`} className="empty"></div>);
  }
  //再渲染 daysCount 个 day 的块
  for (let i = 1; i <= daysCount; i++) {
    days.push(
      <div key={i} className="day">
        {i}
      </div>
    );
  }

  return days;
};
```

- 首先定义个数组，来存储渲染的内容。

- _然后计算当前月有多少天_。这里用到了前面那个 new Date 时传入 date 为 0 的技巧。

- _再计算当前月的第一天是星期几_。也就是 new Date(year, month, 1).getDay()，_这样就知道从哪里开始渲染，渲染多少天了_。

- 然后先一个循环，渲染 day - 1 个 empty 的块。

- 再渲染 daysCount 个 day 的块。

这样就完成了日期渲染。

![[react/react 通关秘籍/media/6af7bc12377b3c733409261db6838824_MD5.png]]

我们来试试看：

![[react/react 通关秘籍/media/43f58c3901fbfcd86d07bd72d526dfe6_MD5.gif]]

没啥问题。这样，我们就完成了一个 Calendar 组件！是不是还挺简单的？确实，Calendar 组件的原理比较简单。

#### value 和 onChange

接下来，我们增加两个参数，value 和 onChange。

这俩参数和 antd 的 Calendar 组件一样。

**value**

_value 参数设置为 date 的初始值：_

![[react/react 通关秘籍/media/f8b8301b4fce70c3bf5a4880e54045f2_MD5.png]]

我们试试看：

![[react/react 通关秘籍/media/a1aec819e24f91477b8ea0ff756b9601_MD5.png]]

![[react/react 通关秘籍/media/86c56d950dc62eeb290b7787ef53a693_MD5.png]]

年月是对了，但是日期对不对我们也看不出来，所以还得*加点选中样式：*

![[react/react 通关秘籍/media/fcdd770e642ed736974e7f8e38729602_MD5.png]]

![[react/react 通关秘籍/media/cb9c27d9f3ea95b133842e57a1193456_MD5.png]]

现在就可以看到选中的日期了：

![[react/react 通关秘籍/media/38523fedd157c3ca9c10b8bdf701bb3d_MD5.png]]

没啥问题。

**onChange 的回调函数**

然后我们再*加上 onChange 的回调函数：*

![[react/react 通关秘籍/media/992f2b5bfe441c2b2a9c3d72da3da17f_MD5.png]]

```javascript
const clickHandler = onChange?.bind(
  null,
  new Date(date.getFullYear(), date.getMonth(), i)
);
```

就是在*点击 day 的时候，调用 bind 了对应日期的 onChange 函数*。

我们试试看：

![[react/react 通关秘籍/media/6eed21e635157f7a20f210845e82ae7c_MD5.png]]

![[react/react 通关秘籍/media/23358237429acfe6942b5e3321d98bf0_MD5.gif]]

也没啥问题。现在这个 Calendar 组件就是可用的了，可以通过 value 来传入初始的 date 值，修改 date 之后可以在 onChange 里拿到最新的值。
大多数人到了这一步就完成 Calendar 组件的封装了。这当然没啥问题。

#### 提供 ref 属性

但其实你还可以再做一步，**提供 ref 来暴露一些 Canlendar 组件的 api**。

![[react/react 通关秘籍/media/e209a60fba71ae45477ca06259d0a3ea_MD5.png]]

![[react/react 通关秘籍/media/a3ff8386b8504df6333ee9b903f6c11d_MD5.png]]

前面章节讲过，**useImperativeHandle 可以自定义 ref 的内容**。

然后用的时候就可以*通过 useRef 创建 ref 对象，设置到 Calendar 的 ref 属性上，拿到转发出的 ref 内容：*

![[react/react 通关秘籍/media/58312661b5d0cba3ed171fcfafd7b593_MD5.png]]

试试看：

![[react/react 通关秘籍/media/602d4988a4f0f3dfe98d50c6a5d7426b_MD5.gif]]

ref 的 api 也都生效了。

**这就是除了 props 之外，另一种暴露组件 api 的方式。**

#### 渲染前后月份的日期

现在的 Calender 除了本月的日期外，其余的地方是用空白填充的。很多日历用的是上个月、下个月的日期。

![[react/react 通关秘籍/media/478f62e107f8a358830c82cce1e5d68f_MD5.png]]

这个也很简单，new Date(year, month + 1???, 0) 是拿到当前月的*最后一天~~第一天~~*，那 -1 就是上个月的最后一天，-2 就是倒数第二天。

下个月的也是同理，用当前月最后一天 +1、+2 即可。

全部代码如下：

```tsx
import React, { useEffect, useImperativeHandle, useRef, useState } from "react";
import "./index.css";

interface CalendarProps {
  value?: Date;
  onChange?: (date: Date) => void;
}

interface CalendarRef {
  getDate: () => Date;
  setDate: (date: Date) => void;
}

const InternalCalendar: React.ForwardRefRenderFunction<
  CalendarRef,
  CalendarProps
> = (props, ref) => {
  const { value = new Date(), onChange } = props;

  const [date, setDate] = useState(value);

  useImperativeHandle(ref, () => {
    return {
      getDate() {
        return date;
      },
      setDate(date: Date) {
        setDate(date);
      },
    };
  });

  const handlePrevMonth = () => {
    setDate(new Date(date.getFullYear(), date.getMonth() - 1, 1));
  };

  const handleNextMonth = () => {
    setDate(new Date(date.getFullYear(), date.getMonth() + 1, 1));
  };

  const monthNames = [
    "一月",
    "二月",
    "三月",
    "四月",
    "五月",
    "六月",
    "七月",
    "八月",
    "九月",
    "十月",
    "十一月",
    "十二月",
  ];

  const daysOfMonth = (year: number, month: number) => {
    return new Date(year, month + 1, 0).getDate();
  };

  const firstDayOfMonth = (year: number, month: number) => {
    return new Date(year, month, 1).getDay();
  };

  const renderDays = () => {
    const days = [];

    const daysCount = daysOfMonth(date.getFullYear(), date.getMonth());
    const firstDay = firstDayOfMonth(date.getFullYear(), date.getMonth());

    for (let i = 0; i < firstDay; i++) {
      days.push(<div key={`empty-${i}`} className="empty"></div>);
    }

    for (let i = 1; i <= daysCount; i++) {
      const clickHandler = onChange?.bind(
        null,
        new Date(date.getFullYear(), date.getMonth(), i)
      );
      if (i === date.getDate()) {
        days.push(
          <div key={i} className="day selected" onClick={clickHandler}>
            {i}
          </div>
        );
      } else {
        days.push(
          <div key={i} className="day" onClick={clickHandler}>
            {i}
          </div>
        );
      }
    }

    return days;
  };

  return (
    <div className="calendar">
      <div className="header">
        <button onClick={handlePrevMonth}>&lt;</button>
        <div>
          {date.getFullYear()}年{monthNames[date.getMonth()]}
        </div>
        <button onClick={handleNextMonth}>&gt;</button>
      </div>
      <div className="days">
        <div className="day">日</div>
        <div className="day">一</div>
        <div className="day">二</div>
        <div className="day">三</div>
        <div className="day">四</div>
        <div className="day">五</div>
        <div className="day">六</div>
        {renderDays()}
      </div>
    </div>
  );
};

const Calendar = React.forwardRef(InternalCalendar);

function Test() {
  const calendarRef = useRef < CalendarRef > null;

  useEffect(() => {
    console.log(calendarRef.current?.getDate().toLocaleDateString());

    setTimeout(() => {
      calendarRef.current?.setDate(new Date(2024, 3, 1));
    }, 3000);
  }, []);

  return (
    <div>
      {/* <Calendar value={new Date('2023-3-1')} onChange={(date: Date) => {
        alert(date.toLocaleDateString());
    }}></Calendar> */}
      <Calendar ref={calendarRef} value={new Date("2024-8-15")}></Calendar>
    </div>
  );
}
export default Test;
```

```css
.calendar {
  border: 1px solid #aaa;
  padding: 10px;
  width: 300px;
  height: 250px;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 40px;
}

.days {
  display: flex;
  flex-wrap: wrap;
}

.empty,
.day {
  width: calc(100% / 7);
  text-align: center;
  line-height: 30px;
}

.day:hover,
.selected {
  background-color: #ccc;
  cursor: pointer;
}
```

案例代码上传了[小册仓库](https://github.com/QuarkGluonPlasma/react-course-code/tree/main/mini-calendar-test)。

## 总结

Calendar 或者 DatePicker 组件我们经常会用到，今天自己实现了一下。

其实原理也很简单，就是 Date 的 api。new Date 的时候 date 传 0 就能拿到*上个月最后一天*的日期，然后 getDate 就可以*知道那个月有多少天*。然后再通过 getDay 取到这个月第一天是*星期几*，_就知道怎么渲染这个月的日期了_。

我们用 react 实现了这个 Calendar 组件，支持传入 value 指定初始日期，传入 onChange 作为日期改变的回调。

除了 props 之外，还额外提供 ref 的 api，通过 forwardRef + useImperativeHandle 的方式。

整天用 Calender 组件，不如自己手写一个吧！
