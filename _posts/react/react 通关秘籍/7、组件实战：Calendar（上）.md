上节我们实现了 mini calendar，为啥要加个 mini 呢？因为它与真实用的 Calendar 组件相比，还是过于简单了。
这节我们再来写一个复杂一些的，真实项目用的 Calendar 组件：

![[react/react 通关秘籍/media/5e91f4083e50982505964b40ec93699a_MD5.gif]]

# 创建项目

用 cra 创建个项目：

```
npx create-react-app --template typescript calendar-component
```

![[react/react 通关秘籍/media/3c7d2bb503e99157a0b799d30d67c99c_MD5.png]]

# 思路

先不着急写，我们**先理一下思路：**

*日历组件的核心是什么？* 是拿到每月的天数，每月的第一天是星期几。

我们知道这个月有 30 天，第一天是周三，那就知道如何显示这个月的日历了。比如这个月：

![[react/react 通关秘籍/media/887dd2f7e0e6b56515784d56642007c7_MD5.png]]


*那如何知道每月的天数呢？* 上节讲过，用 Date 的 api 就可以。当然，也可以用 dayjs，它封装了这些。

安装 dayjs：

```
npm install --save dayjs
```

在 test.js 写如下代码：

```javascript
const dayjs = require('dayjs');

console.log(dayjs('2023-11-1').daysInMonth());

console.log(dayjs('2023-11-1').startOf('month').format('YYYY-MM-DD'));

console.log(dayjs('2023-11-1').endOf('month').format('YYYY-MM-DD'));

```

创建一个 dayjs 的对象，然后用 daysInMonth 方法可以拿到这个月的天数，用 startOf、endOf 可以拿到这个月的第一天和最后一天的日期。

跑一下：

![[react/react 通关秘籍/media/922a71e0bc448af342904f460ba48682_MD5.png]]

这次 Calendar 组件我们用 dayjs 的 api 来实现。很多组件库的 Calendar 组件都是基于 dayjs 设置和返回日期的。比如 antd 的：

![[react/react 通关秘籍/media/bc2ee3f4fba0e8e724fab237cef263b3_MD5.png]]

# 结构和样式

下面正式来写 Calendar 组件。

创建 src/Calendar/index.tsx

```javascript
import './index.scss';

function Calendar() {
    return <div className="calendar">
        
    </div>
}

export default Calendar;
```
还有样式 src/Calendar/index.scss

```scss
.calendar {
    width: 100%;

    height: 200px;
    background: blue;
}
```
这里用到了 scss，需要安装下用到的包：

```
npm install --save sass
```

然后在 App.tsx 里引入 Calendar 组件：

```javascript
import Calendar from './Calendar';

function App() {
  return (
    <div className="App">
      <Calendar></Calendar>
    </div>
  );
}

export default App;
```

跑一下：

```
npm run start
```

![[react/react 通关秘籍/media/13c97e77fb74ee1a39c2e2d0c9d72fa5_MD5.png]]

这样，sass 就引入成功了。

## 日期列表组件

![[react/react 通关秘籍/media/f9d0b271b72f5115ec0bd28144594632_MD5.png]]

这个组件可以分为 Header 和 MonthCalendar 两个组件。

我们先写下面的 MonthCalender 组件。首先是周日到周六的部分：

src/Calendar/MonthCalendar.tsx

```javascript
function MonthCalendar() {

    const weekList = ['周日', '周一', '周二', '周三', '周四', '周五', '周六'];

    return <div className="calendar-month">
        <div className="calendar-month-week-list">
            {weekList.map((week) => (
                <div className="calendar-month-week-list-item" key={week}>
                    {week}
                </div>
            ))}
        </div>
    </div>
}

export default MonthCalendar;
```

先把周日到周一渲染出来，然后在 src/Calendar/index.scss 里写下样式：

