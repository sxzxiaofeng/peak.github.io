---
title: 常用utils方法
date: 2022-07-28 15:19:49
tags: JavaScript
type: "tags"
---
<a name="WQVPU"></a>
#### 判断是否是数组
```javascript
 const isArray = Array.isArray || function(object){ return object instanceof Array }
```

<a name="ymvWR"></a>
#### 判断是否是对象
```javascript
const isObject = (obj) => 
  obj !== null && typeof obj === 'object'
```

<a name="F3wsL"></a>
#### 判断是否是空对象
```javascript
const isEmptyObject = function(obj) {
  for (let name in obj) return false
  return true
}
```

<a name="jXD7u"></a>
#### 检测对象属性
```javascript
var foo = {
    hasOwnProperty: function() {
    return false;
  },
    bar: 'Here be dragons'
};

const hasOwn = (obj, key) => 
  Object.prototype.hasOwnProperty.call(obj, key);

// Object.prototype.hasOwnProperty.call() 
// 确保使用的是Object原型上的hasOwnProperty方法

Object.prototype.hasOwnProperty(foo, 'bar') // false
Object.prototype.hasOwnProperty.call(foo, 'bar') // true
```

<a name="d41d8cd9"></a>
#### 判断是否是普通对象
```javascript
function isPlainObject(val) {
  if (typeof val !== 'object' || val === null) 
    return false
  return Object.getPrototypeOf(val) === Object.prototype
}
```

<a name="jzAhO"></a>
#### 节流
```javascript
## 节流
每隔一段时间后执行一次，也就是降低频率，将高频操作优化成低频操作(单位时间内,第一次生效)

- 应用场景：
  - 鼠标/触摸屏的 mouseover/touchmove 事件
  - 页面窗口的 resize 事件
  - 滚动条的 scroll 事件
  function throttle(fn,delay=500){
      let lastTime = 0;
      // return的是DOM事件的回调
      return function(){
      	let nowTime = Date.now();
     	if(nowTime - lastTime > delay){
             lastTime = nowTime;
             fn.apply(this,arguments);
          }
      }
  }
```

<a name="nt73g"></a>
#### 防抖
```javascript
## 防抖
- 当持续触发事件时，一定时间段内没有再触发事件，事件处理函数才会执行一次，如果设定的时间到来之前，
  又一次触发了事件，就重新开始延时。(让函数在规定时间内只调用一次。 触发最后一次)

- 应用场景：按钮点击事件/input 事件，防止用户多次重复提交
const debounce = (fn, delay=500) =>{
    let timer = null;
    return function(...args){
        if(timer) clearTimeout(timer);
        timer = setTimeout(() => {
           fn.apply(this,args); 
        },delay)
    }
}
```


<a name="mxs7h"></a>
#### 深拷贝
```javascript
## 深复制
const deepClone = (target) => {
    // 获取类型
    const type = Object.prototype.toString.call(target).slice(8,-1);
    let result = null;
    if(type === 'Array'){
        result = [];
    } else if(type === 'Object'){
        result = {};
    } else {
        return target; // 其它类型不用克隆
    }
    for(let i in target){
     /*
        // 属性值 
        const value = target[key];
        // 如果value是对象/数组，还需要克隆一份新的
        const newValue = deepClone(value);
        // 添加到result中
        result[key] = newValue;
    */ 
        result[i] = deepClone(target[i]);
    }
    return result;
}
```

<a name="ghWXf"></a>
#### 数组扁平化
```javascript
## 数组扁平化
const myFlat = (arr) => {
  let result = [];
  //遍历数组的每一项
  arr.forEach((item) => {
    //如果该项是数组就继续遍历,否则push进空数组
    if(Array.isArray(item)){
      result = result.concat(myFlat(item));
    }
    else{
      result.push(item);
    }
  });
  return result;
};
```

