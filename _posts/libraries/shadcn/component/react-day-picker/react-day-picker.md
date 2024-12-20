"react-day-picker": "8.10.1",


# 工程结构概述

## 1. 根目录

- **`.editorconfig`**: 编辑器配置文件，确保不同编辑器和IDE之间的代码风格一致。
- **`.eslintrc.cjs`**: ESLint配置文件，用于代码静态检查。
- **`.git/`**: Git版本控制目录。
- **`.github/`**: GitHub相关配置和工作流程文件。
- **`.gitignore`**: 指定Git忽略的文件和目录。
- **`.npmrc`**: npm配置文件。
- **`.nvmrc`**: Node.js版本管理器配置文件。
- **`.prettierrc`**: Prettier配置文件，用于代码格式化。
- **`.vscode/`**: Visual Studio Code配置目录。
- **`CHANGELOG.md`**: 项目变更日志。
- **`CONTRIBUTING.md`**: 项目贡献指南。
- **`LICENSE`**: 项目许可证。
- **`README.md`**: 项目说明文档。
- **`SECURITY.md`**: 项目安全指南。

## 2. `examples/` 目录

- **`AccessibleDatePicker.tsx`**, **`AutoFocus.tsx`**, **`BroadcastCalendar.tsx`**, ...: 各种示例组件，展示了日历组件的不同用法。

## 3. `examples-app/` 目录

- **`tsconfig.node.json`**: TypeScript配置文件，用于Node.js环境。
- **`package.json`**: 示例应用的配置文件，定义了依赖和脚本。

## 4. `jalali.d.ts` 和 `jalali.js`

- **`jalali.d.ts`**: 定义了Jalali日期库的类型声明。
- **`jalali.js`**: 可能是Jalali日期库的实现。

## 5. `jest.config.ts`

- **`jest.config.ts`**: Jest测试框架的配置文件。

## 6. `locale.d.ts` 和 `locale.js`

- **`locale.d.ts`**: 定义了本地化相关的类型声明。
- **`locale.js`**: 可能是本地化相关的实现。

## 7. `package.json`

- **`package.json`**: 项目的配置文件，定义了项目的依赖、脚本等信息。

## 8. `performance-tests/` 目录

- 可能包含性能测试相关的文件和配置。

## 9. `pnpm-workspace.yaml`

- **`pnpm-workspace.yaml`**: pnpm工作区配置文件。

## 10. `react-day-picker.code-workspace`

- **`react-day-picker.code-workspace`**: Visual Studio Code工作区配置文件。

## 11. `scripts/` 目录

- 可能包含项目的脚本文件，用于构建、测试等操作。

## 12. `src/` 目录

### `src/classes` 目录

`src/classes` 目录在当前工程中扮演着重要的角色，它主要负责定义和实现与日历组件相关的核心类。*这些类提供了对日历中日期、月份、周等概念的抽象和操作方法，是构建日历组件的基础（数据结构）*。

#### 详细功能

##### 1. `CalendarDay` 类

- **功能**：表示日历中的一个日期。
- **文件**：`CalendarDay.ts`
- **作用**：
    - 存储日期对象和显示月份。
    - 判断日期是否属于显示月份。
    - 提供方法检查两个 `CalendarDay` 对象是否相等。

##### 2. `CalendarMonth` 类

- **功能**：表示日历中的一个月份。
- **文件**：`CalendarMonth.ts`
- **作用**：
    - 存储月份的日期信息。
    - 提供方法获取月份的天数、第一天的星期几等。

##### 3. `CalendarWeek` 类

- **功能**：表示日历中的一个周。
- **文件**：`CalendarWeek.ts`
- **作用**：
    - 存储周的日期信息。
    - 提供方法获取周的天数、周数等。

##### 4. `DateLib` 类

- **功能**：提供日期操作的工具函数。
- **文件**：`DateLib.ts`
- **作用**：
    - 封装了一些常用的日期操作方法，如比较日期、格式化日期等。

