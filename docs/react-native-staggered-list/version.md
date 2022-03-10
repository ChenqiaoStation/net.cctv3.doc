# 版本

## 🍀 Version 1.x

🍀 Published react-native-staggered-list，支持分页加载 & Header & Footer 等功能。

### Version 1.9.0

- 🐞 修改加载完成 `onLoadComplete()` 的逻辑，因为当数据量比较大的时候，即使你不滑动，他也会不断 `onLoadComplete()`。

  - 1. 资源的浪费，不断加载下一页，会导致服务端的压力也变大。
  - 2. 下拉刷新有 `BUG`，为了让每一列能得到比较准确的高度，我会在添加的时候，加一个 `计时器`，如果他在不断的渲染的过程中，你突然下拉刷新，状态不好控制，会有意想不到的 `BUG` 出现。

所以这次更新，我回调的逻辑是 `每一列都滑动到底部了`，并且 `数据渲染完了`，这个时候我再去回调。



### Version 1.8.0

- 🆕 新增泛型 `ItemT` 的支持。

### Version 1.8.1

- 🛠 修改 README.md。

### Version 1.7.3

- 🛠 更新 README.md。

### Version 1.7.2

- 🐞 还是防抖的逻辑，不要控制 `refreshing`，控制 `r` → `setR(Math.random())`。

### Version 1.7.1

- 🐞 修改了一下防抖的时机，改为 `onRefresh()` 回调前就进行处理。

### Version 1.7.0

- 🐞 新增下拉刷新的防抖的处理，防止用户不断下拉刷新造成重复渲染的 BUG。
- 💄 优化瀑布流排列，不可见区域采用延时处理，排列更为准确。

### Version 1.6.2

- 🐞 `RefreshControl` 报错。

### Version 1.6.0

- 🚀 全新升级: 最外层由 `ScrollView` → `VirtualizedList`，包括内层的 `View` 堆砌也换成了 `VirtualizedList`。而且还解决了一些奇怪的问题，比如之前遇见过把 `Banner` 放到 `Header` 里面无法自动轮播，必须要手动碰一下才可以。

### Version 1.6.1

- 🗑 删除 `Header` 以及 `Footer` 的测量的回调。

### Version 1.5.1

- 🐞。

### Version 1.5.0

- 🚀 综合 `从左到右依次填充` 和 `最小高度填充` 两种方式，使瀑布流两边高度尽量一致。
- 🆕 新增 `List` → `Item` 右下角的 `index`，便于直观的看到渲染顺序和效果。

### Version 1.4.2

- 🐞 瀑布流渲染的错误。

### Version 1.4.1

- 🐞 潜藏的 BUG。

### Version 1.4.0

- 🗑 移除原来除了测量除了 `header` 和 `footer` 测量的逻辑，直接从左到右每一列挨个填充 `Item`。

### Version 1.3.0

- 🆕 新增 `Columns` 样式自定义，可以自己调节 `Header` 和 `Columns` 之间的距离，也可以自己调节 `Columns` 和屏幕两边的边距。

### Version 1.2.1

- 🛠 修改 README.md。

### Version 1.2.0

- 🆕 新增下拉刷新功能 `onrefresh: () => void`。
- 🛠 更新 README.md，添加运行截图，以及示例代码。

### Version 1.1.1

- 🐞 修改初始化 `measureResult`，防止 `header` 或者 `footer` 为 `null` 造成的回调参数为空的 BUG。

### Version 1.1.0

- 🆕 新增原生滑动事件的回调: `onScroll: (NativeSyntheticEvent<NativeScrollEvent>) => void`。

- 🆕 新增 Header & Columns & Footer 测量高度的回调。

有了以上这两个事件，就可以在使用的时候，实现 `TabBar` 的渐变以及吸顶效果。

### Version 1.0.1

- 🗑 删除多余依赖。
- ✍🏻 重命名 `StaggeredListView` → `StaggeredList`。
- 🛠 更新 README.md。
