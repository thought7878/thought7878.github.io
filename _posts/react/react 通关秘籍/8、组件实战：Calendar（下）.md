# 添加一些属性

基本的布局完成了，我们来添加一些参数：

![[react/react 通关秘籍/media/8e629ab7cff504145d70df9d5f7e6ce0_MD5.png]]

```javascript
export interface CalendarProps {
    value: Dayjs;
    style?: CSSProperties;
    className?: string | string[];
    // 定制日期显示，会完全覆盖日期单元格
    dateRender?: (currentDate: Dayjs) => ReactNode;
    // 定制日期单元格，内容会被添加到单元格内，只在全屏日历模式下生效。
    dateInnerContent?: (currentDate: Dayjs) => ReactNode;
    // 国际化相关
    locale?: string;
    onChange?: (date: Dayjs) => void;
}
```

*style 和 className* 用于修改 Calendar 组件外层容器的样式。内部的布局我们都是用的 flex，所以只要外层容器的样式变了，内部的布局会自动适应。

*dateRender* 是用来定制日期单元格显示的内容的。比如加一些日程安排，加一些农历或者节日信息：

![[react/react 通关秘籍/media/c8ebeadd0ead061a765c7b57aa330ff4_MD5.png]]

![[react/react 通关秘籍/media/19d30465a04918b127397ba0016a7323_MD5.png]]

*dateRender* 是整个覆盖，连带日期的数字一起，而 *dateInnerContent* 只会在日期的数字下添加一些内容。这两个 props 是不一样的。

*locale* 是用于国际化的，比如切换到中文显示或者是英文显示。

*onChange* 是当选择了日期之后会触发的回调。

然后实现下这些参数对应的逻辑。

## className 和 style

首先是 className 和 style：

![[react/react 通关秘籍/media/28d53d77de4412e4ee2c559f8eb98ed0_MD5.png]]

```javascript
function Calendar(props: CalendarProps) {

    const {
        value,
        style,
        className,
    } = props;

    const classNames = cs("calendar", className);

    return <div className={classNames} style={style}>
        <Header></Header>
        <MonthCalendar {...props}/>
    </div>
}
```
这里用 classnames 这个包来做 className 的合并。

```
npm install classnames
```
它可以传入对象或者数组，会自动合并，返回最终的 className：

![[react/react 通关秘籍/media/994abe1e5076b0cbbb2945dda3413b20_MD5.png]]

![[react/react 通关秘籍/media/d15d79377cecda7762c794deb52d4ebb_MD5.png]]

当 className 确定需要一段复杂计算逻辑的时候，就用 classname 这个包。

测试下：

![[react/react 通关秘籍/media/714dcfea8a3967e6d2c3093735e409a4_MD5.png]]

```javascript
import dayjs from 'dayjs';
import Calendar from './Calendar';

function App() {
  return (
    <div className="App">
      <Calendar value={dayjs('2023-11-08')} className={'aaa'} style={{background: 'yellow'}}></Calendar>
    </div>
  );
}

export default App;

```

![[react/react 通关秘籍/media/005d89aac6f9abaadb15641813fb25bd_MD5.png]]

![[react/react 通关秘籍/media/56215558212c3c467d27a4c5d6adf92c_MD5.png]]

className 和 style 的处理没问题。

## dateRender 和 dateInnerContent

然后我们处理下一个 props： dateRender 和 dateInnerContent。

在 MonthCalendar 里把它取出来，传入到 renderDays 方法里：

![[react/react 通关秘籍/media/cac6572ec90cbaa2cbab1958c78a96a7_MD5.png]]

```javascript
const {
    dateRender,
    dateInnerContent
} = props;
```
```javascript
renderDays(allDays, dateRender, dateInnerContent)
```
dateRender 的处理也很简单，就是把渲染日期的逻辑换一下：

![[react/react 通关秘籍/media/e2cc8390d0ffb2cc13560430a2fd5f0a_MD5.png]]

