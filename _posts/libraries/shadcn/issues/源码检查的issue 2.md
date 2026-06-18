# shadcn-ui Bug 报告

> 项目路径: `/Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui`
> 扫描日期: 2026-06-18
> Next.js 版本: 16.2.7
> React 版本: 19.2.3
> Tailwind CSS: v4

---

## Bug 分类说明

| 级别                | 说明                |
| ----------------- | ----------------- |
| **Critical (严重)** | 导致功能失效、运行时错误或数据丢失 |
| **High (高)**      | 影响核心用户体验、可能导致性能退化 |
| **Medium (中)**    | 边界情况处理不当、潜在隐患     |
| **Low (低)**       | 代码规范、可维护性问题       |

---

## Critical — 严重问题

### BUG-001: TypeScript 构建错误被完全忽略

**文件:** [next.config.mjs](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/next.config.mjs#L7-L9)

```js
typescript: {
  ignoreBuildErrors: true,
}
```

**问题描述:** `ignoreBuildErrors: true` 会在构建时完全忽略所有 TypeScript 错误，导致类型错误被隐藏并可能引发运行时崩溃。

**影响范围:** 整个项目构建流程

**复现步骤:**
1. 引入任意 TypeScript 类型错误
2. 运行 `pnpm build`
3. 构建成功通过，但运行时可能崩溃

**修复建议:**
- 开发阶段可以暂时开启，但生产构建必须关闭
- 改为 `ignoreBuildErrors: process.env.NODE_ENV !== 'production'`

**严重程度:** Critical

---

### BUG-002: metadataBase 可能因环境变量缺失而崩溃

**文件:** [layout.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/app/layout.tsx#L22)

```ts
metadataBase: new URL(process.env.NEXT_PUBLIC_APP_URL!),
```

**问题描述:** 使用 `!` 非空断言强制忽略 `NEXT_PUBLIC_APP_URL` 可能为 `undefined` 的情况。如果环境变量未设置，`new URL(undefined!)` 会在构建/运行时抛出 TypeError。

**影响范围:** 所有页面的 Open Graph、Twitter Card、RSS 等元数据生成

**复现步骤:**
1. 不设置 `NEXT_PUBLIC_APP_URL` 环境变量
2. 启动开发服务器或构建
3. 访问任意页面时 metadata 生成崩溃

**修复建议:**
```ts
metadataBase: process.env.NEXT_PUBLIC_APP_URL
  ? new URL(process.env.NEXT_PUBLIC_APP_URL)
  : undefined,
```

**严重程度:** Critical

---

### BUG-003: OG 图片 URL 拼接缺少环境变量时崩溃

**文件:** [layout.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/app/layout.tsx#L41)

```ts
url: `${process.env.NEXT_PUBLIC_APP_URL}/opengraph-image.png`,
```

**问题描述:** 与 BUG-002 同样的问题，OG image URL 和 Twitter image URL 直接使用未校验的环境变量拼接，如果未设置会生成 `undefined/opengraph-image.png`。

**影响范围:** Open Graph 图片、Twitter Card 图片

**修复建议:** 使用 `siteConfig.url` 作为 fallback:
```ts
url: `${process.env.NEXT_PUBLIC_APP_URL || siteConfig.url}/opengraph-image.png`,
```

**严重程度:** Critical

---

### BUG-004: copyToClipboard 的 setTimeout 缺少清理机制导致内存泄漏

**文件:** [use-copy-to-clipboard.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/hooks/use-copy-to-clipboard.ts#L70-L74)

```ts
if (timeout !== 0) {
  setTimeout(() => {
    setIsCopied(false)
  }, timeout)
}
```

**问题描述:** `setTimeout` 的返回值没有被保存，组件卸载时不会清理。如果组件在 timeout 触发前卸载，会尝试在已卸载组件上调用 `setState`，导致 React 警告和潜在内存泄漏。

**影响范围:** 所有使用 `useCopyToClipboard` 的组件（CopyButton、BlockViewer 等）

**复现步骤:**
1. 快速点击复制按钮
2. 在 2 秒内卸载组件（如导航到其他页面）
3. React 发出 "Can't perform a React state update on an unmounted component" 警告

**修复建议:**
```ts
React.useEffect(() => {
  if (isCopied && timeout !== 0) {
    const timer = setTimeout(() => setIsCopied(false), timeout)
    return () => clearTimeout(timer)
  }
}, [isCopied, timeout])
```

**严重程度:** Critical

---

### BUG-005: legacyCopyToClipboard 使用已废弃的 document.execCommand

**文件:** [copy-button.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/copy-button.tsx#L25) / [use-copy-to-clipboard.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/hooks/use-copy-to-clipboard.ts#L20)

```ts
hasCopied = document.execCommand("copy")
```

**问题描述:** `document.execCommand("copy")` 已在现代浏览器中被标记为废弃，在部分新版浏览器（如 Chrome 127+、Firefox 120+）中已被完全移除，fallback 将永远失败。

**影响范围:** 不支持 `navigator.clipboard` 的旧浏览器和 HTTP 环境下的复制功能

**修复建议:**
- HTTP 环境下提示用户需要 HTTPS 才能使用复制功能
- 移除 `document.execCommand` 或使用更可靠的替代方案

**严重程度:** Critical

---

## High — 高级别问题

### BUG-006: useMobile Hook 返回 undefined 导致初始渲染异常

**文件:** [use-mobile.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/hooks/use-mobile.ts#L4-L16)

```ts
const [isMobile, setIsMobile] = React.useState<boolean | undefined>(undefined)
// ...
return !!isMobile
```

**问题描述:** 初始值为 `undefined`，在服务端渲染时 `!!undefined` 为 `false`，但在客户端 hydration 时 `useState(undefined)` 与服务端渲染结果不一致（如果实际窗口宽度小于 768px），可能导致 hydration mismatch。

**影响范围:** 所有使用 `useIsMobile` 的响应式组件

**复现步骤:**
1. 在移动端浏览器（宽度 < 768px）访问
2. 服务端渲染时 isMobile = false
3. 客户端 hydration 后 isMobile = true
4. 可能出现 UI 闪烁或 hydration mismatch 警告

**修复建议:**
```ts
const [isMobile, setIsMobile] = React.useState(false)
```
保持 SSR 和客户端初始状态一致。

**严重程度:** High

---

### BUG-007: CommandMenu 键盘事件监听器未清理 timeout 引用

**文件:** [command-menu.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/command-menu.tsx#L69-L127)

```ts
const searchTimeoutRef = React.useRef<NodeJS.Timeout | undefined>(undefined)
```

**问题描述:** `searchTimeoutRef` 用于 debounce 搜索和事件追踪，虽然有两个 useEffect 做了清理，但在 `handleSearchChange` 中使用 `React.startTransition` 包裹 `setSearch` 和 `trackSearchQuery`，如果组件在 debounce 期间卸载，transition 可能在已卸载组件上执行。

**影响范围:** CommandMenu 搜索功能

**严重程度:** High

---

### BUG-008: CommandMenu 的 Ctrl+C 复制逻辑对 page/component 类型复制空内容

**文件:** [command-menu.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/command-menu.tsx#L374-L379)

```ts
if (selectedType === "page" || selectedType === "component") {
  copyToClipboardWithMeta(copyPayload, {
    name: "copy_npm_command",
    properties: { command: copyPayload, pm: packageManager },
  })
}
```

**问题描述:** 对于 `selectedType === "page"` 时，`copyPayload` 在 `handlePageHighlight` 中被设置为空字符串 `""`。复制空字符串没有意义，但仍然会触发事件追踪。对于 component 类型，`copyPayload` 设置正确，但 page 类型不应该触发复制。

**影响范围:** CommandMenu 的 Cmd+C 快捷键功能

**复现步骤:**
1. 打开 CommandMenu (Cmd+K)
2. 高亮一个非 component 页面
3. 按 Cmd+C
4. 复制了空内容但仍然触发了 analytics 事件

**修复建议:**
```ts
if (selectedType === "component" && copyPayload) {
  copyToClipboardWithMeta(copyPayload, ...)
}
```

**严重程度:** High

---

### BUG-009: BlockViewer iframe 使用 loading="lazy" 但立即显示导致加载延迟

**文件:** [block-viewer.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/block-viewer.tsx#L250-L261)

```tsx
<iframe
  key={iframeKey}
  src={`/view/${styleName}/${item.name}`}
  loading="lazy"
  ...
/>
```

**问题描述:** iframe 使用 `loading="lazy"` 延迟加载，但由于 iframe 在视口中立即可见，lazy loading 实际上不会生效，反而可能在某些浏览器中导致额外的加载延迟。

**影响范围:** Block 预览组件

**修复建议:** 移除 `loading="lazy"` 或改为 `loading="eager"`

**严重程度:** High

---

### BUG-010: MobileNav 使用 router.push 与 Link 重复导航

**文件:** [mobile-nav.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/mobile-nav.tsx#L203-L215)

```tsx
<Link
  href={href}
  onClick={() => {
    router.push(href.toString())
    onOpenChange?.(false)
  }}
```

**问题描述:** `Link` 组件本身已经处理导航，再在 onClick 中调用 `router.push` 会导致两次导航：一次是 Next.js 的 Link 客户端导航，一次是 `router.push`。这可能导致导航竞态条件和页面闪烁。

**影响范围:** 移动端导航菜单

**复现步骤:**
1. 在移动端打开导航菜单
2. 点击一个链接
3. 观察可能出现的双次导航行为

**修复建议:**
```tsx
<Link
  href={href}
  onClick={() => {
    onOpenChange?.(false)
  }}
```
移除 `router.push`，让 Link 自己处理导航。

**严重程度:** High

---

### BUG-011: useMutationObserver 的 options 对象在每次渲染时重新创建

**文件:** [use-mutation-observer.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/hooks/use-mutation-observer.ts#L6-L11)

```ts
options: MutationObserverInit = {
  attributes: true,
  characterData: true,
  childList: true,
  subtree: true,
}
```

**问题描述:** 默认参数在每次函数调用时创建新对象，导致 useEffect 的依赖数组中 `options` 总是新引用，effect 会每次渲染都重新执行，频繁创建和销毁 MutationObserver。

**影响范围:** 所有使用 `useMutationObserver` 的组件（CommandMenu 中大量使用）

**修复建议:**
```ts
const defaultOptions: MutationObserverInit = {
  attributes: true,
  characterData: true,
  childList: true,
  subtree: true,
}

export const useMutationObserver = (
  ref: React.RefObject<HTMLElement | null>,
  callback: MutationCallback,
  options: MutationObserverInit = defaultOptions
) => { ... }
```

**严重程度:** High

---

### BUG-012: ThemeProvider 键盘快捷键与输入法冲突

**文件:** [theme-provider.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/theme-provider.tsx#L10-L28)

```ts
if (
  (e.key === "d" || e.key === "D") &&
  !e.metaKey &&
  !e.ctrlKey &&
  !e.altKey
) {
```

**问题描述:** 按 `D` 键切换主题的快捷键没有检查 `e.isComposing`，在中文、日文等输入法组合输入过程中，如果输入包含字母 D，会意外触发主题切换。

**影响范围:** 全局键盘快捷键

**复现步骤:**
1. 切换到中文输入法
2. 在任意非输入区域输入包含 "d" 的拼音
3. 在输入法组合模式下按 "d"，主题会意外切换

**修复建议:**
```ts
if (
  (e.key === "d" || e.key === "D") &&
  !e.metaKey &&
  !e.ctrlKey &&
  !e.altKey &&
  !e.isComposing
) {
```

**严重程度:** High

---

## Medium — 中级别问题

### BUG-013: useLayout localStorage 事件处理中的 setLayout 与 setLayoutState 行为不一致

**文件:** [use-layout.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/hooks/use-layout.tsx#L112-L125)

```ts
const handleStorage = (e: StorageEvent) => {
  if (e.key !== storageKey) return
  if (!e.newValue) {
    setLayout(defaultLayout)  // 调用包装函数
  } else if (e.newValue === "fixed" || e.newValue === "full") {
    setLayoutState(e.newValue)  // 直接调用 useState setter
  }
}
```

**问题描述:** `e.newValue` 存在时直接调用 `setLayoutState` 而不调用 `setLayout`，绕过了 `applyLayout` 中通过 `setLayout` 回调触发的 DOM class 更新。虽然另一个 useEffect 会响应 `layout` 变化并调用 `applyLayout`，但这种不一致容易导致维护混乱。

**影响范围:** Layout 切换功能

**严重程度:** Medium

---

### BUG-014: getColors 函数中的正则表达式可能匹配失败

**文件:** [colors.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/lib/colors.ts#L49-L68)

```ts
const rgb = color.rgb.replace(
  /^rgb\((\d+),(\d+),(\d+)\)$/,
  "$1 $2 $3"
)
```

**问题描述:** 正则表达式假设 rgb 格式严格为 `rgb(r,g,b)`，但如果上游数据源返回 `rgb(r, g, b)`（带空格）或 `rgba(r, g, b, a)`，正则匹配失败会返回原始字符串，后续处理会出错。

**影响范围:** 颜色功能模块

**修复建议:** 使用更宽松的正则或添加错误处理

**严重程度:** Medium

---

### BUG-015: CommandMenu commandFilter 使用字符串拼接可能产生意外匹配

**文件:** [command-menu.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/command-menu.tsx#L129-L138)

```ts
const extendValue = value + " " + (keywords?.join(" ") || "")
if (extendValue.toLowerCase().includes(searchValue.toLowerCase())) {
  return 1
}
```

**问题描述:** `value` 和 `keywords` 简单拼接后搜索，如果 `value` 的结尾和 `keywords` 的开头组合产生意外的匹配词，会导致错误的搜索结果。例如 value="Red" keywords=["black"] 拼接为 "Red black"，搜索 "ed bl" 会匹配到。

**影响范围:** CommandMenu 搜索准确度

**严重程度:** Medium

---

### BUG-016: processMdxForLLMs 同步读取文件可能导致阻塞

**文件:** [llm.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/lib/llm.ts#L86)

```ts
let source = fs.readFileSync(src, "utf8")
```

**问题描述:** 在生成 LLM 友好文档时使用同步文件读取。如果有大量 ComponentPreview 组件，同步读取会阻塞事件循环，导致请求超时。

**影响范围:** LLM 文档生成 API

**严重程度:** Medium

---

### BUG-017: BlockViewer 的 highlightedFiles 访问可能越界

**文件:** [block-viewer.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/block-viewer.tsx#L103)

```ts
const [activeFile, setActiveFile] = React.useState<
  BlockViewerContext["activeFile"]
>(highlightedFiles?.[0].target ?? null)
```

**问题描述:** 当 `highlightedFiles` 是一个空数组 `[]` 时，`highlightedFiles?.[0]` 为 `undefined`，`undefined.target` 会抛出 TypeError。可选链只检查了 `highlightedFiles` 是否为 null/undefined，没有检查数组是否为空。

**影响范围:** BlockViewer 组件初始化

**复现步骤:**
1. 传入空的 `highlightedFiles` 数组
2. BlockViewerProvider 初始化时崩溃

**修复建议:**
```ts
highlightedFiles?.length ? highlightedFiles[0].target ?? null : null
```

**严重程度:** Medium

---

### BUG-018: SiteHeader 中 Suspense fallback={null} 可能导致布局偏移

**文件:** [site-header.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/site-header.tsx#L50-L55)

```tsx
<Suspense fallback={null}>
  <V0Button />
</Suspense>
<Suspense fallback={null}>
  <ProjectForm />
</Suspense>
```

**问题描述:** `fallback={null}` 意味着在组件加载期间不渲染任何内容，可能导致 header 布局在组件加载前后发生变化（高度跳动），影响用户体验。

**影响范围:** 站点头部导航栏

**严重程度:** Medium

---

### BUG-019: globals.css 中 :root 使用 oklch(0% 0 0) 可能导致兼容性问题

**文件:** [globals.css](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/app/globals.css#L76)

```css
--foreground: oklch(0% 0 0);
```

**问题描述:** `oklch(0% 0 0)` 在语法上不正确。正确的 OKLCH 语法应该是 `oklch(lightness chroma hue)`，其中 `0%` 作为第一个参数（亮度）是正确的，但应该写成 `oklch(0 0 0)`（不带 % 符号），因为 OKLCH 的亮度值是 0-1 范围的数值。部分浏览器可能无法正确解析。

**影响范围:** 全局颜色变量

**修复建议:** 使用 `oklch(0 0 0)` 替代 `oklch(0% 0 0)`

**严重程度:** Medium

---

### BUG-020: index page 使用 w-[140vw] 导致移动端水平溢出

**文件:** [page.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/app/(app)/(root)/page.tsx#L65)

```tsx
<section className="-mx-4 w-[140vw] overflow-hidden md:hidden">
```

**问题描述:** 首页图片区域使用 `w-[140vw]` 强制宽度为视口的 140%，虽然有 `overflow-hidden`，但在某些移动浏览器中可能导致水平滚动条短暂出现或触摸行为异常。

**影响范围:** 首页移动端体验

**严重程度:** Medium

---

## Low — 低级别问题

### BUG-021: siteConfig 中的 navItems 与实际路由可能不同步

**文件:** [config.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/lib/config.ts#L11-L41)

**问题描述:** `siteConfig.navItems` 中定义的导航项（如 `/docs/installation`）是硬编码的，如果文档路由结构发生变化（如重命名、删除），配置不会自动更新，导致导航链接失效。

**影响范围:** 导航菜单、CommandMenu

**严重程度:** Low

---

### BUG-022: showMcpDocs 是硬编码常量

**文件:** [flags.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/lib/flags.ts#L1)

```ts
export const showMcpDocs = true
```

**问题描述:** `showMcpDocs` 是一个硬编码的常量，无法通过环境变量或运行时配置控制。如果需要隐藏 MCP 文档，必须修改代码。

**影响范围:** MCP 文档可见性

**严重程度:** Low

---

### BUG-023: useConfig 的 Config 类型过于严格

**文件:** [use-config.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/hooks/use-config.ts#L4-L8)

```ts
type Config = {
  style: "new-york-v4"
  packageManager: "npm" | "yarn" | "pnpm" | "bun"
  installationType: "cli" | "manual"
}
```

**问题描述:** `style` 被硬编码为只能是 `"new-york-v4"`，如果未来添加新的 style 类型或从 localStorage 读取到旧的 style 值，类型不匹配可能导致运行时错误。

**影响范围:** 配置管理

**严重程度:** Low

---

### BUG-024: DocsSidebar 中 EXCLUDED_SECTIONS 和 EXCLUDED_PAGES 使用硬编码

**文件:** [docs-sidebar.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/docs-sidebar.tsx#L64-L65)

```ts
const EXCLUDED_SECTIONS = ["installation", "dark-mode", "changelog", "rtl"]
const EXCLUDED_PAGES = ["/docs", "/docs/changelog", "/docs/rtl", "/docs/new"]
```

**问题描述:** 排除的章节和页面使用硬编码列表，新增需要排除的页面时必须手动更新此列表，容易遗漏。

**影响范围:** 文档侧边栏

**严重程度:** Low

---

### BUG-025: ActiveThemeProvider 中 className 过滤逻辑可能误删

**文件:** [active-theme.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/shadcn-ui/apps/v4/components/active-theme.tsx#L31-L41)

```ts
Array.from(document.body.classList)
  .filter((className) => className.startsWith("theme-"))
  .forEach((className) => {
    document.body.classList.remove(className)
  })
```

**问题描述:** 移除所有以 `theme-` 开头的 class，如果其他组件也使用 `theme-` 前缀（如 `theme-scaled`），会被意外移除。虽然代码中对 `-scaled` 后缀做了特殊处理（第 38-40 行），但逻辑不够健壮。

**影响范围:** 主题切换功能

**严重程度:** Low

---

## Bug 统计汇总

| 级别 | 数量 | Bug ID |
|------|------|--------|
| Critical | 5 | BUG-001 ~ BUG-005 |
| High | 7 | BUG-006 ~ BUG-012 |
| Medium | 8 | BUG-013 ~ BUG-020 |
| Low | 5 | BUG-021 ~ BUG-025 |
| **总计** | **25** | |

---

## 建议修复优先级

### Phase 1 — 立即修复 (Critical)
1. **BUG-002/003**: 修复环境变量缺失时的崩溃 — 影响所有页面元数据
2. **BUG-004**: 修复 useCopyToClipboard 内存泄漏 — 影响所有复制功能
3. **BUG-001**: 关闭生产环境 TypeScript 错误忽略 — 保证代码质量

### Phase 2 — 短期修复 (High)
1. **BUG-010**: 修复 MobileNav 双重导航 — 影响移动端核心体验
2. **BUG-012**: 修复输入法快捷键冲突 — 影响多语言用户
3. **BUG-011**: 修复 useMutationObserver 性能问题 — 影响 CommandMenu 性能
4. **BUG-006**: 修复 useMobile hydration 不一致 — 影响响应式渲染

### Phase 3 — 中期优化 (Medium/Low)
1. **BUG-017**: 修复 BlockViewer 数组越界
2. **BUG-014**: 修复颜色正则表达式
3. **BUG-009**: 修复 iframe lazy loading
4. 其余 Low/Medium 级别问题按实际情况排期
