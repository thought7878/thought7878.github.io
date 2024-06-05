## 4 种类型的缓存

[docs](https://nextjs.org/docs/app/building-your-application/caching#overview)

## 3 种清除缓存的方法

### revalidatePath / revalidateTag

### revalidate / dynamic 变量

[docs](https://nextjs.org/docs/app/building-your-application/caching#revalidatepath)

```javascript
// Opt out of caching for all data requests in the route segment
export const dynamic = "force-dynamic";
```

### fetch config option

```javascript
// Revalidate at most every hour
fetch("https://...", { next: { revalidate: 3600 } });
```

```javascript
// Opt out of caching for an individual `fetch` request
fetch(`https://...`, { cache: "no-store" });
```
