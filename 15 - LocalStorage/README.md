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
    		<label for="item${i}">${plate.text}</label>
		</li>
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

目前應該可以看到輸入項目皆顯示出來囉!也可以檢查瀏覽器的LocalStorage是否有存值。

> 接下來要介紹Event Delegate

- `Event Delegate`:在一般時候，使用`event binding`即可達到每一個要求。`Event binding`主要是綁定特定DOM元素後透過註冊Event觸發DOM function達到效果。而Event Delegate則是綁定DOM的父元件而非子元件，讓子元件在更新後也可以透過父元件操作。
- 最簡單的例子則是先用`Event binding`綁定子元件後新增元件，此時新增的元件並不會有已經綁定的事件。此時若透過`Event Delegate`綁定父元件，子元件也會被父元件綁定的事件觸發，事件會上傳給父元件，父元件會帶著子元件一同返回事件。
- `toggleDone()`則是要利用此一特性，用`click`事件綁定`itemsList`(.plates)，當子元件('input')被`click`後會觸發toggleDone，此時印出`e.target`會發現返回`input`及`label`兩個元素。此時我們僅需要`input`元素，篩選`if (!e.target.matches('input')) return;`後把點擊的項目儲存後拿來改變`done`的值，並存入`LocalStorage`。

```javascript
  function toggleDone(e) {
    if (!e.target.matches('input')) return; // skip this unless it's an input
    const el = e.target;
    const index = el.dataset.index;
    items[index].done = !items[index].done;
    localStorage.setItem('items', JSON.stringify(items));
    populateList(items, itemsList);
  }
```

> 到這邊就完成今天的項目了!