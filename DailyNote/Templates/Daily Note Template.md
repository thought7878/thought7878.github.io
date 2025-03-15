---
date: <% moment(tp.file.title,'YYYY-MM-DD').format('YYYY-MM-DD') %>
aliases: 
tags:
  - "#type/daily-note"
log-sleep-hours:
log-sleep-rating:
log-healthy-food-rating:
log-day-rating:
---


---
```calendar-nav
```

---


![[Templates/media/92a84d504b8818402d0815b1afb8a4ed_MD5.jpeg]]

## Morning Pages

> [! journal]- On This Day(去年的今天)...
> ```dataview
LIST 
FROM "Journal/01 Daily"
WHERE dateformat(file.day,"MM-dd") = dateformat(this.file.day,"MM-dd")


> [! calendar]- Notes Created This Day
> ```dataview
TABLE created, updated as modified, tags, summary
FROM "" AND !"Journal" AND !"Templates"
WHERE icontains(dateformat(file.ctime, "YYYY-MM-DD"), dateformat(this.file.day, "YYYY-MM-DD"))


> [! task]- Tasks
> ```dataview
> TASK
> WHERE !completed
> AND icontains(text, "<% moment(tp.file.title,'YYYY-MM-DD').format('YYYY-MM') %>-")
> AND icontains(text, "#task")
> GROUP BY file.name as fileanme
> SORT rows.file.ctime DESC
> ```

---

## 今日任务
- [ ] {{input("任务1")}}
- [ ] {{input("任务2")}}

---

## Evening Review
- [ ] #log/daily-review  
- [ ] #log/daily-food 