```scss
.calendar {
    width: 100%;
}

.calendar-month {
    &-week-list {
        display: flex;
        padding: 0;
        width: 100%;
        box-sizing: border-box;
        border-bottom: 1px solid #ccc;

        &-item {
            padding: 20px 16px;
            text-align: left;
            color: #7d7d7f;
            flex: 1;
        }
    }
}
```

样式用 *display:fex 加 flex:1*，这样就是每个列表项平分剩余空间，然后加上 padding。

在 src/Calendar/index.tsx 里引入：

```javascript
import MonthCalendar from './MonthCalendar';
import './index.scss';

function Calendar() {
    return <div className="calendar">
        <MonthCalendar/>
    </div>
}

export default Calendar;
```
这样，上面的 week list 就完成了：

![[react/react 通关秘籍/media/17ff0fe08fd4922978d3d1bf465b8fcf_MD5.png]]

然后是下面部分：

![[react/react 通关秘籍/media/3b7ce0a68a0b72c850df5115bf1aaf7f_MD5.png]]

思路前面分析过了，就是拿到当前月份的天数和第一天是星期几，前后用上个月和下个月的日期填充。

## 加一个 value 的 props

我们给 Calendar 组件加一个 value 的 props，也就是*当前日期*。

![[react/react 通关秘籍/media/9be4bfc9547c767384d779d4e4a5d9ca_MD5.png]]

value 我们选择用 Dayjs 类型，当然，用 Date 也可以。

```javascript
import { Dayjs } from 'dayjs';
import MonthCalendar from './MonthCalendar';
import './index.scss';

export interface CalendarProps {
    value: Dayjs
}

function Calendar(props: CalendarProps) {
    return <div className="calendar">
        <MonthCalendar {...props}/>
    </div>
}

export default Calendar;

```
在 MonthCalendar 也加上 props：

![[react/react 通关秘籍/media/d418697370a4e872f6319fb3843baa69_MD5.png]]

```javascript
interface MonthCalendarProps extends CalendarProps {

}
```

在 App.tsx 传入参数：

![[react/react 通关秘籍/media/513ed9766754d935f5dbda3ff3a06de8_MD5.png]]

这样，MonthCalendar 就可以根据传入的 value 拿到当前的月份信息了。

我们加一个 getAllDays 方法，打个断点：

![[react/react 通关秘籍/media/48d78f074b75806e740bac155b11dc72_MD5.png]]

```javascript
function getAllDays(date: Dayjs) {
    const daysInMonth = date.daysInMonth();
    const startDate = date.startOf('month');
    const day = startDate.day()    

}
```

```javascript
const allDays = getAllDays(props.value);
```
然后创建个调试配置：

![[react/react 通关秘籍/media/17418e64d1031172f6d8aa92fab27158_MD5.png]]

![[react/react 通关秘籍/media/4de7ae678e4b8512c4758ea3f7ccface_MD5.png]]

点击调试启动：

![[react/react 通关秘籍/media/59d23c72ddf747a66939a6af7efce482_MD5.png]]

可以看到，拿到了这个月的天数，是 30 天。

接下来我们边调试边写。
##  当月和前后月的日期

### 计算日期数据

不管这个月有多少天，我们日历都是固定 6 * 7 个日期：

![[react/react 通关秘籍/media/c03167ae5d32b3d2ec4c45981609f027_MD5.png]]

所以创建一个 6 * 7 个元素的数组。*这个月第一天之前的日期，用第一天的日期 -1、-2、-3 这样计算出来：*

```javascript
function getAllDays(date: Dayjs) {
    const daysInMonth = date.daysInMonth();
    const startDate = date.startOf('month');
    const day = startDate.day()    

    const daysInfo = new Array(6 * 7);

    for(let i = 0 ; i < day; i++) {
        daysInfo[i] = {
            date: startDate.subtract(day - i, 'day').format('YYYY-MM-DD')
        }
    }
    
    debugger;

}
```

*11 月 1 日是星期三：*

![[react/react 通关秘籍/media/06d15f823bd121670f9310a076977a30_MD5.png]]

那也就是要在之前填充星期日、星期一、星期二，*这 3 天的日期：*