### `src/components` 目录

`src/components` 目录在当前工程中扮演着核心的角色，它主要负责定义和实现日历组件的各个部分。这些组件共同构成了完整的日历功能，包括日期选择、导航、显示等。

#### 详细功能

##### 1. 按钮组件

- **`Button.tsx`**: 通用按钮组件，用于各种交互操作。

##### 2. 标题标签组件

- **`CaptionLabel.tsx`**: 用于显示日历标题，如月份和年份。

##### 3. 箭头组件

- **`Chevron.tsx`**: 用于导航的箭头图标，通常用于切换月份或年份。

##### 4. 日期组件

- **`Day.tsx`**: 表示日历中的一个日期单元格，用于显示和选择日期。
- **`DayButton.tsx`**: 日期按钮组件，用于点击选择日期。

##### 5. 下拉菜单组件

- **`Dropdown.tsx`**: 通用下拉菜单组件，用于选择月份或年份。
- **`DropdownNav.tsx`**: 带有导航功能的下拉菜单组件。

##### 6. 页脚组件

- **`Footer.tsx`**: 日历的页脚部分，可能包含一些额外的信息或操作按钮。

##### 7. 月份组件

- **`Month.tsx`**: 表示日历中的一个月份，包含多个日期单元格。
- **`MonthCaption.tsx`**: 月份标题组件，显示月份名称。
- **`MonthGrid.tsx`**: 月份网格组件，用于布局日期单元格。

##### 8. 月份选择组件

- **`Months.tsx`**: 用于选择多个月份的组件。
- **`MonthsDropdown.tsx`**: 用于选择月份的下拉菜单组件。

##### 9. 导航组件

- **`Nav.tsx`**: 日历的导航栏，包含箭头和标题等元素。
- **`NextMonthButton.tsx`**: 用于切换到下一个月的按钮。
- **`PreviousMonthButton.tsx`**: 用于切换到上一个月的按钮。

##### 10. 根组件

- **`Root.tsx`**: 日历的根组件，包含所有其他组件，构成完整的日历界面。

##### 11. 选择组件

- **`Select.tsx`**: 通用选择组件，用于选择单个或多个选项。

##### 12. 周组件

- **`Week.tsx`**: 表示日历中的一个周，包含多个日期单元格。
- **`WeekNumber.tsx`**: 显示周数的组件。
- **`WeekNumberHeader.tsx`**: 周数标题组件。

##### 13. 工作日组件

- **`Weekday.tsx`**: 表示日历中的一个工作日，用于显示星期几。
- **`Weekdays.tsx`**: 包含所有工作日的组件。

##### 14. 周选择组件

- **`Weeks.tsx`**: 用于选择多个周的组件。

##### 15. 年份选择组件

- **`YearsDropdown.tsx`**: 用于选择年份的下拉菜单组件。

##### 16. 自定义组件

- **`custom-components.tsx`**: 包含自定义的日历组件，用于扩展或定制日历功能。



### `src/formatters` 目录

`src/formatters` 目录的作用是*提供一组用于格式化日期和其他相关信息的函数*。这些函数*可以根据用户的需求自定义日期的显示格式*，从而增强日历组件的灵活性和可定制性。

#### 功能概述

1. **日期格式化**：提供了多个函数，用于格式化不同的日期部分，如月份、日期、星期几等。
2. **自定义格式**：允许用户通过传递自定义格式字符串来定制日期的显示方式。
3. **国际化支持**：支持不同的语言和地区，通过使用适当的格式化函数来显示本地化的日期信息。

#### 详细功能

##### 1. `formatCaption`

- **功能**：格式化月份标题。
- **文件**：`formatCaption.ts`

##### 2. `formatDay`

- **功能**：格式化日期显示在日历单元格中。
- **文件**：`formatDay.ts`