在 App.tsx 里传入 dateRender 参数：

![[react/react 通关秘籍/media/fec4e689d315d11df461ea854e92432d_MD5.png]]

```javascript
import dayjs from 'dayjs';
import Calendar from './Calendar';

function App() {
  return (
    <div className="App">
      <Calendar value={dayjs('2023-11-08')} dateRender={(value) => {
        return <div>
          <p style={{background: 'yellowgreen', height: '50px'}}>{value.format('YYYY/MM/DD')}</p>
        </div>
      }}></Calendar>
    </div>
  );
}

export default App;
```
这样，渲染的内容就换成自定义的了：

![[react/react 通关秘籍/media/1b306b7dcae7af03b464a4aa6fff8200_MD5.png]]

不过现在我们没有做内容溢出时的处理：

![[react/react 通关秘籍/media/5d0fb9f4d8d09cb30bf2cc239ad80843_MD5.png]]

![[react/react 通关秘籍/media/6e720b52d9378b03773926edb161d195_MD5.png]]

*加个 overflow: hidden 就好了：*

![[react/react 通关秘籍/media/9e97c73ba8831f187f1912d87a073ae9_MD5.png]]

![[react/react 通关秘籍/media/c5521dd975236a3c15d90606306d5adb_MD5.png]]
而且之前加 padding 的位置也不对。

改一下渲染日期的逻辑，*如果传了 dateRender 那就整个覆盖日期单元格，否则就是只在下面渲染 dateInnerContent 的内容：*

![[react/react 通关秘籍/media/93e6c34d229b1602c1bb3c4e952b0a70_MD5.png]]

```javascript
function renderDays(
    days: Array<{ date: Dayjs, currentMonth: boolean}>,
    dateRender: MonthCalendarProps['dateRender'],
    dateInnerContent:  MonthCalendarProps['dateInnerContent']
) {
    const rows = [];
    for(let i = 0; i < 6; i++ ) {
        const row = [];
        for(let j = 0; j < 7; j++) {
            const item = days[i * 7 + j];
            row[j] = <div className={
                "calendar-month-body-cell " + (item.currentMonth ? 'calendar-month-body-cell-current' : '')
            }>
                {
                    dateRender ? dateRender(item.date) : (
                        <div className="calendar-month-body-cell-date">
                            <div className="calendar-month-body-cell-date-value">{item.date.date()}</div>
                            <div className="calendar-month-body-cell-date-content">{dateInnerContent?.(item.date)}</div>
                        </div>
                    )
                }
            </div>
        }
        rows.push(row);
    }
    return rows.map(row => <div className="calendar-month-body-row">{row}</div>)
}
```
改下对应的样式：

![[react/react 通关秘籍/media/7f9a47e4edd186b7f3ea143e1803799e_MD5.png]]

把加 padding 的位置改为内部的元素。

测试下：

![[react/react 通关秘籍/media/db2d0af254847e689d19183313a4add1_MD5.png]]

```javascript
import dayjs from 'dayjs';
import Calendar from './Calendar';

function App() {
  return (
    <div className="App">
      <Calendar value={dayjs('2023-11-08')} dateInnerContent={(value) => {
        return <div>
          <p style={{background: 'yellowgreen', height: '30px'}}>{value.format('YYYY/MM/DD')}</p>
        </div>
      }}></Calendar>
    </div>
  );
}

export default App;
```

![[react/react 通关秘籍/media/94599260170eda744066b902d0b8288f_MD5.png]]

这样，dateRender 和 dateInnerContent 的逻辑就完成了。

## locale

接下来做国际化，也就是 locale 参数的处理。

国际化就是可以让日历支持中文、英文、日文等，其实也很简单，就是**把写死的文案换成按照 key 从配置中取的文案就行了**。

定义下用到的 ts 类型 src/Calendar/locale/interface.ts