![[react/react 通关秘籍/media/076b0f7b173ff624dda88f73cffd0e47_MD5.png]]

*这里用 dayjs 的 subtract 方法就可以计算当前日期 -1、-2、-3 的日期*。

*计算剩下的日期，当月的日期和下月的日期*，点击刷新。这个循环就是填充剩下的日期的，从 startDate 开始 +1、+2、+3 计算日期。

![[react/react 通关秘籍/media/2a9a8e83a8466cc6e26f921df9479f13_MD5.png]]

```javascript
function getAllDays(date: Dayjs) {
    const daysInMonth = date.daysInMonth();
    const startDate = date.startOf('month');
    const day = startDate.day()    

    const daysInfo = new Array(6 * 7);

    for(let i = 0 ; i < day; i++) {
        daysInfo[i] = {
            date: startDate.subtract(day - i, 'day').format('YYYY-MM-DD')
        }
    }

    for(let i = day ; i < daysInfo.length; i++) {
        daysInfo[i] = {
            date: startDate.add(i - day, 'day').format('YYYY-MM-DD')
        }
    }

    debugger;

}
```


hover 上去可以看到，计算的结果是对的：

![[react/react 通关秘籍/media/4d96cf8634c1168f5f47c18f09dfc45c_MD5.png]]

![[react/react 通关秘籍/media/fa441c95a3c62344eca8d3163c7738da_MD5.png]]

然后把 format 删掉，这里不需要格式化。再*添加一个属性标识是否是当前月份的*。

![[react/react 通关秘籍/media/2fa8e9150eb832d1586072b819d43a55_MD5.png]]

```javascript
function getAllDays(date: Dayjs) {
    const startDate = date.startOf('month');
    const day = startDate.day()    

    const daysInfo = new Array(6 * 7);

    for(let i = 0 ; i < day; i++) {
        daysInfo[i] = {
            date: startDate.subtract(day - i, 'day'),
            currentMonth: false
        }
    }

    for(let i = day ; i < daysInfo.length; i++) {
        const calcDate = startDate.add(i - day, 'day');

        daysInfo[i] = {
            date: calcDate,
            currentMonth: calcDate.month() === date.month()
        }
    }

    return daysInfo;
}
```

*就是先 -1、-2、-3 计算本月第一天之前的日期，然后从第一天开始 +1、+2、+3 计算之后日期*。

返回值处打个断点，刷新下：

![[react/react 通关秘籍/media/40fa7fcbb14f39d6160c812117bec883_MD5.png]]

![[react/react 通关秘籍/media/9c9b9f6c66c5734901ad16d6b13fa285_MD5.png]]

当前月份的日期、之前几天的日期、之后几天的日期都有了。这样，日历的数据就准备好了。其实上一节我们也是这么做的，只不过用的是 Date 的 api，而这节换成 dayjs 的 api 了。

再声明下返回的数组的类型：

![[react/react 通关秘籍/media/0f763265d0d7b7d87628144010dca008_MD5.png]]

```javascript
const daysInfo: Array<{date: Dayjs, currentMonth: boolean}> = new Array(6 * 7);
```

### 渲染

数据准备好了，接下来就可以渲染了：

![[react/react 通关秘籍/media/ebe78bf143dd187a7e1f0d1f7380747b_MD5.png]]

```javascript
<div className="calendar-month-body">
    {
        renderDays(allDays)
    }
</div>
```
```javascript
function renderDays(days: Array<{ date: Dayjs, currentMonth: boolean}>) {
    const rows = [];
    for(let i = 0; i < 6; i++ ) {
        const row = [];
        for(let j = 0; j < 7; j++) {
            const item = days[i * 7 + j];
            row[j] = <div className="calendar-month-body-cell">{item.date.date()}</div>
        }
        rows.push(row);
    }
    return rows.map(row => <div className="calendar-month-body-row">{row}</div>)
}
```
这里就是把 6 * 7 个日期，按照 6 行，每行 7 个来组织成 jsx。

