# 简介

基于 `VirtualizedList` 封装的 `react-native` 可以自己测量 Item 高度的瀑布流组件。

之前看了 `GitHub` 上面几个瀑布流组件的库，基本都是踏 🐴 瞎 🐔 儿扯，还得自己手动传 `View` 的高度。

## 😍 组件特色

### 🌽 智能排列

- `ScrollView` → `VirtualizedList`。
- 经过这么多期不断优化迭代，可见部分采用 `从左到右` 依次填充，不可见部分采用 `高度最小列` 优先填充。

### 🍉 泛型支持

像 `FlatList` 一样 `renderItem`，然后支持自己的 `ItemT`。

![](https://net-cctv3.oss-cn-qingdao.aliyuncs.com/net.cctv3.open/StaggeredListT0228.jpg)

### 🍇 扩展性强

- 支持自定义列数 `columns`。

- 支持下拉刷新 `onRefresh()`。

- 支持自定义 `Header` 和 `Footer`。

- 支持自定义列表的 `Container` 样式。

- 支持滑动监听 `onScroll(NativeSyntheticEvent<NativeScrollEvent>) => void`。

- 支持分页加载，加载完了一页回调 `onCompleted: () => void`，接着来下一页的数据也是 `OJBK` 的。

**觉得有用，路过的各位老铁们右上角的小星星走起来，谢谢。**

![](https://net-cctv3.oss-cn-qingdao.aliyuncs.com/net.cctv3.open/StaggeredListDemo0215.gif)

## 😌 命名规范

整体的设计思想模仿的是 `FlatList`，提供以下内容的自定义。

| Name                         | Type                                                | Description                                                                     |
| :--------------------------- | :-------------------------------------------------- | :------------------------------------------------------------------------------ |
| columns                      | `number`                                            | 列数。                                                                          |
| datas                        | `ItemT []`                                          | 数据源 ( 支持泛型 ItemT )。                                                     |
| renderItem                   | `(item: ItemT) => React.Node`                       | 跟 `FlatList` 一样，渲染什么您说了算。                                          |
| onLoadComplete               | `() => void`                                        | 数据全部渲染完成时候的回调，比如 `分页` 这种应用场景。                          |
| header                       | `React.Node`                                        | 瀑布流的头部。                                                                  |
| footer                       | `React.Node`                                        | 瀑布流的尾部。                                                                  |
| showsVerticalScrollIndicator | `boolean`                                           | 是否显示 `纵向` 滚动条。                                                        |
| onScroll                     | `(NativeSyntheticEvent<NativeScrollEvent>) => void` | 滑动事件，比如 `吸顶` 要判断滑动距离这种场景。                                  |
| onRefresh                    | `() => void`                                        | 下拉刷新时候的回调。                                                            |
| columnsStyle                 | `StyleProp<ViewStyle>`                              | 瀑布流的 Container 的样式，可以控制 `内边距` 以及 `列表` 和 `Header` 的距离等。 |

## 🤔 如何使用

```bash
npm install react-native-staggered-list
```

```JS
<StaggeredList
  onRefresh={() => {
    setR(Math.random());
    setPageIndex(1);
  }}
  header={<View />}
  datas={datas}
  renderItem={(item) => <HomeItem item={item} />}
  columns={2}
  columnsStyle={{
    justifyContent: 'space-around',
    paddingHorizontal: 5 * vw,
  }}
  onScroll={(e) => {
    setTabBarOpacity(
      Math.min(1, e.nativeEvent.contentOffset.y / imgHeight),
    );
  }}
  onLoadComplete={() => {
    setPageIndex((t) => t + 1);
  }}
/>
```

## 😳 实现原理

两种思路：

- 一种就是直接挨个 `index%column` 往里面填充，适合左右两边高度差不多相等的情况。

- 另外一种就是等上一个渲染完了，然后回调完了高度，找出这几列高度最低的一个，然后渲染下一个。

```javascript
// views[findMinColumn()].current.push(uniteEffects.datas[index.index]);
```

最开始的时候确实用的是第二个思路，但是后来发现一种场景好像不太合适。比如说如果封装一个自动高度的图片组件。他会使目前的 `Item` → `onLayout()` 两次。

> 因为 onLayout() 只要 Item 布局变了，他就会回调。

那么我就要在 `renderItem()` 里面做文章，但是 `Item` 好像拿不到 `props.children` 里面的状态，这就很麻烦。想了很多方法，感觉都不是很好。

代码贴出来:

```js
const Item: React.FC<ItemProps> = (props) => {
  return (
    <View
      onLayout={(layout) => {
        // console.log(layout.nativeEvent.layout);
        layout.nativeEvent.layout.height > 0 &&
          props.onMeasuredHeight(layout.nativeEvent.layout.height);
      }}
    >
      {React.isValidElement(props.children) &&
        React.cloneElement(props.children, {
          nextRender: (next: boolean, height: number) => {
            next && props.onMeasuredHeight(height);
          },
        })}
      {/* {props.children} */}
    </View>
  );
};
```

而且 `Item` 会不断的 `onLayout()` 还会有硬件方面性能的损失，再就是就算是拿到 `renderItem` 里面的状态的话，那也是像老母鸡 🐔 下蛋 🥚 一样，一个一个的渲染，体验上也说不过去。

~~综上所述，从 `1.4.0` 版本开始，准备使用第一种思路，直接从左到右挨个排列。~~

目前的思路是:

为了节省时间，优化体验，刚开始肉眼可见的区域是直接从左到右依次填充。给了一个高度容错的范围，默认 `[0, 2*props.columns]`。在这个范围里面的数据，渲染的时候，延时 `1000ms`，这样儿确保了前面的数据渲染完了，拿到的高度能更真实一些。也就是说最后这几个 `Item` 是优化布局，纠错用的。