```javascript
export interface CalendarType {
    formatYear: string;
    formatMonth: string;
    today: string;
    month: {
        January: string;
        February: string;
        March: string;
        April: string;
        May: string;
        June: string;
        July: string;
        August: string;       
        September: string;
        October: string;
        November: string;
        December: string;
    } & Record<string, any>;
    week: {
        monday: string;
        tuesday: string;
        wednesday: string;
        thursday: string;
        friday: string;
        saturday: string;
        sunday: string;
    } & Record<string, any>
}
```
然后分别定义中文和英文的配置：

src/Calendar/locale/zh-CN.ts

```javascript
import { CalendarType } from "./interface";

const CalendarLocale: CalendarType = {
    formatYear: 'YYYY 年',
    formatMonth: 'YYYY 年 MM 月',
    today: '今天',
    month: {
        January: '一月',
        February: '二月',
        March: '三月',
        April: '四月',
        May: '五月',
        June: '六月',
        July: '七月',
        August: '八月',
        September: '九月',
        October: '十月',
        November: '十一月',
        December: '十二月',
    },
    week: {
        monday: '周一',
        tuesday: '周二',
        wednesday: '周三',
        thursday: '周四',
        friday: '周五',
        saturday: '周六',
        sunday: '周日',
    }
}

export default CalendarLocale;
```

src/Calendar/locale/zh-CN.ts

把会用到的文案列出来。

然后再写个英文版：

src/Calendar/locale/en-US.ts

```javascript
import { CalendarType } from "./interface";

const CalendarLocale: CalendarType = {
    formatYear: 'YYYY',
    formatMonth: 'MMM YYYY',
    today: 'Today',
    month: {
        January: 'January',
        February: 'February',
        March: 'March',
        April: 'April',
        May: 'May',
        June: 'June',
        July: 'July',
        August: 'August',
        September: 'September',
        October: 'October',
        November: 'November',
        December: 'December',
    },
    week: {
        monday: 'Monday',
        tuesday: 'Tuesday',
        wednesday: 'Wednesday',
        thursday: 'Thursday',
        friday: 'Friday',
        saturday: 'Saturday',
        sunday: 'Sunday',
    },
}

export default CalendarLocale;
```

我们先把上面的周一到周日的文案替换了：

![[react/react 通关秘籍/media/703a86a0a7d3d58452d5a5bf7c5f5a52_MD5.png]]

在 MonthCalendar 引入中文的资源包：

![[react/react 通关秘籍/media/b3647e6e85705b198e0d346e6974b7e8_MD5.png]]

然后把之前写死的文案，改成按照 key 从资源包中取值的方式：

![[react/react 通关秘籍/media/42fc33432ff4c6894a7c2e71f0e70e8f_MD5.png]]

```javascript
function MonthCalendar(props: MonthCalendarProps) {

    const {
        dateRender,
        dateInnerContent
    } = props;

    const weekList = ['sunday', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday']

    const allDays = getAllDays(props.value);

    return <div className="calendar-month">
        <div className="calendar-month-week-list">
            {weekList.map((week) => (
                <div className="calendar-month-week-list-item" key={week}>
                    {CalendarLocale.week[week]}
                </div>
            ))}
        </div>
        <div className="calendar-month-body">
            {
                renderDays(allDays, dateRender, dateInnerContent)
            }
        </div>
    </div>
}
```

现在渲染出来的是这样的：

![[react/react 通关秘籍/media/378453996642768912322410b6cba488_MD5.png]]

只要改一下用的资源包：

![[react/react 通关秘籍/media/591d84d57e8158f7355ff476b833f0db_MD5.png]]

文案就变了：

![[react/react 通关秘籍/media/737d111baf493d49eab428245952a158_MD5.png]]

这就是国际化。

### context

当然，现在我们是手动切换的资源包，其实应该是全局统一配置的。

这个可以通过 context 来做：

新建 src/Calendar/LocaleContext.tsx

