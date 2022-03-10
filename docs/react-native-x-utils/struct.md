# Class 对象类

## useRandomProperty(clazz: any)

随机取 Object 的属性。

- `clazz`: Object 对象。

```javascript
const useRandomProperty = (clazz: any) => {
  var keys = Object.keys(clazz);
  return clazz[keys[(keys.length * Math.random()) << 0]];
};
```
