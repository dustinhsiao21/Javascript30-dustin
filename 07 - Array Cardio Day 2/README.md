# Array Cardio Day2

## 摘要
本篇延續Day4 的陣列常使用方法做介紹。本篇包含`some()`，`every()`，`find()`，`findIndex()`，利用也提到了關於`splice()`, `slice()`, `...`的應用。

## 內容
- `some()`:  `Array.prototype.some()`，只要一個條件符合就回傳`true`。`(new Date).getFullYear()`可以取得當前日期的年份。

>題目：查詢陣列內是否有人成年

```javascript
    const isAdult = people.some(person => ((new Date).getFullYear()) - person.year >=19 );
    console.log(isAdult);
```
- `every()`:  `Array.prototype.every()`，要每一個都符合條件才回傳`true`。`

>題目：查詢陣列內是否皆為成年。

```javascript
    const allAdults = people.every(person => ((new Date).getFullYear()) - person.year >=19 );
    console.log(allAdults);
```
- `find()`:有點類似`filter()`，但是`filter`會回傳全部符合的項目，`find()`則回傳單一項目。

>題目：找陣列id等於823423，並回傳該元素。

```javacript
    const comment = comments.find(comment => comment.id === 823423);
    console.log(comment);
```
- `findIndex()`:和`find()`功能一樣，只是會查找`index`。

>題目：找陣列id等於823423，並回傳index

```javascript
	const index = comments.find(comment => comment.id === 823423);
```
刪除元素可以使用

- `splice(index, num)`，第一個參數是要刪除的index起點, 第二個參數是要刪除的數量，第三個參數之後是要新加入的內容。會回傳刪除後的陣列。
- `slice(index, index)`，第一個參數為起點，第二個參數為終點（不包含終點），若第二個參數不填預設為最後。回傳一個array object。若是利用拆分的方式，掠過`index`不處理。可以達到`splice()`的效果。
- `...`spread operator(ES6功能)，可以將陣列轉換成單一數組，或將單一數組轉換成陣列。下面的例子因為連續使用`comments.slice()`，所以會讓數值變成`[Array[], Array[]]`，為了打破第二層的array，需使用`...`，把結構轉變成`[{}, {}, {}, {}, ...]`。

>題目：刪除該index元素。

```javascript
	comments.splice(index, 1);
	//or 用slice處理。
	const newComments = [
      ...comments.slice(0, index),
      ...comments.slice(index + 1)
    ];
	console.table(comments);
```