```javascript
import { createContext } from "react";

export interface LocaleContextType {
    locale: string;
}

const LocaleContext = createContext<LocaleContextType>({
    locale: 'zh-CN'
});

export default LocaleContext;
```
然后在 Calendar 组件里用 provider 修改 context 的值：

![[react/react 通关秘籍/media/63692d439afa26ced92a146d4d568aab_MD5.png]]

*如果传入了参数，就用指定的 locale，否则，就从浏览器取当前语言：*

![[react/react 通关秘籍/media/cc562a969402cefd20c880c8556086fe_MD5.png]]

加一个国际化资源包的入口：

src/Calendar/locale/index.ts

```javascript
import zhCN from "./zh-CN";
import enUS from "./en-US";
import { CalendarType } from "./interface";

const allLocales: Record<string, CalendarType>= {
    'zh-CN': zhCN,
    'en-US': enUS
}

export default allLocales;
```
把 MonthCalendar 组件的 locale 改成从 context 获取的：

![[react/react 通关秘籍/media/f0093b2a96573c54dbd8d83fb16f47e5_MD5.png]]

```javascript
const localeContext = useContext(LocaleContext);

const CalendarLocale = allLocales[localeContext.locale];
```

这样，当不指定 locale 时，就会按照浏览器的语言来设置：

![[react/react 通关秘籍/media/8a1faa77e19eaa9137ca548616edaee5_MD5.png]]

当指定 locale 时，就会切换为指定语言的资源包：

![[react/react 通关秘籍/media/e3388271518b8267bc60c371905f07a4_MD5.png]]

![[react/react 通关秘籍/media/ed74c776c3eec56a637f20b4e96039ee_MD5.png]]

## value 和 onChange

接下来，我们实现 value 和 onChange 参数的逻辑。
### value
在 MonthCalendar 里取出 value 参数，传入 renderDays 方法：

![[react/react 通关秘籍/media/84f3d74be19d8fd4acb0c3ea4996e2d9_MD5.png]]

用 classnames 的 api 来拼接 className，如果是当前日期，就加一个 xxx-selected 的 className：

![[react/react 通关秘籍/media/cdb5f4b980af79def662a8383659201f_MD5.png]]

```javascript
function renderDays(
    days: Array<{ date: Dayjs, currentMonth: boolean}>,
    dateRender: MonthCalendarProps['dateRender'],
    dateInnerContent:  MonthCalendarProps['dateInnerContent'],
    value: Dayjs
) {
    const rows = [];
    for(let i = 0; i < 6; i++ ) {
        const row = [];
        for(let j = 0; j < 7; j++) {
            const item = days[i * 7 + j];
            row[j] = <div className={
                "calendar-month-body-cell " + (item.currentMonth ? 'calendar-month-body-cell-current' : '')
            }
            >
                {
                    dateRender ? dateRender(item.date) : (
                        <div className="calendar-month-body-cell-date">
                            <div className={
                                cs("calendar-month-body-cell-date-value",
                                    value.format('YYYY-MM-DD') === item.date.format('YYYY-MM-DD')
                                        ? "calendar-month-body-cell-date-selected"
                                        : ""
                                )
                            }>{item.date.date()}</div>
                            <div className="calendar-month-cell-body-date-content">{dateInnerContent?.(item.date)}</div>
                        </div>
                    )
                }
            </div>
        }
        rows.push(row);
    }
    return rows.map(row => <div className="calendar-month-body-row">{row}</div>)
}
```
添加对应的样式：

![[react/react 通关秘籍/media/2c358f73017a65c7206c1a7676222676_MD5.png]]

```css
&-selected {
    background: blue;
    width: 28px;
    height: 28px;
    line-height: 28px;
    text-align: center;
    color: #fff;
    border-radius: 50%;
    cursor: pointer;
}
```
现在渲染出来是这样的：

![[react/react 通关秘籍/media/2bbf006ffd5262ee0851d9d2fd48eba4_MD5.png]]

### onChange

然后我们加上点击的处理：

