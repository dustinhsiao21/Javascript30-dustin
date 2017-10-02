# Javascript Reference VS copy

## 摘要

本日要介紹Javascript付值時，何時是用copy，何時是用reference。

## 內容

- 對變數來說:無論型別是`integer`, `String`及`boolen`都是**copy**。

```javascript
let age = 100;
let age2 = age;
console.log(age, age2); // 100, 100
age = 200
console.log(age, age2); // 100, 200
```

- 對陣列及物件來說，都是**Reference**

```javascript
// Let's say we have an array
const players = ['Wes', 'Sarah', 'Ryan', 'Poppy'];

// and we want to make a copy of it.
const team = players

console.log(players, team); //['Wes', 'Sarah', 'Ryan', 'Poppy'], ['Wes', 'Sarah', 'Ryan', 'Poppy']

// You might think we can just do something like this:
team[3] = 'Lux';

console.log(players, team); //['Wes', 'Sarah', 'Ryan', 'Lux'], ['Wes', 'Sarah', 'Ryan', 'Lux']
```

以下列出幾個可以**copy**陣列的方法。

- 利用`array.prototype.slice`:slice都會返回一個新的陣列(return a new array)。

  補充說明 splice vs slice

  ```javascript
  slice(begin, end):

  ['a', 'b', 'c', 'd'].slice(1, 3) //['b'. 'c'] 是切除後回傳新的陣列。end不包含該項。

  splice(index, howmany, additem):

  x = ['a', 'b', 'c', 'd'];

  y = x.splice(1, 3)

  console.log(x) //['a']

  console.log(y) //['b', 'c', 'd']
  ```

- `concat`:join array。可以創建一個空的然後`join`目標。return a new array。

- 利用`ES6 Spread`。

- `Array.from()`:可以傳入`陣列`(array-like)or`String`轉換成陣列

  ```javascript
  const bar = ["b", "a", "r"];
  Array.from(bar);
  // ["b", "a", "r"]

  Array.from('foo')
  //['f', 'o', 'o'];
  ```

  ​

```javascript
const team2 = players.slice();
const team3 = [].concat(players);
const team4 = [...players];
const team5 = Array.from(players);
```

物件也是一樣的情況:**Reference**

> 解決辦法

- `Object.assign({}, object)`，但是assign是只有one Level，若是有多層object則要利用其他方式。
- `obj3 = JSON.parse(JSON.stringify(obj));`可以完整地複製物件。
  - `JSON.stringigy`:物件轉String。
  - `JSON.parse`:String轉物件。

```javascript
const obj1 = {
      name: 'Wes',
      age: 100,
      social: {
        twitter: '@wesbos',
        facebook: 'wesbos.developer'
      }
    };
obj2 = Object.assign({}, obj1, {age:12}); //one level copy
obj3 = JSON.parse(JSON.stringify(obj1)); //full copy
```

簡單來說:就是變數付值都會是**copy**，陣列及物件時則會是**Reference**。

這概念很簡單，但是在實作上常常會忽略，要熟記喔!

> 今日課程到這邊結束囉!