<a name="DH9UT"></a>
#### 扁平数组转tree
```javascript
// 主要思路先把数据转成Map去存储，之后遍历的同时借助对象的引用，直接从Map找对应的数据做存储。
   在遍历的时候即做Map存储，又找对应关系，性能会更好。
function arrayToTree(items) {
  const result = [];   // 存放结果集
  const itemMap = {}; 
  for (const item of items) {
    const id = item.id;
    const pid = item.pid;

    if (!itemMap[id]) {
      itemMap[id] = {
        children: [],
      }
    }

    itemMap[id] = {
      ...item,
      children: itemMap[id]['children']
    }

    const treeItem =  itemMap[id];

    if (pid === 0) {
      result.push(treeItem);
    } else {
      if (!itemMap[pid]) {
        itemMap[pid] = {
          children: [],
        }
      }
      itemMap[pid].children.push(treeItem)
    }

  }
  return result;
}

/**
 * 将扁平数组转换成树结构
 * @param list 扁平数组
 * @param options 树解析map
 * @returns
 */
function transformTree(
  list: any[] = [],
  options = {
    keyField: 'id',
    childField: 'children',
    parentField: 'pid',
  },
) {
  const { keyField, childField, parentField } = options;

  const tree = [];
  const record: any = {};

  for (let i = 0, len = list.length; i < len; i++) {
    const item = list[i];
    const id = item[keyField];

    if (!id) {
      continue;
    }

    if (record[id]) {
      item[childField] = record[id];
    } else {
      item[childField] = record[id] = [];
    }

    if (item[parentField]) {
      const parentId = item[parentField];

      if (!record[parentId]) {
        record[parentId] = [];
      }

      record[parentId].push(item);
    } else {
      tree.push(item);
    }
  }

  return tree;
}

export default transformTree;
```

<a name="CA0XU"></a>
#### call apply 方法
```javascript
## call apply 方法
Function.prototype.myCall = function(context){
    // this表示将来调用myCall函数的对象
    if(typeof this !== 'function'){
        throw new TypeError('error');
    }
    context = context || window;
    context.fn = this; //给context创建一个fn属性,值设置为需要调用的函数(this指向context)
    const args = [...arguments].slice(1); // 剥离参数,从2个开始截取到最后
    const result = context.fn(...args);
    delete context.fn;
    return result;
}

Function.prototype.myApply = function(context,args){
    if(typeof this!== 'function'){
        throw new TypeError('error');
    }
    context = context || window;
    context.fn = this;
    const result = context.fn(...args);
    delete context.fn;
    return result;
}
```

<a name="xIILp"></a>
####  bind方法
```javascript
## bind方法
Function.prototype.myBind = function(context){
     if(typeof this!== 'function'){
        throw new TypeError('error');
    }
    const _this = this;
    const args = [...arguments].slice(1);
    // 返回一个函数
    return function F(){
        // 返回的是函数,可以new F()调用,需要判断
        if(this instanceof F){
            return new _this.(..args,...arguments);
        }
        // 直接调用 因为bind方法可以实现类似f.bind(obj,1)(2),所以需要将两边的参数拼接起来
        return _this.apply(context,args.concat(...arguments));
    }
}
```


<a name="yt7A9"></a>
#### 自定义实现 new
```javascript
### new 关键字做了什么

1.调用函数
2.创建一个新的对象,把实例对象的隐式原型指向构造函数的显式原型
3.改变 this 指向,把 this 绑定在创建的实例对象上
4.返回一个对象,如果构造函数本身返回的是一个对象,那么就返回这个对象,如果不是就返回这个实例对象

#### 自定义实现 new 关键字
function _new(fn,...args){
    //创建一个实例对象
  const obj = Object.create(fn.prototype)
  	//改变this指向
  const result = fn.apply(obj,args)
  return result instanceOf Object ? result : obj
}
```


<a name="k38Iw"></a>
#### 实现instanceof
```javascript
### 实现instanceof
function instance_Of(L, R) {
    L = L.__proto__;
    R = R.prototype;
    while (true) {
      if (L === null) return false;
      if (L === R) return true;
      L = L.__proto__;
    }
  }
```


<a name="M18F7"></a>
#### 自定义map/reduce
```javascript
## 自定义map方法
      Array.prototype.myMap = function(cb) {
        const newArr = []
        for (let i = 0; i < this.length; i++) {
          newArr.push(cb(this[i], i)) // this[i] 表示数组当前的item
        }
        return newArr
      }

## 自定义reduce方法 reduce接收4个参数
    Accumulator (acc) (累计器)
    Current Value (cur) (当前值)
    Current Index (idx) (当前索引)
    Source Array (src) (源数组)
   Array.prototype.myReduce = function(cb, initValue) {
        let total = initValue
        for (let i = 0; i < this.length; i++) {
          total = cb(total, this[i], i, this)
        }
        return total
    }
```