![[react/react 通关秘籍/media/02df778ce9d79e6588ff55408867f876_MD5.png]]

```javascript
interface MonthCalendarProps extends CalendarProps {
    selectHandler?: (date: Dayjs) => void
}
```
添加一个 selectHandler 的参数，传给 renderDays 方法。

![[react/react 通关秘籍/media/51bae714c84645eb762b2fa206c5cb4c_MD5.png]]

renderDays 方法里取出来，给日期添加上点击事件：

![[react/react 通关秘籍/media/4e09dd5bf05dead6020e21b2814a5d8d_MD5.png]]

```javascript
function renderDays(
    days: Array<{ date: Dayjs, currentMonth: boolean}>,
    dateRender: MonthCalendarProps['dateRender'],
    dateInnerContent:  MonthCalendarProps['dateInnerContent'],
    value: Dayjs,
    selectHandler: MonthCalendarProps['selectHandler']
) {
    const rows = [];
    for(let i = 0; i < 6; i++ ) {
        const row = [];
        for(let j = 0; j < 7; j++) {
            const item = days[i * 7 + j];
            row[j] = <div className={
                "calendar-month-body-cell " + (item.currentMonth ? 'calendar-month-body-cell-current' : '')
            }
                onClick={() => selectHandler?.(item.date)}
            >
                {
                    dateRender ? dateRender(item.date) : (
                        <div className="calendar-month-body-cell-date">
                            <div className={
                                cs("calendar-month-body-cell-date-value",
                                    value.format('YYYY-MM-DD') === item.date.format('YYYY-MM-DD')
                                        ? "calendar-month-body-cell-date-selected"
                                        : ""
                                )
                            }>{item.date.date()}</div>
                            <div className="calendar-month-cell-body-date-content">{dateInnerContent?.(item.date)}</div>
                        </div>
                    )
                }
            </div>
        }
        rows.push(row);
    }
    return rows.map(row => <div className="calendar-month-body-row">{row}</div>)
}
```
然后这个参数是在 Calendar 组件传进来的：

![[react/react 通关秘籍/media/1820dc38fa93f92eb893243347f05ae6_MD5.png]]

我们添加一个 state 来存储当前日期，selectHandler 里调用 onChange 的参数，并且修改当前日期。

```javascript
function Calendar(props: CalendarProps) {

    const {
        value,
        style,
        className,
        dateRender,
        dateInnerContent,
        locale,
        onChange
    } = props;

    const [curValue, setCurValue] = useState<Dayjs>(value);

    const classNames = cs("calendar", className);
        
    function selectHandler(date: Dayjs) {
        setCurValue(date);
        onChange?.(date);
    }

    return <LocaleContext.Provider value={{
        locale: locale || navigator.language
    }}>
        <div className={classNames} style={style}>
            <Header></Header>
            <MonthCalendar {...props} value={curValue} selectHandler={selectHandler}/>
        </div>
    </LocaleContext.Provider>
}
```
试一下，改下 App.tsx：

```javascript
import dayjs from 'dayjs';
import Calendar from './Calendar';

function App() {
  return (
    <div className="App">
      <Calendar value={dayjs('2023-11-08')} onChange={(date) => {
          alert(date.format('YYYY-MM-DD'));
      }}></Calendar>
    </div>
  );
}

export default App;
```

![[react/react 通关秘籍/media/e51c0f2c33c49ff762a444d3f2193355_MD5.gif]]

# Header 组件里的日期切换 

然后实现下 Header 组件里的日期切换：

![[react/react 通关秘籍/media/7fca29540402fa8619c356dd55e75d29_MD5.png]]

根据传入的 value 来展示日期，点击上下按钮的时候会调用传进来的回调函数：