##### 3. `formatMonthDropdown`

- **功能**：格式化月份下拉菜单中的选项。
- **文件**：`formatMonthDropdown.ts`

##### 4. `formatWeekNumber`

- **功能**：格式化周数。
- **文件**：`formatWeekNumber.ts`

##### 5. `formatWeekNumberHeader`

- **功能**：格式化周数标题。
- **文件**：`formatWeekNumberHeader.ts`

##### 6. `formatWeekdayName`

- **功能**：格式化星期几的名称。
- **文件**：`formatWeekdayName.ts`

##### 7. `formatYearDropdown`

- **功能**：格式化年份下拉菜单中的选项。
- **文件**：`formatYearDropdown.ts`


### `src/helpers` 目录

`src/helpers` 目录在当前工程中扮演着重要的角色，它主要负责提供各种辅助函数和工具，以支持日历组件的核心功能。这些函数和工具涵盖了日期计算、格式化、比较、样式处理等多个方面，为日历组件的开发和使用提供了便利。

#### 详细功能

##### 1. 日期计算和处理

- **`calculateFocusTarget.ts`**: 计算焦点目标日期。
- **`getBroadcastWeeksInMonth.ts`**: 获取广播日历中的周数。
- **`getDays.ts`**: 获取日历中的所有日期。
- **`getDisplayMonths.ts`**: 获取显示的月份。
- **`getInitialMonth.ts`**: 获取初始月份。
- **`getWeeks.ts`**: 获取日历中的所有周。

##### 2. 日期格式化

- **`getFormatters.ts`**: 获取日期格式化函数。

##### 3. 样式处理

- **`getClassNamesForModifiers.ts`**: 根据修饰符获取类名。
- **`getStyleForModifiers.ts`**: 根据修饰符获取样式。

##### 4. 数据属性处理

- **`getDataAttributes.tsx`**: 获取数据属性。

##### 5. 测试辅助

- **`broadcastCalendar.test.ts`**: 广播日历测试。
- **`endOfBroadcastWeek.test.ts`**: 广播日历结束周测试。
- **`getBroadcastWeeksInMonth.test.ts`**: 广播日历周数测试。
- **`getClassNamesForModifiers.test.ts`**: 类名修饰符测试。
- **`getDataAttributes.test.tsx`**: 数据属性测试。
- **`getDays.test.ts`**: 日期测试。
- **`getDisplayMonths.test.ts`**: 显示月份测试。
- **`getFormatters.test.ts`**: 格式化函数测试。
- **`getInitialMonth.test.ts`**: 初始月份测试。
- **`getWeeks.test.ts`**: 周测试。

### `src/labels` 目录

`src/labels` 目录在当前工程中的作用是*提供与日历组件相关的标签和文本内容的管理和配置*。这些标签和文本内容通常用于显示在日历的不同部分，例如按钮、标题、下拉菜单选项等，*以提供多语言支持和自定义文本内容的能力*。

#### 详细功能

##### 1. 标签和文本内容管理

- **`index.ts`**: 作为入口文件，导出所有标签和文本内容的配置。
- **`labelDayButton.ts`**: 定义与日期按钮相关的标签和文本内容。
- **`labelGrid.ts`**: 定义与日历网格相关的标签和文本内容。
- **`labelGridcell.ts`**: 定义与日历网格单元格相关的标签和文本内容。
- **`labelMonthDropdown.ts`**: 定义与月份下拉菜单相关的标签和文本内容。
- **`labelNav.ts`**: 定义与导航相关的标签和文本内容。
- **`labelNext.ts`**: 定义与“下一个”按钮相关的标签和文本内容。
- **`labelPrevious.ts`**: 定义与“上一个”按钮相关的标签和文本内容。
- **`labelWeekNumber.ts`**: 定义与周数相关的标签和文本内容。
- **`labelWeekNumberHeader.ts`**: 定义与周数标题相关的标签和文本内容。
- **`labelWeekday.ts`**: 定义与工作日相关的标签和文本内容。
- **`labelYearDropdown.ts`**: 定义与年份下拉菜单相关的标签和文本内容。

