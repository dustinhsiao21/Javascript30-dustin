# Dev tools 

## 摘要

本篇主要介紹幾個常用的`dev tool`，可以增加您在開發`javascript`的除錯速度。

## 內容

> 輸出內容

- `console.log()`:顯示在console內。
  - 也可用替代字符的方式`console.log('hello %s', 'world');`
  - 或是加入css`console.log('%c hello world', 'font-size:50px');`
- 除了一般的顯示外，還提供幾種顯示的警示:
  - `console.warn('hello world')`:警示符號
  - `console.error('hello world')`:錯誤符號
  - `console.info('hello world')`:資訊符號

> 可以當作測試使用

- `console.assert()`若第一參數為`false`，則會輸出第二參數內容。`console.assert( 1 ===2 , 'This is false');`。

> 清除畫面

- `console.clear()`:清除console畫面。

> 查看被選取dom元素的資料內容

- `console.dir()`:可以查看被選取的dom元素屬性。

> Group資料。

```javascript
   dogs.forEach(dog => {
      console.groupCollapsed(`${dog.name}`);
      console.log(`This is ${dog.name}`);
      console.log(`${dog.name} is ${dog.age} years old`);
      console.log(`${dog.name} is ${dog.age * 7} dog years old`);
      console.groupEnd(`${dog.name}`);
    });
```

> 計算次數

- `console.count()`:可以計算參數內出現的次數。

> 計算執行時間

- `console.time()`:可以計算`console.time('hello world')`到`console.timeEnd('hello world')`的執行時間。

> 把陣列輸出成table型式

- `console.table(dog)`:把array用表格型式輸出。