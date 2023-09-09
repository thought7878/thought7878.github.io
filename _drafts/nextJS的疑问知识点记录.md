## next

user?.username

useRouter/router.back()/usePathname;pathname === "/profile/edit"

next/navigation 与 next/router 区别

redirect

- 两个 useRouter 的区别

```javascript
import { useRouter } from "next/router";
import { useRouter, usePathname } from "next/navigation";
```

- revalidatePath

Next.js 中的 revalidatePath 函数用于使特定路径的缓存失效。如果您需要强制页面重新加载最新数据，这会很有用。

[chatgpt4google](https://webapp.chatgpt4google.com/s/MTAwOTMx)

```javascript
revalidatePath(path);
```

## mongon db

- [$push](https://webapp.chatgpt4google.com/s/MTE1NDE0) / \$in

在 MongoDB 中， $push 运算符用于将元素添加到文档中的数组字段。它通常用于更新操作，将新值附加到现有数组而不影响数组中的其他元素。

```javascript
await User.findByIdAndUpdate(author, {
  $push: { threads: createdThread._id },
});

//$in
Thread.find({ parentId: { $in: [null, undefined] } });
```

- User.findOne

- id

```
_id
```

- populate

Mongoose populate(填充) 是一种允许您引用 MongoDB 中其他集合中的文档的方法。它的工作原理是用其他集合中引用的文档替换原始文档中的指定路径

https://webapp.chatgpt4google.com/s/Mjk2NDQz

```

```