#### 数组去重
```javascript
## 数组去重
function unique1(arr){
   const newArr = [];
   arr.forEach(item => {
       if(newArr.indexOf(item) === -1){
           newArr.push(item);
       }
   })
    return newArr;
}

function unique2(arr){
    return arr.filter((item,index) => index === arr.indexOf(item));
}

function unique3(arr){
    return [...new Set(arr)];
}

// 利用对象属性名不能重复去重
function unique4(arr){
    const obj = {};
    arr.forEach((item) =>{
          // 把数组的每一项作为对象的key
            obj[item] = 0;
        });
     //对象的键名不能重复,重复会被覆盖
    const newArr = [];
    for(const key in obj){
        //对象的键名是字符串,用+转换成number类型
        newArr.push(+key);
    }
    return newArr;
}
```

<a name="sBL28"></a>
#### 数组对象去重
```javascript

// 通过数组对象的id属性去重
function uniqueArrById(arr) {
  let obj = {};
  return arr.reduce((cur, next) => {
    obj[next.id] ? "" : (obj[next.id] = true && cur.push(next));
    return cur;
  }, []);
}


输入:
[{a:1,b:2,c:3},{b:2,c:3,a:1},{d:2,c:2}]
输出:
[{a:1,b:2,c:3},{d:2,c:2}]
function objSort(obj){
    let newObj = {}
    Object.keys(obj).sort().map(key => {
        newObj[key] = obj[key]
    })
    return JSON.stringify(newObj)
}

function unique(arr){
    let set = new Set();
    for(let i=0;i<arr.length;i++){
        let str = objSort(arr[i])
        set.add(str)
    }
    arr = [...set].map(item => {
        return JSON.parse(item)
    })
    return arr
}
```

<a name="RA3Op"></a>
#### 数组对象合并
```javascript
/*
  arr = [{id: 1, guid:'1234'}, {id: 2, guid:'3456'}, {id:1, guid: '4567'}]
  => [{id: 1, guid:['1234','4567']}, {id: 2, guid:'3456'}]
*/
function mergeById(arr) {
    let tempArr = [], newArr = []
    for (let i = 0; i < arr.length; i++) {
      if (tempArr.indexOf(arr[i].id) === -1) {
        newArr.push({
          id: arr[i].id,
          guid: [arr[i].guid]
        })
        
        tempArr.push(arr[i].id);
        
      } else {
        for (let j = 0; j < newArr.length; j++) {
          if (newArr[j].id == arr[i].id) {
            newArr[j].guid.push(arr[i].guid)
          }
        }
      }
    }
  
  return newArr
}

function mergeById(arr) {
    const newArr = arr.reduce((acc, cur) => {
      const index = acc.findIndex((item) => item.id === cur.id);
      if (index === -1) {
        cur.guid=[cur.guid];
        acc.push(cur);
      } else {
        // acc[index].guid += cur.guid;
        acc[index].guid.push(cur.guid);
      }
      return acc;
  },[]);
  
  return newArr
}

```
<a name="I46X0"></a>
#### 数字转千分位
```javascript
## 数字转千分位
//num.toLocaleString() 直接可以转千分位
function money(num)
   // return (''+num).replace(/(\d)(?=(\d{3})+(?!\d))/g, '$1,');
   return (''+num).replace(/(\d)(?=(\d{3})+\.)/g, '$1,');
}

function money1(num){
    // 先把数字换成字符串，然后转换成数组，反转之后，再组合成字符串
    var reverseStr = num.toString().split('').reverse().join('');
    // 用正则替换，每隔3位加一个逗号
    reverseStr = reverseStr.replace(/(\d{3})/g,'$1,');
    // 处理正好三位的情况，如 123 -> ,123
    reverseStr = reverseStr.replace(/\,$/,'');
    // 把加了逗号的字符串反转回正常的顺序
    reverseStr = reverseStr.split('').reverse().join('');
    return reverseStr;
}
```


<a name="vuB6Q"></a>
#### 获取日期
```javascript
## 获取今天日期 格式为 y-mm-dd
function getToday(){
  let now = new Date();
  let y = now.getFullYear();
  let m = now.getMonth() + 1;
  let d = now.getDate();
  return y + '-' + (m < 10 ? '0' + m : m) + '-' +  (d < 10 ? '0' + d : d);
}
console.log(getToday());
```


