---
date: <% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(0,'days').format('YYYY-MM-DD')  %>
aliases: 
tags:
  - "#type/weekly-note"
---


---
```calendar-nav
```
---

<%

let start_of_week = moment(tp.file.title, 'YYYY-[W]WW').startof('week');
let days_in_week = 7;
tR += `> [! picture]- Pictures\n`;
tR += Array(days_in_week).fill(null).map((x,i) => `> ![[${moment(start_of_week).add(i,'d').format('YYYY-MM-DD[]#YYYY-MM-DD')}]]`).join("\n") + '\n';

%>

> [! highlight]- Highlights!
>```dataview
TASK
FROM ""
WHERE icontains(text, "#log/highlight")
AND (date<= (date(this.date) + dur(6 days))) and (date >= (date(this.date)))
GROUP BY file.name as filename





>[! Calendar]- Daily Reviews
>```dataview
TASK
WHERE icontains(text, "#log/day-review")
AND (date <= (date(this.date) + dur(6 days))) and (date >= (date(this.date)))
GROUP BY file.name as filename
SORT filename ASC

---
- [ ] #log/weekly-review

---

## 分析


```tracker
searchType: frontmatter
searchTarget: log-day-rating, log-sleep-rating,log-sleep-hours,log-healthy-eating
datasetName:Day-Rating, Sleep-Rating, Sleep-Hours, Healthy-eating
startDate: <% moment(tp.file.title,'YYYY-[W]ww').startOf('month').add(0,'days').format("YYYY-MM-DD") %>
endDate: <% moment(tp.file.title,'YYYY-[W]ww').startOf('month').add(1,'months').add(-1,'days').format("YYYY-MM-DD") %>
month:
  startWeekOn:'Sun'
  threshold:0.99, 0.99, 7.51, 0.99
  color: green
  initMonth: <% moment(tp.file.title,'YYYY-[W]ww').format('YYYY-MM') %>
  circleColorByValue: true
  todayRingColor: white
  selectedRingColor: white
```

### 平均值

```tracker
searchType: frontmatter
searchTarget: log-day-rating, log-sleep-rating, log-sleep-hours,log-healthy-eating
datasetName: log-day-rating, log-sleep-rating, log-sleep-hours, log-healthy-eating
startDate: <% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(0,'days').format("YYYY-MM-DD") %>
endDate: <% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(6,'days').format("YYYY-MM-DD")
  summary:
    template:
      " - Average Day Rating:  {{average()}}
      \n - Average sleep rating:  {{average(dataset(1))}}
      \n - Average sleep hours:  {{average(dataset(2))}}
      \n - Average Healthy Food Rating:  {{average(dataset(3))}}"
  ```
      






