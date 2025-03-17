---
tags:
  - Blog
  - Obsidian
  - Dataview
done: false
f: true
date: 2025-03-20
d: 2025-03-12T18:28:00
array:
  - "1"
  - "2"
  - "3"
Inline arrays: ["a", "b", 1, 2]

obj:
  key1: "Val"
  key2: 3
  key3:
    - "List1"
    - "List2"
    - "List3"
---
```dataview
LIST tags
WHERE file = this.file
```

```dataviewjs
dv.list(dv.pages("").file.tags.distinct())
```

Basic Field:: Some random Value
**Bold Field**:: Nice!
带❤变量:: 我是表情+非拉丁字母
I would rate this a [rating:: 9]! It was [mood:: acceptable].

- [ ] Send an mail to David about the deadline [due:: 2022-04-05].

This will not show the (longKeyIDontNeedWhenReading:: key).

基础字段：\`= this.basic-field\`
加粗字段：\`= this.bold-field\`
在语句中：Rating: \`= this.rating\`, Mood: \`= this.mood\`
在任务列表中：\`= this.due\`
不显示变量名：\`= this.longKeyIDontNeedWhenReading\`
非常规变量：\`= this.带❤变量\`


```dataview
TABLE file.name AS 文件名, file.ctime AS 创建时间, file.mtime AS 修改时间, file.tags AS 标签
WHERE file = this.file
```
TABLE file.name AS 文件名, file.ctime AS 创建时间, file.mtime AS 修改时间, file.tags AS 标签
WHERE file = this.file

- [ ] 任务创建 [created:: 2024-04-25]
- [ ] 任务开始 [start:: 2024-04-26]
- [ ] 任务开始2 [start:: 2024-04-22]
- [x] 任务完成
	- [x] 子任务完成 1
	- [ ] 子任务未完成 1
	- [x] 子任务完成 2 [completion:: 2024-04-28]
- [x] 任务全部完成
	- [x] 完成 1
	- [x] 完成 2

```dataview
TASK
WHERE start >= date("2024-04-22")
```

```dataview
CALENDAR file.day
FROM "10 Example Data/dailys"
```


> [!danger] Bugs
> 有中到的阿斯顿发

> [!calendar]
> Contents

> [!important]
> Contents

> [!bug]
> Contents

> [!faq]
> Contents

> [!calendar]-
> Contents
> asdfasdf
> asdfasdf

> [!example]
> Contents


> [!example]+
> Contents



> [!asdf-]-
> Contents

> [!cloze]
> Contents

> [!casdfadf]-
> asdfasdf