```javascript
import { Dayjs } from "dayjs";
interface HeaderProps {
    curMonth: Dayjs;
    prevMonthHandler: () => void;
    nextMonthHandler: () => void;
}
function Header(props: HeaderProps) {

    const {
        curMonth,
        prevMonthHandler,
        nextMonthHandler
    } = props;

    return <div className="calendar-header">
        <div className="calendar-header-left">
            <div className="calendar-header-icon" onClick={prevMonthHandler}>&lt;</div>
            <div className="calendar-header-value">{curMonth.format('YYYY 年 MM 月')}</div>
            <div className="calendar-header-icon" onClick={nextMonthHandler}>&gt;</div>
            <button className="calendar-header-btn">今天</button>
        </div>
    </div>
}

export default Header;
```

然后在 Calendar 组件创建 curMonth 的 state，点击上下按钮的时候，修改月份：

![[react/react 通关秘籍/media/e9e39a089cd4bed9ca5faf78b182f954_MD5.png]]

```javascript
function Calendar(props: CalendarProps) {

    const {
        value,
        style,
        className,
        dateRender,
        dateInnerContent,
        locale,
        onChange
    } = props;

    const [curValue, setCurValue] = useState<Dayjs>(value);

    const [curMonth, setCurMonth] = useState<Dayjs>(value);

    const classNames = cs("calendar", className);
        
    function selectHandler(date: Dayjs) {
        setCurValue(date);
        onChange?.(date);
    }

    function prevMonthHandler() {
        setCurMonth(curMonth.subtract(1, 'month'));
    }

    function nextMonthHandler() {
        setCurMonth(curMonth.add(1, 'month'));
    }

    return <LocaleContext.Provider value={{
        locale: locale || navigator.language
    }}>
        <div className={classNames} style={style}>
            <Header curMonth={curMonth} prevMonthHandler={prevMonthHandler} nextMonthHandler={nextMonthHandler}></Header>
            <MonthCalendar {...props} value={curValue} selectHandler={selectHandler}/>
        </div>
    </LocaleContext.Provider>
}
```

测试下：

![[react/react 通关秘籍/media/380e3196dd7bb239ea28912e52a3260a_MD5.gif]]

# 切换日期表格

但现在月份是变了，但下面的日历没有跟着变。
*因为我们之前是拿到 value 所在月份来计算的日历，现在要改成 curMonth 所在的月份。*

![[react/react 通关秘籍/media/f4efb03fb55566fa503a73a1bb349caf_MD5.png]]

![[react/react 通关秘籍/media/b5bee5e5157fe374af741b74297c2c5f_MD5.png]]

这样，月份切换时，就会显示那个月的日历了：

![[react/react 通关秘籍/media/4545416d9c7c306b0f3538880de33894_MD5.gif]]

# 今天按钮

然后我们加上今天按钮的处理：

![[react/react 通关秘籍/media/fbd6771de086c1197465a2ffdfcebd8a_MD5.png]]

```javascript
import { Dayjs } from "dayjs";
interface HeaderProps {
    curMonth: Dayjs;
    prevMonthHandler: () => void;
    nextMonthHandler: () => void;
    todayHandler: () => void;
}
function Header(props: HeaderProps) {

    const {
        curMonth,
        prevMonthHandler,
        nextMonthHandler,
        todayHandler
    } = props;

    return <div className="calendar-header">
        <div className="calendar-header-left">
            <div className="calendar-header-icon" onClick={prevMonthHandler}>&lt;</div>
            <div className="calendar-header-value">{curMonth.format('YYYY 年 MM 月')}</div>
            <div className="calendar-header-icon" onClick={nextMonthHandler}>&gt;</div>
            <button className="calendar-header-btn" onClick={todayHandler}>今天</button>
        </div>
    </div>
}

export default Header;
```

在 Calendar 里传入 todayHandler：

![[react/react 通关秘籍/media/2f7449599f4755ed1d1299385217f8a5_MD5.png]]

```javascript
function todayHandler() {
    const date = dayjs(Date.now());

    setCurValue(date);
    setCurMonth(date);
    onChange?.(date);
}
```
*同时修改日期和当前月份，并且还要调用 onChange 回调*。

