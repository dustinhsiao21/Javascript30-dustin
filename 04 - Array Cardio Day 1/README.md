# Array Cardio Day1

## 摘要
本篇主要介紹幾個JS array的使用方式。包含`filter()`，`map()`，`sort()`，`reduce()`。

## 內容
- `filter()`:  用來過濾物件條件中的選項，並收集回傳`true`的陣列。

```Javascript
const fifteen = inventors.filter(inventor => (inventor.year >= 1500 && inventor.year < 1600));
console.table(fifteen)//會以table的形式顯示。
```

- `map()`:依條件組合物件中的內容，並回傳陣列。

```Javascript
const fullNames = inventors.map(inventor => inventor.first+ '' + inventor.last);
```

- `sort()` :排序，若比較a.b  要若`a< b`，a排在b前面則回傳小於0的值(一般為 -1)，若`a>b`，則a在b後面，則回傳大於0的值(1)

```javascript
const ordered = inventors.sort((a, b) => a.year > b.year ? 1 : -1); //排序出生日期

const oldest = inventors.sort((a, b) => { //排序年齡，由大到小
	const lastGuy = a.passed - a.year;
	const nextGuy = b.passed - b.year;
    return lastGuy > nextGuy  ? -1 : 1 ;
})
```

- `reduce()` :  遞減，常用於累加數值，第一個arg為存放數值的地方，第二個為陣列名稱。需要帶入一個callback 起始數值。

```javascript
const totalYears = inventors.reduce((total, inventor) => { //加總全部數字
	return total + (inventor.passed - inventor.year)
}, 0);
```

- `map()` + `filter`應用:`querySelectorAll('')`取得的值是NodeList，如果想用陣列的方法需要利用`Array.from()`轉為陣列，才可以使用陣列提供的方法。選取網頁中含有de的的街道，使用`.includes('de')`

```javascript
const category = document.querySelector('.mw-category');
const links = Array.from(category.querySelectorAll('a'));
const de = links
				.map(link => link.textcontent) //取得連結的文字
        		.filter(streetName => streetName.includes('de')); //過濾文字
```

- `sort()`練習:對人名排序。會使用到`split()`方法，作用是依據給的內容拆分string成array。

```javascript
const alpha = people.sort((lastOne, nextOne) => {
	const [aLast, aFirst] = lsatOne.split(', ');
    const [bLast, bFirst] = nextOne.split(', ');
    return aLast > bLast ? 1:-1;
})
```

- `reduce()` 練習，計算陣列內重複的個數。若陣列內index沒有該項目，則新增為零，並增加項目內數量。重點在於後面的`{}`，代表回傳的是一個物件。

```javascript
const transportation = data.reduce((obj, item)=>{
	if(!obj[item]){
    	obj[item] = 0;
    }
    obj[item]++;
    return obj;
}, {});
```