scss 部分如下：

![[react/react 通关秘籍/media/ce0358b9facbdbfac9fa1b7b3b87d532_MD5.png]]

```scss
&-body {
    &-row {
        height: 100px;
        display: flex;
    }
    &-cell {
        flex: 1;
        border: 1px solid #eee;
        padding: 10px
    }

}
```
每行的每个单元格用 flex:1 来分配空间，然后设置个 padding。

渲染出来是这样的：

![[react/react 通关秘籍/media/908a4f9595df2f3c6b364f6fb46f7df9_MD5.png]]

然后当前月和其他月份的日期加上个不同颜色区分：

![[react/react 通关秘籍/media/6c5c76f23a9a3aaf26fe82d2026f68e8_MD5.png]]

```javascript
function renderDays(days: Array<{ date: Dayjs, currentMonth: boolean}>) {
    const rows = [];
    for(let i = 0; i < 6; i++ ) {
        const row = [];
        for(let j = 0; j < 7; j++) {
            const item = days[i * 7 + j];
            row[j] = <div className={
                "calendar-month-body-cell " + (item.currentMonth ? 'calendar-month-body-cell-current' : '')
            }>{item.date.date()}</div>
        }
        rows.push(row);
    }
    return rows.map(row => <div className="calendar-month-body-row">{row}</div>)
}
```
![[react/react 通关秘籍/media/5a233a3a2f0d775c322a747a8356feb5_MD5.png]]

```scss
color: #ccc;
&-current {
    color: #000;
}
```

这样，我们的日历就基本完成了：

![[react/react 通关秘籍/media/c4f29aff9a7ef1024464a2d2769f1f40_MD5.png]]

## Header 部分

切换日期是在 Header 部分做的，接下来写下这部分：

![[react/react 通关秘籍/media/5beea75b0fa9bcafdc96009211bcd9d2_MD5.png]]

写下 src/Calendar/Header.tsx：

```javascript
function Header() {
    return <div className="calendar-header">
        <div className="calendar-header-left">
            <div className="calendar-header-icon">&lt;</div>
            <div className="calendar-header-value">2023 年 11 月</div>
            <div className="calendar-header-icon">&gt;</div>
            <button className="calendar-header-btn">今天</button>
        </div>
    </div>
}

export default Header;
```
还有对应的样式：

```scss
.calendar-header {
    &-left {
        display: flex;
        align-items: center;

        height: 28px;
        line-height: 28px;
    }

    &-value {
        font-size: 20px;
    }

    &-btn {
        background: #eee;
        cursor: pointer;
        border: 0;
        padding: 0 15px;
        line-height: 28px;

        &:hover {
            background: #ccc;
        }
    }
  
    &-icon {
        width: 28px;
        height: 28px;

        line-height: 28px;
    
        border-radius: 50%;
        text-align: center;
        font-size: 12px;

        user-select: none;
        cursor: pointer;

        margin-right: 12px;
        &:not(:first-child) {
            margin: 0 12px;
        }

        &:hover {
            background: #ccc;
        }
    }
  
}
```
这部分就是用 flex + margin 来实现布局，就不展开讲了。

渲染出来是这样的：

![[react/react 通关秘籍/media/04bd49d8ad05fba00f4ad6579246e5db_MD5.png]]

这样我们就完成了布局部分。

案例代码上传了[小册仓库](https://github.com/QuarkGluonPlasma/react-course-code/tree/main/calendar-component)。

# 总结

这节我们开始实现一个真实的 Calendar 组件。

我们不再用 Date 获取当前月、上个月、下个月的天数和星期几，而是用 dayjs 的 api。

我们完成了布局部分，包括用于切换月份的 Header 和每个月的日期 MonthCalender 组件。

我们使用 sass 来管理样式。

上面的周几、下面的日期我们都是用的 flex 布局，这样只要外层容器大小变了，内层就会跟着变。

下节我们来实现具体的组件逻辑。
