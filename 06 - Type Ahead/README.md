#  AJAX Type Ahead

## 摘要
本篇主要介紹 `Fetch api`用 ajax的方式來取資料(城市名稱)，並依使用者輸入字符的不同查找不同的資料，會使用到正規表達式來處理字串。
[fetch api](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

## 內容
- `fetch()api`:是標準的web api，不需要透過引入則可直接使用，不過因為是新的api，會有瀏覽器相容的問題，詳見[MDN](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)。`Fetch API`有許多優點，其中一個是實作了`Promise`的語法結構，讓使用者可以更有效的解析(resolve)回傳的資料。

- `Promise`: `Promise`是一個`非同步(async)操作執行後的結果`。當`Promise`初始話時，狀態為`等待中(pending)`，當執行async任務後，會回傳結果，無論成功(fulfilled)或是拒絕(rejected)皆會回傳，且不會在改變回傳的內容，所以每次執行時都會回傳新的`Promise`物件。

- `Response`:當`Promise`被`解析(resolve)`後會回傳`Response`物件，可以直接以`.then()`方式進行串接解析，且能使用`Response`提供的`json()`方法取得資料。 

>利用fetch取得資料

```javascript
const endpoint = 'https://gist.githubusercontent.com/Miserlou/c5cd8364bf9b2420bb29/raw/2bf258763cdddd704f8ffd3ea9a3e81d25e2c6f6/cities.json';
const cities = [];
fetch(endpoint)
  .then(blob => blob.json())
  .then(data => cities.push(...data)); //因為cities為const，無法直接賦值，需以Array.protorype.push 把資料丟進去。
```

##### 補充資料 async/await
- `async/await`:當一個function 被放上 `async`的前綴字(prefix)後，等於宣告一個非同步函式。代表方法內可以使用`await`關鍵字，當`await`被執行時，會暫停該`async function`的執行，並等待`await function`的回傳後才繼續執行。若回傳失敗(rejected)則拋出例外，並附上拒絕理由

```javascript
async function foo(param){
	var aa = await bar(); //等待bar執行並回傳，此時先暫停foo 的執行，收到回傳後才console.log(aa);
	
	console.log(aa);
}

```

>接下來要處理輸入特定文字後取得特定資料。

- `RegExp()`:用來做正規表達試的參數， `g`代表global,`i`代表insensitive,不受大小寫影響。 

```javascript
function findMatches(wordToMatch, cities){
  return cities.filter(place => {
    const regex = new RegExp(wordToMatch, 'gi');
    return place.city.match(regex) || place.state.match(regex)
  });
}
```

到這邊就確定可以透過`findMatches()`取到特定的資料了。

> 接下來要把資料依照查找的字串render出來。

- 使先對輸入的自框建立`keyup`及`change`監聽事件，並觸發`displayMatehes`的查找事件。

```javascript
function displayMatches(){

}

const searchInput = document.querySelector('.search');
const suggestions = document.querySelector('.suggestions');

searchInput.addEventListener('change', displayMatches);
searchInput.addEventListener('keyup', displayMatches);
```

> 定義查找事件`displayMatches`的內容。

- 首先先取得資料，並把資料重新`map`，並把查詢的字用css方式顯示出來。
- `join()`:把`arrayObject`中的所有元素放入一個字串中。
- 如果是要return `html`的語法要用` `` `包裹，中間要傳遞的參數可以用`${value}`，帶入。

```javascript
function displayMatches(){
  const matchArray = findMatches(this.value, cities);
  const html = matchArray.map(place => {
      const regex = new RegExp(this.value, 'gi');
      const cityName = place.city.replace(regex, `<span class="hl">${this.value}</span>`);
      const stateName = place.state.replace(regex, `<span class="hl">${this.value}</span>`);
      return `
		<li>
		<span class="name">${cityName}, ${stateName}</span>
		<span class="population">${place.population}</span>
		</li>
	`;
  }).join('');
  suggestions.innerHTML = html;
}
```

> 最後再處理人數的顯示處理問題。

- 用`numberWithCommas`處理正規表達試的問題。

```javascript
function numberWithCommas(x) {
  return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
}

function displayMatches(){
  ...
  <span class="population">${numberWithCommas(place.population)}</span>
}
```

> 今日項目完成！

