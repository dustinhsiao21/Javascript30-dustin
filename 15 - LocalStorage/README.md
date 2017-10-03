# LocalStorage and Event Delegate

## 摘要

今日主要介紹兩個部分:

- `LocalStorage`的使用。
- `Event Delegate`的介紹

## 內容

> 首先先來介紹LocalStorage

- `LocalStorage`:是一個在瀏覽器端作為`緩存`機制的存在。一般在瀏覽器端的儲存方式分為三種:`Cookie`, `LocalStorage`及`SessionStorage`。這邊以表格簡述一下其差異。可參考更多[Cookie vs LocalStorage vs SessionStorage](http://jerryzou.com/posts/cookie-and-web-storage/)

  | 特性          | Cookie                                   | LocalStorage               | SessionStorage             |
  | ----------- | ---------------------------------------- | -------------------------- | -------------------------- |
  | 生命週期        | 由Server端生成，可設置失效時間。若是browser生成，默認關閉browser後失效 | 除非被清楚，否則永久保存               | 當前有效，關閉頁面或瀏覽器刪除。           |
  | 大小          | 4KB                                      | 5MB                        | 5MB                        |
  | 與server通信狀況 | 每次送HTTP請求時皆會存在Header中。                   | 不參與Server通信                | 不參與Server通信                |
  | 易用性         | 需自行封裝，原生API不好處理                          | 原生API難處理，可在封裝成object及array | 原生API難處理，可在封裝成object及array |
  | 使用時機        | 用戶登入信息即可                                 | 可將購物車訊息從cookie移過來。         | 表單頁面處理。                    |

> 先實作輸入項目輸出在items內

- `e.preventDefault()`:此方法會取消當前DOM元素應該觸發的效果，如此例是取消`submit`的效果。
- 當物件的`key` 和 `value的變數名稱`相同時，可以使用ES6的方法，只寫變數名稱即可。

```javascript
  function addItem(e){
    e.preventDefault();
    const text = (this.querySelector('[name=item]')).value;
    const item = {
      text,
      done:false
    }
    item.push(item);
  }

  addItems.addEventListener('submit', addItem);
```

此時應該可以在`console`內輸入`items`看到輸入的內容。

> 接下來要把items輸出在列表內

- 這邊先創建一個方法`populateList`，用來處理插入項目。
- 應用`.map()`處理每一項的輸出，利用`join('')`，將陣列轉換為連續字串。
- `this.reset()`:用來清空輸入欄位。

```javascript
function addItem(e){
    ...
    items.push(item);
    populateList(items, itemsList);
    this.reset();
}

function populateList(plates = [], platesList){
  platesList.innerHTML = plates.map((plate, i)=>{
    return `
    	<li>
    		<input type="checkbox" data-index=${i} id="item${1}" ${plate.done ? 'checked' : ''}/> //若done為true則顯示checked，若無顯示空字串。
    		<label for "item${i}">${plate.text}</label>
    `;
  }).join('');
}
```

>最後要使用LocalStorage套入

- `LocalStorage.setItem('name', 'value')`:用來儲存。
- `LocalStorage.getItem('name', 'value')`:用來取值。
- 由於直接輸入陣列時，LocalStorage會直接輸入`{Object}`，所以我們在儲存時須使用`JSON.stringity`把物件轉為`string`儲存，並使用`JSON.parse()`取出為物件。

```javascript
const items = JSON.parse(localStorage.getItem('items')) || []; //修改items讀取方式
function addItem(e){
  ...
  populateList(items, itemsList);
  localStorage.setItem('items', JSON.stringify(items));
}
...
 populateList(items, itemsList);//記得要在最後插入這段，否則每次re-load之後還要按下submit才會出現資料!
```