<a name="nAmXX"></a>
#### 转驼峰 
```javascript
// str = get-element-by-id => getElementById
function combo(str){
   let arr = str.split('-');
   for(let i = 1; i < arr.length; i++){
       arr[i] = arr[i].charAt(0).toUpperCase() + arr[i].sbustr(1,arr[i].length - 1);
   }
   return arr.join('');
}

const camelize = function(str) {
    return str.replace(/-+(.)?/g, (match, chr) => chr ? chr.toUpperCase() : '')
}

const camelize = function(s) {
    return s.replace(/-\w/g, function(x) {
        return x.slice(1).toUpperCase();
    })
}
 
const convertCamelCase = (str) => {
  return str.split('-').reduce(
    (preStr, curStr, idx) =>
      preStr +
      (idx === 0
        ? curStr
        : curStr[0].toUpperCase() + curStr.slice(1)),
    ''
  );
}
```

<a name="nVjo6"></a>
#### 清楚字符串前后空格
```javascript
function strTrim(str){
    return str.replace(/^\s+/,'').replace(/\s+$/,'');
}
```

<a name="eyopR"></a>
#### 冒泡排序
```javascript
// 基础版本
const bubbleSort = (arr) => {
    for(let i = 0; i < arr.length - 1; i++){ // -1 自己不用和自己比较
       for(let j = 0; j < arr.length - 1 - i; j++){ // -i 每遍历一次,就确定一个数的顺序
           if(arr[j] > arr[j+1]){
               [arr[j],arr[j+1]] = [arr[j+1],arr[j]];
           }
       }
    }
    return arr;
}


// 冒泡排序的两个优化点
## 1,定义一个变量做标识,当数组是有序状态时,停止后续的遍历
const bubbleSort1 = (arr) => {
    let isSorted = true; // 有序标记,每一轮初始值都为true
    for(let i = 0; i < arr.length - 1; i++){ 
       for(let j = 0; j < arr.length - 1 - i; j++){ 
           if(arr[j] > arr[j+1]){
               [arr[j],arr[j+1]] = [arr[j+1],arr[j]];
               isSorted = false; // 有元素交换,则不是有序,标记为false
           }
       }
        if(isSorted) break; // 为true,说明没有元素交换,数列已然有序,则退出外层循环
    }
    return arr;
}

## 2,记录最后一次元素交换的位置,设置为无序数列的边界,往后就是有序区
let arr = [3,4,2,1,5,6,7,8]; // 元素后4位已然有序,不需要多次遍历

const bubbleSort3 = (arr) => {
     // 记录最后一次交换位置
    let lastExchangeIndex = 0;
    // 无序数列的边界,每次比较只需要比较到这里为止
    let sortBorder = arr.length - 1;
    
    for(let i = 0; i < arr.length - 1; i++){ 
       // 有序标记,每一轮初始值都为true 
       let isSorted = true;
       for(let j = 0; j < sortBorder; j++){ 
           if(arr[j] > arr[j+1]){
               [arr[j],arr[j+1]] = [arr[j+1],arr[j]];
               isSorted = false; // 有元素交换,则不是有序,标记为false
               // 更新元素最后一次交换的位置
               lastExchangeIndex = j;
           }
       }
        // 更新边界值,后面的已然有序,不用再遍历
        sortBorder = lastExchangeIndex; 
        if(isSorted) break; // 为true,说明没有元素交换,数列已然有序,则退出外层循环
    }
    return arr;
}
```


<a name="dhnQc"></a>
#### 快排
```javascript
## 快排
function quickSort(arr){
    // 保存数组的长度提高效率
    const len = arr.length;
    // 设置边界,防止递归出现堆栈溢出
    if(len <= 1) return array;

      let left = [];
      let right = [];
      cosnt pivot = array[0];//用数组第一个值,做基准值更方便
      //也可取出数组中间值做基准值, 用第一个数更简单方便
      // let pivot = array.splice((Math.floor(array.length/2)),1)[0];
      
    for(let i = 1; i < len; i++) {
      if(arr[i] >= pivot){
          right.push(arr[i]);
      } else {
          left.push(arr[i]);
      }
    }
    return [...quickSort(left),pivot,...quickSort(right)];
}
```