##### 2. 测试文件

- **`labelDayButton.test.ts`**: 对日期按钮标签的测试。
- **`labelGrid.test.ts`**: 对日历网格标签的测试。
- **`labelGridcell.test.ts`**: 对日历网格单元格标签的测试。
- **`labelMonthDropdown.test.ts`**: 对月份下拉菜单标签的测试。
- **`labelNav.test.ts`**: 对导航标签的测试。
- **`labelNext.test.ts`**: 对“下一个”按钮标签的测试。
- **`labelPrevious.test.ts`**: 对“上一个”按钮标签的测试。
- **`labelWeekNumber.test.ts`**: 对周数标签的测试。
- **`labelWeekNumberHeader.test.ts`**: 对周数标题标签的测试。
- **`labelWeekday.test.ts`**: 对工作日标签的测试。
- **`labelYearDropdown.test.ts`**: 对年份下拉菜单标签的测试。


### `src/selection` 目录

`src/selection` 目录在当前工程中扮演着重要的角色，它主要负责*处理日历组件中的日期选择逻辑*。这个目录中的文件*实现了不同的选择模式，包括单选、多选和范围选择，以及提供了一个统一的接口来管理这些选择模式*。

#### 详细功能

##### 1. 选择模式管理

- **`useSelection.ts`**: 根据传入的 `DayPickerProps` 和 `DateLib`，返回对应的选择模式（单选、多选或范围选择）。
- **`useSingle.tsx`**: 实现单选模式的逻辑。
- **`useMulti.tsx`**: 实现多选模式的逻辑。
- **`useRange.tsx`**: 实现范围选择模式的逻辑。

##### 2. 测试文件

- **`useSingle.test.tsx`**: 对单选模式的测试。
- **`useMulti.test.tsx`**: 对多选模式的测试。
- **`useRange.test.tsx`**: 对范围选择模式的测试。

### `src/types` 目录

`src/types` 目录在当前工程中的作用是*定义和管理与日历组件相关的类型和接口*。这些类型和接口用于确保组件的属性、状态和方法的一致性和正确性，以及提供代码的类型安全性和可读性。

#### 详细功能

##### 1. 类型定义

- **`index.ts`**: 作为入口文件，导出所有类型和接口。
- **`props.ts`**: 定义与日历组件属性相关的类型和接口，如 `DayPickerProps`、`PropsMultiRequired` 等。
- **`shared.ts`**: 定义一些共享的类型和接口，如 `DayClickEventHandler`、`DayModifiers` 等。
- **`selection.ts`**: 定义与日期选择相关的类型和接口，如 `SelectionMode`、`SelectedDays` 等。

##### 2. 测试文件

- **`props.test.tsx`**: 对属性类型的测试。

### `src/utils` 目录

`src/utils` 目录在当前工程中的作用是*提供各种实用工具函数和辅助函数*，这些函数被用于*支持日历组件的各种功能，例如日期处理、范围检查、类型守卫等*。

#### 详细功能

##### 1. 日期范围操作

- **`addToRange.ts`**: 提供了向日期范围添加日期的功能。
- **`rangeContainsDayOfWeek.ts`**: 检查日期范围是否包含指定的星期几。
- **`rangeContainsModifiers.ts`**: 检查日期范围是否包含指定的修饰符。
- **`rangeIncludesDate.ts`**: 检查日期范围是否包含指定的日期。
- **`rangeOverlaps.ts`**: 检查两个日期范围是否重叠。

##### 2. 日期匹配修饰符

- **`dateMatchModifiers.ts`**: 提供了根据修饰符匹配日期的功能。

##### 3. 类型守卫

- **`typeguards.ts`**: 包含了一系列类型守卫函数，用于检查值是否属于特定的类型。

