
# 输入
参考下面源码

# 输出
返回日历中要显示的所有月份，可以是多个月份。

CalendarMonth->CalendarWeek->CalendarDay
# 逻辑



```tsx
import type { DateLib } from "../classes/DateLib.js";
import { CalendarWeek, CalendarDay, CalendarMonth } from "../classes/index.js";
import type { DayPickerProps } from "../types/index.js";

/**
 * 返回日历中要显示的所有月份。
 *
 * @param {Date[]} displayMonths - 要显示的月份（以日期形式）。
 * @param {Date[]} dates - 日历中要显示的日期。
 * @param {Pick<DayPickerProps, "broadcastCalendar" | "fixedWeeks" | "ISOWeek" | "reverseMonths">} props - 来自props上下文的选项。
 * @param {DateLib} dateLib - 日期库对象，提供日期操作方法。
 * 
 * @returns {CalendarMonth[]} - 返回要显示的月份数组。
 */
export function getMonths(
  /** The months (as dates) to display in the calendar. */
  displayMonths: Date[],
  /** The dates to display in the calendar. */
  dates: Date[],
  /** Options from the props context. */
  props: Pick<
    DayPickerProps,
    "broadcastCalendar" | "fixedWeeks" | "ISOWeek" | "reverseMonths"
  >,
  dateLib: DateLib
): CalendarMonth[] {
  // 从dateLib中解构出多个日期操作方法
  const {
    addDays,
    endOfBroadcastWeek,
    endOfISOWeek,
    endOfMonth,
    endOfWeek,
    getISOWeek,
    getWeek,
    startOfBroadcastWeek,
    startOfISOWeek,
    startOfWeek,
  } = dateLib;
  // 使用reduce方法遍历displayMonths数组，为每个月创建一个CalendarMonth对象
  const dayPickerMonths = displayMonths.reduce<CalendarMonth[]>(
    (months, month) => {
      // 根据props中的配置，计算该月的第一周的第一天
      const firstDateOfFirstWeek = props.broadcastCalendar
        ? startOfBroadcastWeek(month, dateLib)
        : props.ISOWeek
        ? startOfISOWeek(month)
        : startOfWeek(month);

      // 根据props中的配置，计算该月的最后一周的最后一天
      const lastDateOfLastWeek = props.broadcastCalendar
        ? endOfBroadcastWeek(month, dateLib)
        : props.ISOWeek
        ? endOfISOWeek(endOfMonth(month))
        : endOfWeek(endOfMonth(month));

      /** The dates to display in the month. */
      // 过滤dates数组，只保留在该月范围内的日期
      const monthDates = dates.filter((date) => {
        return date >= firstDateOfFirstWeek && date <= lastDateOfLastWeek;
      });

      // 根据props中的配置，计算固定周数下该月的日期数量
      const nrOfDaysWithFixedWeeks = props.broadcastCalendar ? 35 : 42;

      // 如果启用了fixedWeeks选项，并且该月的日期数量少于固定周数，则添加额外的日期到该月的日期数组中
      if (props.fixedWeeks && monthDates.length < nrOfDaysWithFixedWeeks) {
        const extraDates = dates.filter((date) => {
          const daysToAdd = nrOfDaysWithFixedWeeks - monthDates.length;
          return (
            date > lastDateOfLastWeek &&
            date <= addDays(lastDateOfLastWeek, daysToAdd)
          );
        });
        monthDates.push(...extraDates);
      }

      // 使用reduce方法遍历该月的日期数组，为每个周创建一个CalendarWeek对象，并将日期添加到对应的周中
      const weeks: CalendarWeek[] = monthDates.reduce<CalendarWeek[]>(
        (weeks, date) => {
          const weekNumber = props.ISOWeek ? getISOWeek(date) : getWeek(date);
          const week = weeks.find((week) => week.weekNumber === weekNumber);

          const day = new CalendarDay(date, month, dateLib);
          if (!week) {
            weeks.push(new CalendarWeek(weekNumber, [day]));
          } else {
            week.days.push(day);
          }
          return weeks;
        },
        []
      );

      // 创建一个CalendarMonth对象，包含该月的所有周
      const dayPickerMonth = new CalendarMonth(month, weeks);
      // 将该月添加到结果数组中
      months.push(dayPickerMonth);
      return months;
    },
    []
  );

  // 如果启用了reverseMonths选项，则反转结果数组，否则直接返回结果数组
  if (!props.reverseMonths) {
    return dayPickerMonths;
  } else {
    return dayPickerMonths.reverse();
  }
}
```