<a name="YBBOU"></a>
#### 二叉搜索
```javascript

function BinarySearch2 (arr, target) {
    let from = 0;
    let to = arr.length - 1;
    let mid;
    while (from <= to) {
        mid = Math.floor((from + to)/2)
        if (arr[mid] > target) {
            to = mid - 1
        } else if (arr[mid] < target) {
            from = mid + 1
        } else {
            return mid
        }
    }
    return -1
}
```


```javascript
// 提取arr对象数组中子孙元素的id值,组成一个一维数组
let arr = [
  {
    id: 1,
    children: [
      {
        id: 2,
        children: [
          {
            id: 3,
            children: null,
          },
        ],
      },
    ],
  },
  {
    id: 1,
    children: [
      {
        id: 2,
        children: [
          {
            id: 3,
            children: null,
          },
        ],
      },
    ],
  },
];

function list(arr) {
  let obj = {};
  function getList(node) {
    let list = [];
    list.push(node.id);
    if (node.children) {
      for (let i = 0; i < node.children.length; i++) {
        list.push(...getList(node.children[i]));
      }
    }
    return list;
  }
  for (let i = 0; i < arr.length; i++) {
    obj[i] = getList(arr[i]);
  }
  return Object.values(obj);
}
console.log(list(arr).flat())
```


<a name="p0Kf8"></a>
#### 判断两个对象是否相等
```javascript
function shallowEqual(obj1={}, obj2={}) {
  if (obj1 === obj2) {
  	return true
  }
	
  if (obj1 === null || typeof obj1 !== 'object' || obj2 === null || typeof obj2 !== 'object') {
  	return false
  }
  
  const keys1 = Object.keys(obj1);
  const keys2 = Object.keys(obj2);
  
  if (keys1.length !== keys2.lenght) {
  	return false
  }
  
  for (let key of keys1) {
  	if (!obj2.hasOwnProperty(key) || obj1[key] !== obj2[key]) {
    	return false
    }
  }
  
  return true
}
```

<a name="EFa5h"></a>
#### 判断当前环境
```javascript
const isPc = () => {
  const userAgentInfo = navigator.userAgent;
  const Agents = new Array(
    'Android',
    'iPhone',
    'SymbianOS',
    'Windows Phone',
    'iPad',
    'iPod',
  );
  let flag = true;
  for (let v = 0; v < Agents.length; v++) {
    if (userAgentInfo.indexOf(Agents[v]) > 0) {
      flag = false;
      break;
    }
  }
  return flag;
};
```

<a name="nwA1n"></a>
#### 格式化美元价格
```javascript
function formatAsUSD(aNumber) {
    return new Intl.NumberFormat("en-US",
        {
            style: "currency", currency: "USD",
            minimumFractionDigits: 2
        }).format(aNumber);
}
```

<a name="QMqvH"></a>
#### 获取Cookie
```javascript
const getCookie = (name) => document.cookie.match(`[;\s+]?${name}=([^;]*)`)?.pop();
```

<a name="sjVzN"></a>
#### every/some
```javascript
const every = (arr,fn) => {
    let result = true;
    for(const value of arr)
       result = result && fn(value)
    return result
}

const some = (arr,fn) => {
    let result = false;
    for(const value of arr)
       result = result || fn(value)
    return result
}

```

<a name="zNiBn"></a>
#### sortBy排序
```javascript
// 通过对象的property属性排序

const sortBy = (property) => {
    return (a,b) => {
        var result = (a[property] < b[property]) ? -1 : (a[property] > b[property]) ? 1 : 0;
        return result;
    }
}
```

<a name="pwMbY"></a>
#### unary方法
```javascript
// 只接收函数的第一个参数

const unary = (fn) =>
  fn.length === 1
    ? fn
    : (arg) => fn(arg) 
```

<a name="O1jM9"></a>
#### once函数
```javascript
// 函数只执行一次

const once = (fn) => {
  let done = false;
  
  // （exp1, exp2） 逗号操作符
  //  从左到右依次执行，返回最后一个exp2的值
  return function () {
    return done ? undefined : ((done = true), fn.apply(this, arguments))
  }
}
```

<a name="QNR6t"></a>
#### memoized缓存函数
```javascript
// 应用场景：斐波拉契数列计算优化，阶乘计算优化

const memoized = (fn) => {
  const lookupTable = {};
    
  return (arg) => lookupTable[arg] || (lookupTable[arg] = fn(arg));
}


function cached (fn) {
  var cache = Object.create(null);
  
  return (function cachedFn (str) {
    return cache[str] || (cache[str] = fn(str))
  })
}
```

