https://github.com/mui/base-ui/issues/5131


## 原因
分析完成。核心结论：

**Bug 根因**：[useAnchorPositioning.ts:191-214](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/base-ui/packages/react/src/utils/useAnchorPositioning.ts#L191-L214) 的 `bias = 1` 逻辑会向 collision padding 的「preferred side 的对边」加 1px，且无条件应用到所有平台/所有 popup 组件。在 `flip()` 中又额外 `+ bias` 一次（[L273-L278](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/base-ui/packages/react/src/utils/useAnchorPositioning.ts#L273-L278)），导致默认 `side="bottom"` + `collisionPadding=5` 时，top 实际为 6（flip 检测里为 7），产生可见的 1px 偏差。

**推荐修复**：删除该 bias 块（及 flip 中第二次 `+ bias`），让 `flip` 在对称 padding 下自然偏向首选侧；若需保留 iOS 键盘修复，应通过 `openMethod === 'touch'` 或 UA 检测做条件门控，而非全局生效。


## 复现

>在源代码的示例中，复现这个bug

我新建了一个 experiment 来可视化复现 issue #5131 的 1px 偏差。

### 文件

- [collision-padding-bias.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/docs/src/app/(private)/experiments/popover/collision-padding-bias.tsx) — experiment 主文件
- [collision-padding-bias.module.css](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/docs/src/app/(private)/experiments/popover/collision-padding-bias.module.css) — 样式

### 访问 URL

Dev server 已在 `http://localhost:3005` 启动，直接访问：

`http://localhost:3005/experiments/popover/collision-padding-bias`

### 复现原理

1. **viewport**: `400×400` 的 `overflow:hidden` 容器，作为 `clipping-ancestors` 边界
2. **anchor**: `30×30`，根据 `side` 自动贴到 viewport 对应边
3. **popup**: `350×350`（几乎占满 viewport），强制 flip + shift 撞到对侧边墙
4. **测量**: 通过 `getBoundingClientRect` 实时测量 popup 到 viewport 四边的实际距离

### 预期结果

切换 `side` 时，会看到对应「对侧边」的实际距离比 `collisionPadding` 多 `+1px`：

| `side` | biased edge | 实际距离（collisionPadding=20） |
|--------|-------------|------------------------------|
| `bottom`（默认） | `top` | `21px`（+1） |
| `top` | `bottom` | `21px`（+1） |
| `right` | `left` | `21px`（+1） |
| `left` | `right` | `21px`（+1） |

### 已通过的检查

- `pnpm tsc --noEmit -p docs/tsconfig.json` ✅
- `npx eslint <file>` ✅
- `pnpm stylelint <file>` ✅
- `pnpm prettier --check <file>` ✅
- HTTP 200，页面编译运行 ✅