##### 4. 测试文件

- **`addToRange.test.ts`**: 对 `addToRange` 函数的测试。
- **`dateMatchModifiers.test.ts`**: 对 `dateMatchModifiers` 函数的测试。
- **`rangeContainsDayOfWeek.test.ts`**: 对 `rangeContainsDayOfWeek` 函数的测试。
- **`rangeContainsModifiers.test.ts`**: 对 `rangeContainsModifiers` 函数的测试。
- **`rangeIncludesDate.test.ts`**: 对 `rangeIncludesDate` 函数的测试。
- **`rangeOverlaps.test.ts`**: 对 `rangeOverlaps` 函数的测试。
- **`typeguards.test.ts`**: 对类型守卫函数的测试。

## 13. `test/` 目录

- **`jest-custom-matchers.d.ts`**: 定义了自定义的Jest匹配器。
- **`elements.ts`**: 定义了测试中使用的一些辅助函数。

## 14. `tsconfig-base.json`, `tsconfig-cjs.json`, `tsconfig-docs.json`, `tsconfig-esm.json`, `tsconfig.json`

- 多个TypeScript配置文件，用于不同的编译目标和场景。

## 15. `website/` 目录

- **`versioned_docs/version-8.10.1/development/contributing.mdx`**, **`versioned_docs/version-8.10.1/development/architecture.mdx`**: 项目的贡献指南和架构文档。
- **`versioned_sidebars/version-8.10.1-sidebars.json`**: 文档网站的侧边栏配置。
- 可能还包含其他文档和网站相关的文件。

## 总结

这个工程主要是一个日历组件库，提供了丰富的日期处理功能和灵活的配置选项。通过不同的目录和文件，实现了日期计算、格式化、选择逻辑等核心功能，并提供了详细的文档和示例代码，方便用户使用和贡献。



这个 TypeScript 文件  `DayPicker.tsx`  是一个 React 组件的实现，用于渲染日期选择器日历。它依赖于多个外部文件和目录，每个文件和目录的作用如下：

- **DayPicker.tsx**：这是  `DayPicker`  组件的**主文件**，定义了组件的结构和逻辑。
- **UI.ts**：提供了一些用于渲染的常量和样式类名。

- **useCalendar.ts**：提供了一个自定义 Hook，用于管理日历的状态。
- **useDayPicker.ts**：提供了一个上下文（Context），用于在组件树中传递日期选择器的配置。
- **useFocus.ts**：提供了一个自定义 Hook，用于管理焦点状态。
- **useGetModifiers.ts**：提供了一个自定义 Hook，用于获取日期单元格的修饰符。
- **useSelection.ts**：提供了一个自定义 Hook，用于管理日期选择状态。