<a name="bTcso"></a>
#### 自定义objectAssign
```javascript
function objectAssign() {
    var to = {};
    for (var i = 0; i < arguments.length; i += 1) {
      var from = arguments[i];
      var keys = Object.keys(from);
      for (var j = 0; j < keys.length; j += 1) {
        to[keys[j]] = from[keys[j]];
      }
    }
    return to;
  }
```

<a name="ZmQnI"></a>
#### 自定义数组方法
```javascript
const map = (array,fn) => {
  let results = [];
  for(const value of array)
      results.push(fn(value))

  return results;  
}


const filter = (array,fn) => {
  let results = [];
  for(const value of array)
     (fn(value)) ? results.push(value) : undefined

  return results;  
}


// 扁平化二维数组
const concatAll = (array,fn) => {
  let results = [];
  for(const value of array)
     // vaule数组的元素会逐个传递给push
     results.push.apply(results, value);

  return results;  
}


const reduce = (array,fn,initialValue) => {
  let accumlator;

  if(initialValue != undefined)
    accumlator = initialValue;
  else
    accumlator = array[0];

  if(initialValue === undefined)
    for(let i=1;i<array.length;i++)
      accumlator = fn(accumlator,array[i])
  else
    for(const value of array)
      accumlator = fn(accumlator,value)

  return [accumlator]	
}


// 合并两个给定的数组, 并执行fn函数逻辑
const zip = (leftArr,rightArr,fn) => {
  let index, results = [];

  for(index = 0;index < Math.min(leftArr.length, rightArr.length);index++)
    results.push(fn(leftArr[index],rightArr[index]));
  
  return results; 
}

zip([1,2,3],[4,5,6],(x,y) => x+y))
```
 
<a name="nIDVw"></a>
#### 柯里化
```javascript
const curry =(fn) => {
    if(typeof fn!=='function'){
        throw Error('No function provided');
    }

    return function curriedFn(...args){
      
      if(args.length < fn.length){
        return function(){
          return curriedFn.apply(null, args.concat([].slice.call(arguments)));
        };
      }
   
      return fn.apply(null, args);
    };
};
```

<a name="vji8M"></a>
#### 偏函数
```javascript
// 允许开发者，部分的应用函数参数
const partial = function (fn,...partialArgs){
  let args = partialArgs.slice(0);
  return function(...fullArguments) {
    let arg = 0;
    for (let i = 0; i < args.length && arg < fullArguments.length; i++) {
      if (args[i] === undefined) {
        args[i] = fullArguments[arg++];
        }
      }
      return fn.apply(this, args);
  };
};

/*
  JSON.stringify函数调用的最后两个参数总是相同的'null, 2', 应该partial函数
  可以移除样板代码
*/
let prettyPrintJson = partial(JSON.stringify,undefined,null,2);
let json = prettyPrintJson({foo: "bar", bar: "foo"});
console.log("JSON pretty print via partial", json);
```

<a name="dtAbx"></a>
#### compose函数
```javascript
const reduce = (array,fn,initialValue) => {
  let accumlator;

  if(initialValue != undefined) {
    accumlator = initialValue;
  } else {
    accumlator = array[0];
  }

  if(initialValue === undefined) {
    for(let i=1;i<array.length;i++)
      accumlator = fn(accumlator,array[i])
  } else {
    for(const value of array)
      accumlator = fn(accumlator,value)
  }
  return [accumlator]	
}


定义：接受一个函数的输出，并将其作为输入传递给另一个函数。

// 用c调用返回函数时，用输入c调用函数b, b的输出将作为a的输入
const compose = (a, b) => (c) => a(b(c));

// * compose函数会先执行b, 并将b的返回值作为参数传递给a. 函数调用的
方向是`从右至左`（先执行b, 再执行a）


// 组合多个函数，数据流向从右至左
const composeN = (...fns) =>
  (value) =>
    reduce(fns.reverse(),(acc, fn) => fn(acc), value);

// 管道连接多个函数，数据流向从左至右
const pipe = (...fns) =>
  (value) =>
    reduce(fns,(acc, fn) => fn(acc), value);
```
