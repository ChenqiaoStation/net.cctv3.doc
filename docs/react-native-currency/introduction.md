# 简介

React Native 价格、货币表示，因为像商城项目经常用到价格，干脆封装一个算了。

**虽然代码很简单，路过的老铁们还是右上角的小星星走起来。**

![](https://net-cctv3.oss-cn-qingdao.aliyuncs.com/net.cctv3.open/ReactNativeCurrency0309.jpg);

## 😍 组件特色

没啥特色，就是封装了一下常用的价格标签 😌。

## 😌 命名规范

| Name       | Type                   | Description                                                                                                     |
| :--------- | :--------------------- | :-------------------------------------------------------------------------------------------------------------- |
| name       | `string`               | 货币单位，默认 `¥`。                                                                                            |
| value      | `number` or `string`   | 金额。如果是 `number` 类型，默认精确到两位小数，可以通过 `precision` 控制精度。如果是字符串类型，我就不处理了。 |
| interval   | `number`               | `货币单位` 和 `金额` 之间的距离，默认 `0`。                                                                     |
| precision  | `number`               | 金额的精度，默认为 `2`，即保留小数点后两位小数。                                                                |
| style      | `StyleProp<ViewStyle>` | 整体的样式。                                                                                                    |
| nameStyle  | `StyleProp<TextStyle>` | 货币单位的样式。                                                                                                |
| valueStyle | `StyleProp<TextStyle>` | 金额的样式。                                                                                                    |

## 🤔 如何使用

```bash
npm install react-native-currency
```

```javascript
<Currency
  value={price}
  nameStyle={{ color: "#F72033", fontSize: 14 * vw }}
  valueStyle={{ color: "#F72033", fontSize: 24 * vw }}
/>
```

## 🙄 版本更新记录

### 🍀 Version 1.0.0

🍀 Published react-native-currency.
