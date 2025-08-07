---
created: <% tp.file.creation_date() %>
updated: <% tp.file.last_modified_date() %>
aliases: 
tags:
  - "#daily-note"
log-sleep-hours: 
log-sleep-rating: 
log-healthy-food-rating: 
log-day-rating: 
---
# 习惯
- [睡眠开始时间:: ] [睡眠结束时间:: ] [睡眠分数:: ] [睡眠原因:: ] 
- [运动类型:: 骑动感单车] [运动时间:: ]
- [睡前冥想:: true]

- [英语听力时长:: m]
- [英语单词数量:: 0]

- [练习舞蹈:: ]

# 任务
<%*
const today = tp.date.now("YYYY-MM-DD", 0, tp.file.title, "YYYY-MM-DD")
-%>


## 计划的
- [ ] 
- [ ] 

## 已完成的
```dataviewjs
const dt = dv.luxon.DateTime;
const today = dt.fromISO("<% today %>");

dv.taskList(
    dv.pages().file.tasks
        .where(t => {
            const hasTaskDateField = t.completion;
            if(!hasTaskDateField) return false;
            
            const isTodayCompleted = t.completion.hasSame(today,"day");
            
            return t.completed && isTodayCompleted;
        })
)
```


## 未完成的

```dataviewjs
const today = new Date("<% today %>"); // 从 Templater 获取当前日期

// 查询逻辑
dv.taskList(
    dv.pages().file.tasks
        .where(t => {
	        // 过滤没有inline field元数据的任务（支持 due:: 或 scheduled::）
            const hasTaskDateField = t.start;
            //t.created || t.start || t.completion || t.due || t.scheduled;
            if(!hasTaskDateField) return false;
            
            //return isIncomplete && taskDateField.toDateString() === today.toDateString();
            return !t.completed || !t.completion;
        })
)
```

## 全部的
```dataviewjs
const today = new Date("<% today %>"); // 从 Templater 获取当前日期

// 查询逻辑
dv.taskList(
    dv.pages().file.tasks
        .where(t => {
	        // 过滤没有inline field元数据的任务（支持 due:: 或 scheduled::）
            const hasTaskDateField = t.start;
            //t.created || t.start || t.completion || t.due || t.scheduled;
            if(!hasTaskDateField) return false;
            
            //return isIncomplete && taskDateField.toDateString() === today.toDateString();
            return true;
        })
)
```

# 今日新建的笔记
```dataview
Table file.ctime as "创建日期"
Where date(today) - file.ctime <= dur(0 day)
Sort file.ctime desc
```

```dataview
TABLE file.ctime AS "创建时间"
FROM ""
WHERE date(file.ctime) = date(now)
SORT file.ctime DESC
```

```dataview
table file.name as "笔记名", dateformat(file.ctime, "yyyy-MM-dd HH:mm:ss") as "Created Time"
from ""
where file.ctime != null and typeof file.ctime == "object" and dateformat(file.ctime, "yyyy-MM-dd") = date("<% today %>").toISODate()
```

# 英语
## 单词

# 闪念



# 复盘


# 番茄时间
