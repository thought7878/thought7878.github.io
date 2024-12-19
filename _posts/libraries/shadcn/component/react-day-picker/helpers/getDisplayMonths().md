```tsx
import type { DateLib } from "../classes/DateLib.js";
import type { DayPickerProps } from "../types/index.js";

/**
 * 根据给定的起始月份、结束月份和月份数量，返回一个包含所有要显示的月份的数组。
 *
 * @param {Date} firstDisplayedMonth - 第一个要显示的月份。
 * @param {Date | undefined} calendarEndMonth - 日历的结束月份，如果未指定则为 undefined。
 * @param {Pick<DayPickerProps, "numberOfMonths">} props - 包含要显示的月份数量的对象。
 * @param {DateLib} dateLib - 日期库对象，提供日期操作方法。
 * @returns {Date[]} - 包含所有要显示的月份的数组。
 */
export function getDisplayMonths(
  firstDisplayedMonth: Date,
  calendarEndMonth: Date | undefined,
  props: Pick<DayPickerProps, "numberOfMonths">,
  dateLib: DateLib
) {
  // 从props中解构出numberOfMonths，如果不存在则默认为1
  const { numberOfMonths = 1 } = props;
  // 创建一个空数组，用于存储要显示的月份
  const months: Date[] = [];
  // 循环numberOfMonths次，每次增加一个月
  for (let i = 0; i < numberOfMonths; i++) {
    // 使用dateLib.addMonths方法将firstDisplayedMonth增加i个月
    const month = dateLib.addMonths(firstDisplayedMonth, i);
    // 如果calendarEndMonth存在且month大于calendarEndMonth，则跳出循环
    if (calendarEndMonth && month > calendarEndMonth) {
      break;
    }
    // 将month添加到months数组中
    months.push(month);
  }
  // 返回包含所有要显示的月份的数组
  return months;
}
```
