在业务工作中，如果你需要封装一个组件，你肯定会遇到这样的场景：组件的默认的配置对象defaultOption，需要与暴露出去的api进行合并，保证用户自定义的值能覆盖默认配置，但未定义的值取默认配置。其实，这本质上就是需要把defaultOption对象与外部传入的配置对象进行合并。我们经常会看到两种写法：`Object.assign`和`$.extend`，那这两者有没有区别呢？

## Object.assign()和$.extend()用法

首先呈现官方文档：[Object.assign()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)、[jQuery.extend()](https://api.jquery.com/jQuery.extend/)。

- Object.assign()
`Object.assign(target, ...sources)`，Returns: Object

- $.extend()
`jQuery.extend( target [, object1 ] [, objectN ] )`, Returns: Object
`jQuery.extend( [deep ], target, object1 [, objectN ] )`， If true, the merge becomes recursive（递归） (aka. deep copy，深拷贝). 


The Object.assign() method only copies **enumerable** and **own** properties from a source object to a target object.

$.extends(): Merge the contents of two or more objects together into the first object.

## 区别
两者区别其实不大：
- 主要在于$.extend()如果给第一个参数传入`true`可实现**deep merge**，而Object.assign只会`copies that reference value`。
- 另外，就是使用$.extend()需要提前引入jQuery

## Talk is Cheap, show me the code
- 如果不想改变target object，可以将第一个参数传入空对象{}
```javascript
var opts = Object.assign( {}, defaults, options );
// 或者
var opts = $.extend( {}, defaults, options );
```

- Polyfill of Object.extend()
```javascript
if (typeof Object.assign != 'function') {
  // Must be writable: true, enumerable: false, configurable: true
  Object.defineProperty(Object, "assign", {
    value: function assign(target, varArgs) { // .length of function is 2
      'use strict';
      if (target == null) { // TypeError if undefined or null
        throw new TypeError('Cannot convert undefined or null to object');
      }

      var to = Object(target);

      for (var index = 1; index < arguments.length; index++) {
        var nextSource = arguments[index];

        if (nextSource != null) { // Skip over if undefined or null
          for (var nextKey in nextSource) {
            // Avoid bugs when hasOwnProperty is shadowed
            if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
              to[nextKey] = nextSource[nextKey];
            }
          }
        }
      }
      return to;
    },
    writable: true,
    configurable: true
  });
}
```

- 如何使用Object.assign()实现`deep merge`

```javascript

function isObject(item) {
  return (item && typeof item === 'object' && !Array.isArray(item));
}

function mergeDeep(target, ...sources) {
  if (!sources.length) return target;
  const source = sources.shift();

  if (isObject(target) && isObject(source)) {
    for (const key in source) {
      if (isObject(source[key])) {
        if (!target[key]) Object.assign(target, { [key]: {} });
        mergeDeep(target[key], source[key]);
      } else {
        Object.assign(target, { [key]: source[key] });
      }
    }
  }
  return mergeDeep(target, ...sources);
}
```

## 参考资料
- https://stackoverflow.com/questions/38345937/object-assign-vs-extend
- https://stackoverflow.com/questions/27936772/how-to-deep-merge-instead-of-shallow-merge
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign
- https://api.jquery.com/jQuery.extend/

（本篇完）