测试下：

![[react/react 通关秘籍/media/83c0c926be81e7a89f30372c28c112b1_MD5.gif]]

此外，*我们希望点击上下月份的日期的时候，能够跳转到那个月的日历：*

![[react/react 通关秘籍/media/7935df84045cd5215d995077726cfe79_MD5.gif]]

这个也简单，切换日期的时候顺便修改下 curMonth 就好了：

![[react/react 通关秘籍/media/8274ffe899c249f36689d057f771ece7_MD5.png]]

测试下：

![[react/react 通关秘籍/media/e38d0a47762d42e6c8b9fe2341ac981c_MD5.gif]]

最后，还要加上 Header 的国际化：

![[react/react 通关秘籍/media/7c6d13bdd1e26bbcb7c3468fa0c6f8ac_MD5.png]]

就是把写死的文案，改成丛资源包取值的方式就好了。

```javascript
function Header(props: HeaderProps) {

    const {
        curMonth,
        prevMonthHandler,
        nextMonthHandler,
        todayHandler
    } = props;

    const localeContext = useContext(LocaleContext);
    const CalendarContext = allLocales[localeContext.locale];

    return <div className="calendar-header">
        <div className="calendar-header-left">
            <div className="calendar-header-icon" onClick={prevMonthHandler}>&lt;</div>
            <div className="calendar-header-value">{curMonth.format(CalendarContext.formatMonth)}</div>
            <div className="calendar-header-icon" onClick={nextMonthHandler}>&gt;</div>
            <button className="calendar-header-btn" onClick={todayHandler}>{CalendarContext.today}</button>
        </div>
    </div>
}
```
试试看：

![[react/react 通关秘籍/media/553b3f439f860ab70a608780c448bfbc_MD5.png]]

![[react/react 通关秘籍/media/5f256db83853941678434f9333345155_MD5.png]]

![[react/react 通关秘籍/media/e0ec4add61bf3bcd1f9a3c7eed1d3990_MD5.png]]

没啥问题。

这样，我们的 Calendar 组件就完成了。

# 优化代码

最后我们再来优化下代码：

![[react/react 通关秘籍/media/19476eab9ecf4becf914ce3033d1d89e_MD5.png]]

重复逻辑可以抽离出个方法：

![[react/react 通关秘籍/media/96eafb9899b525ca98c451b82b6a2fed_MD5.png]]

```javascript
function changeDate(date: Dayjs) {
    setCurValue(date);
    setCurMonth(date);
    onChange?.(date);
}
```
渲染逻辑抽离出来的函数，放在组件外需要传很多参数，而这个函数只有这里用，可以移到组件内：

![[react/react 通关秘籍/media/e67860fc5831b422dd668570a1b0a818_MD5.png]]

这样就不用传那些参数了：

![[react/react 通关秘籍/media/27516b3b80e9fd3971ee65a6dc0c2933_MD5.png]]

案例代码上传了[小册仓库](https://github.com/QuarkGluonPlasma/react-course-code/tree/main/calendar-component)。

# 总结

上节我们实现了布局，这节加上了参数并且实现了这些参数对应的逻辑。

className 和 style 用于修改外层容器的样式，内部用的 flex 布局，只要容器大小变了，内容会自动适应。

dateRender 和 dateInnerConent 是用于修改日期单元格的内容的，比如显示节日、日程安排等。

locale 是切换语言，国际化就是把写死的文案换成从资源包取值的方式，我们创建了 zh-CN 和 en-US 两个资源包，并且可以通过 locale 参数来切换。

通过 createContext 创建 context 对象来保存 locale 配置，然后通过 Provider 修改其中的值，这样子组件里就通过 useContext 把它取出来就知道当前语言了。

日历组件是一个常用组件，而且是经常需要定制的那种，因为各种场景下对它有不同的要求，所以能够自己实现各种日历组件是一个必备技能。
