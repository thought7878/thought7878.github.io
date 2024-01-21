



## Responsive Design

### sm: & max-sm:

```html

<!-- 大于等于md 且 小于xl的时候，显示flex -->
<!-- md:大于等于md；max-xl:小于xl -->
<div class="md:max-xl:flex">
  
</div>
```

```html
<!-- 默认(除了md/lg的其他情况，即小于md的情况)，w-16 -->
<!-- 大于等于md且小于lg，w-32 -->
<!-- 大于等于lg，w-48 -->
<!-- Width of 16 by default, 32 on medium screens, and 48 on large screens -->
<img class="w-16 md:w-32 lg:w-48" src="...">
```