- **classes/**
  - **CalendarDay.ts**：定义了  `CalendarDay`  类，用于表示日期信息。
  - **DateLib.ts**：提供了一个日期库，用于处理日期相关的操作。
- **helpers/**：包含多个辅助函数，用于获取类名、组件、数据属性、默认类名、格式化函数、月份选项、样式、星期几、年份选项等。
- **labels/**：包含默认的标签文本。
- **types/**：定义了各种类型，用于类型检查和类型安全。
- **utils/**：包含一些实用函数，用于判断日期范围等。


通过这些文件和目录的协作，`DayPicker`  组件能够实现复杂的日期选择功能，并提供良好的用户体验。

# DayPicker.tsx 代码功能解释

这段 TypeScript 代码定义了一个名为  `DayPicker`  的 React 组件，用于渲染日期选择器日历。主要功能包括：

1. **初始化配置**：通过  `useMemo`  计算并缓存组件所需的配置，如日期库、格式化函数、标签、类名等。
2. **状态管理**：使用  `useCalendar`、`useGetModifiers`、`useSelection`  和  `useFocus`  等自定义 Hook 管理日历的状态和行为。使用  `useSelection`  和  `useFocus`  钩子管理选中的日期和焦点状态。
3. **事件处理**：定义了各种事件处理函数，如点击、聚焦、模糊、键盘操作等，用于处理用户与日期选择器的交互。
4. **渲染日历**：根据配置和状态，渲染日历的各个部分，包括月份导航、周日历、日期单元格等。使用  `useCalendar`  钩子生成日历数据，包括月份、周数、天数等。

## 控制流图

```mermaid
flowchart TD
	A[初始化配置] --> B[计算日历数据]
	B --> C[获取修饰符]
	C --> D[管理选择状态]
	D --> E[管理焦点状态]
	E --> F[定义事件处理器]
	F --> G[渲染日历]
	G --> H[渲染月份导航]
	H --> I[渲染月份标题]
	I --> J[渲染周日历]
	J --> K[渲染日期单元格]
	K --> L[处理用户交互]
	L --> M[更新状态]
	M --> N[重新渲染]
	N --> G
```

### 控制流图说明

1. **初始化配置**：计算并缓存组件所需的配置。
2. **计算日历数据**：生成日历的月份、周和日期数据。
3. **获取修饰符**：为每个日期单元格计算修饰符，如是否选中、是否禁用等。
4. **管理选择状态**：处理日期的选择状态。
5. **管理焦点状态**：处理日期单元格的焦点状态。
6. **定义事件处理器**：定义各种事件处理器，如点击、聚焦、键盘操作等。
7. **渲染日历**：根据配置和状态，渲染整个日历。
8. **渲染月份导航**：渲染月份导航按钮。
9. **渲染月份标题**：渲染月份标题，支持下拉选择。
10. **渲染周日历**：渲染周日历，显示星期几。
11. **渲染日期单元格**：渲染每个日期单元格。
12. **处理用户交互**：处理用户的点击、聚焦、键盘操作等交互。
13. **更新状态**：根据用户交互更新状态。
14. **重新渲染**：状态更新后重新渲染日历。

---

### 初始化配置：

使用  `useMemo`  计算并返回组件所需的配置对象，包括日期库、格式化器、标签、类名等。

- **输入**：`props`  对象，包含组件的各种属性。

- **输出**：配置对象，包括  `components`、`formatters`、`labels`、`dateLib`、`locale`  和  `classNames`。

  - **components**: 包含自定义组件的对象，这些组件可以用于替换或扩展  `DayPicker`  中的默认组件。
  - **formatters**: 包含用于格式化日期和其他数据的函数对象。
  - **labels**: 包含用于显示文本标签的对象，例如月份名称、周几等。
  - **dateLib**: 一个  `DateLib`  实例，用于处理日期相关的逻辑，如日期计算、格式化等。
  - **locale**: 包含本地化配置的对象，用于支持多语言和地区的显示。
  - **classNames**: 包含类名的对象，用于自定义组件的样式。

- **逻辑**：
  - 合并默认的  `locale`  和  `props.locale`。
  - 创建  `dateLib`  实例，配置包括 locale 和一些日历相关的设置。
  - 获取组件、格式化器、标签和类名。

```tsx
const { components, formatters, labels, dateLib, locale, classNames } =
  useMemo(() => {
    const locale = { ...defaultLocale, ...props.locale };

    const dateLib = new DateLib(
      {
        locale,
        weekStartsOn: props.broadcastCalendar ? 1 : props.weekStartsOn,
        firstWeekContainsDate: props.firstWeekContainsDate,
        useAdditionalWeekYearTokens: props.useAdditionalWeekYearTokens,
        useAdditionalDayOfYearTokens: props.useAdditionalDayOfYearTokens,
      },
      props.dateLib
    );

    return {
      dateLib,
      components: getComponents(props.components),
      formatters: getFormatters(props.formatters),
      labels: { ...defaultLabels, ...props.labels },
      locale,
      classNames: { ...getDefaultClassNames(), ...props.classNames },
    };
  }, [
    props.classNames,
    props.components,
    props.dateLib,
    props.firstWeekContainsDate,
    props.formatters,
    props.labels,
    props.locale,
    props.useAdditionalDayOfYearTokens,
    props.useAdditionalWeekYearTokens,
    props.weekStartsOn,
    props.broadcastCalendar,
  ]);
```

### 解构赋值：

- 从  `props`  中解构出多个属性，包括  `captionLayout`、`mode`、`onDayBlur`、`onDayClick`  等。
- 从  `formatters`  对象中解构出多个格式化函数，如  `formatCaption`、`formatDay`  等。

```tsx
const {
  captionLayout,
  mode,
  onDayBlur,
  onDayClick,
  onDayFocus,
  onDayKeyDown,
  onDayMouseEnter,
  onDayMouseLeave,
  onNextClick,
  onPrevClick,
  showWeekNumber,
  styles,
} = props;

/** 解构赋值从formatters对象中提取多个格式化函数 */
const {
  formatCaption, // 用于格式化标题的函数
  formatDay, // 用于格式化日期的函数
  formatMonthDropdown, // 用于格式化月度下拉菜单的函数
  formatWeekNumber, // 用于格式化周数的函数
  formatWeekNumberHeader, // 用于格式化周数头部的函数
  formatWeekdayName, // 用于格式化星期名称的函数
  formatYearDropdown, // 用于格式化年度下拉菜单的函数
} = formatters;
```

### 生成日历数据：

使用  [[useCalendar()]]  钩子生成日历数据。

- **输入**：`props`  和  `dateLib`。
- **输出**：日历数据对象，包括  `days`、`months`、`navStart`、`navEnd`、`previousMonth`、`nextMonth`  和  `goToMonth`。

  - days： 日历中要显示的天的数组，CalendarDay数组，包括上、下个月的天，包括多个月的天
  - months：月份数组 CalendarMonth\[\]；渲染的数据
  - navStart： 导航起始点，用于确定日历显示的开始月份
  - navEnd： 导航结束点，用于确定日历显示的结束月份
  - previousMonth： 上一个月
  - nextMonth： 下一个月
  - goToMonth：函数，用于直接导航到指定的月份

- **逻辑**：
  - 根据  `props`  和  `dateLib`  生成日历数据。

```tsx
const calendar = useCalendar(props, dateLib);

const { days, months, navStart, navEnd, previousMonth, nextMonth, goToMonth } =
  calendar;
```

### 管理选中状态：

使用  `useSelection`  钩子，管理选中的日期状态。从 useSelection 钩子中获取所需函数和状态。useSelection 是一个自定义钩子，用于处理选择逻辑，它接受 props 和一个日期库作为参数

- **输入**：`props`  和  `dateLib`。
- **输出**：选中状态对象，包括  `isSelected`、`select`  和  `selectedValue`。
- **逻辑**：
  - 根据  `props`  和  `dateLib`  管理选中的日期。

```tsx
const {
  isSelected,
  select,
  selected: selectedValue,
} = useSelection(props, dateLib) ?? {};
```

### 管理焦点状态：

使用  `useFocus`  钩子，管理焦点状态。

- **输入**：`props`、`calendar`、`getModifiers`、`isSelected`  和  `dateLib`。
- **输出**：焦点状态对象，包括  `blur`、`focused`、`isFocusTarget`、`moveFocus`  和  `setFocused`。
- **逻辑**：
  - 根据  `props`  和其他状态管理焦点。

```tsx
const { blur, focused, isFocusTarget, moveFocus, setFocused } = useFocus(
  props,
  calendar,
  getModifiers,
  isSelected ?? (() => false),
  dateLib
);
```

### 定义事件处理函数：

定义了各种事件处理函数，用于处理用户与日期选择器的交互。如点击、聚焦、模糊、键盘操作等。

- **事件处理函数**：
  - `handlePreviousClick`：处理前一个月的点击事件。
  - `handleNextClick`：处理后一个月的点击事件。
  - `handleDayClick`：处理日期点击事件。
  - `handleDayFocus`：处理日期聚焦事件。
  - `handleDayBlur`：处理日期模糊事件。
  - `handleDayKeyDown`：处理日期键盘事件。
  - `handleDayMouseEnter`：处理日期鼠标进入事件。
  - `handleDayMouseLeave`：处理日期鼠标离开事件。
  - `handleMonthChange`：处理月份选择改变事件。
  - `handleYearChange`：处理年份选择改变事件。
- **逻辑**：
  - 每个事件处理函数根据相应的事件类型更新状态或调用回调函数。

### 渲染日历组件：

- 根据配置和状态，渲染日历的各个部分，包括导航、月份标题、周数、日期按钮等。
- 渲染的组件包括  `Root`、`Months`、`Nav`、`Month`、`MonthCaption`、`MonthGrid`、`Weekdays`、`Week`、`Day`  和  `Footer`。

- **渲染组件**：
	- `Root`：根容器。
	- `Months`：月份容器。
	- `Nav`：导航栏。
	- `Month`：单个月份。
	- `MonthCaption`：月份标题。
	- `MonthGrid`：月份网格。
	- `Weekdays`：星期标题。
	- `Week`：单周。
	- `Day`：单日。
	- `Footer`：底部内容。

- **逻辑**：
	- 根据  `props`  和状态，动态生成并渲染日历的各个部分。

	- 使用 `dayPickerContext.Provider` 来提供上下文数据。
	- 渲染日历的根组件 `components.Root`，并在其中渲染月份组件 `components.Months`。
	- 在月份组件中，渲染导航按钮 `components.Nav` 和每个月的日历 `components.Month`。
	- 在每个月的日历中，渲染月份标题 `components.MonthCaption` 和日期单元格 `components.Day`。


```mermaid
flowchart TD
    A[开始] --> B[初始化配置]
    B --> B1[合并默认locale]
    B1 --> B2[创建dateLib实例]
    B2 --> B3[获取components, formatters, labels, classNames]
    B3 --> C[生成日历数据]
    C --> C1[调用useCalendar]
    C1 --> C2[生成days, months, navStart, navEnd, previousMonth, nextMonth, goToMonth]
    C2 --> D[管理选中状态]
    D --> D1[调用useSelection]
    D1 --> D2[返回isSelected, select, selectedValue]
    D2 --> E[管理焦点状态]
    E --> E1[调用useFocus]
    E1 --> E2[返回blur, focused, isFocusTarget, moveFocus, setFocused]
    E2 --> F[定义事件处理函数]
    F --> F1[定义handlePreviousClick]
    F1 --> F2[定义handleNextClick]
    F2 --> F3[定义handleDayClick]
    F3 --> F4[定义handleDayFocus]
    F4 --> F5[定义handleDayBlur]
    F5 --> F6[定义handleDayKeyDown]
    F6 --> F7[定义handleDayMouseEnter]
    F7 --> F8[定义handleDayMouseLeave]
    F8 --> F9[定义handleMonthChange]
    F9 --> F10[定义handleYearChange]
    F10 --> G[渲染日历组件]
    G --> G1[渲染Root容器]
    G1 --> G2[渲染Months容器]
    G2 --> G3[渲染Nav导航栏]
    G3 --> G4[渲染Month单个月份]
    G4 --> G5[渲染MonthCaption月份标题]
    G5 --> G6[渲染MonthGrid月份网格]
    G6 --> G7[渲染Weekdays星期标题]
    G7 --> G8[渲染Week单周]
    G8 --> G9[渲染Day单日]
    G9 --> G10[渲染Footer底部内容]
    G10 --> H[结束]
```


