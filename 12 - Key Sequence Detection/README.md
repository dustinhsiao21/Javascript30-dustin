# Key Sequence Detection

## 摘要

以前一定曾有過要輸入一段密碼之後會出現特定的畫面，稱為Key Sequence，今日要做的是如何偵測Key Sequence並產生特殊畫面。

## 內容

- 利用一個陣列存鍵入的內容，另一個用來做儲存的內容。當兩個陣列吻合後即可產生特殊內容。

> 首先先定義一個鍵入內容，一個儲存密碼，並註冊一個監聽器。

- 監聽器用`window.addEventListener()`
- `keyup`事件:當用鍵盤輸入時啟動。
- `Array.prototyope.push`:可以把元素放進陣列內(`const`也可。)

```javascript
const pressed = [];
const secretCode = 'dustin';

window.addEventListener('keyup', (e) => {
  pressed.push(e.key);
  console.log(pressed);
})
```

此時應該可以在console.log看到輸入的內容。

>接下來限制pressed內字數要和secretCode一樣，並把舊的輸入彈出。

- `Array.prototype.splice`:(start, deleteCount, additem)。
  - `start`:開始改動的位置，若為負數則從最後開始。
  - `deleteCount`: 要刪除的數量。
  - `additem`:從起始位置開始，要增加的元素。
- 演算法設置:
  - 由於`start`為負數時會從最後一個開始算起的下一個計算，所以當`start`為`-1`時，起始位置是最後一個的下一個，此時不會有資料被刪除，當起始位置為-2時才會刪除最後一個；同理若要刪除第一個時，則要設置為`-n -1`。
  - 因為當`deleteCount`為負數時不會有資料被刪除。此例子要常駐維持刪減一個數量，所以長度可以設定為`pressed.length-secret.length`。當一開始keyup的東西不多時，為負數，不刪除資料。當`pressed.length`超過`secret.length`時才刪除資料。

```javascript
pressed.splice(-secretCode.length -1, pressed.length - secretCode.length);
```

> 最後是要判定當pressed和secretCode內容相同時會呈現特殊畫面。

- 這邊利用`Array.prototype.join()`方式把陣列改為字串，
- 接著利用`.includes()`方式比對是否。`Array.prototype.includes()`:includes()內的內容是否有在array內出現，有則回傳`true`。

```javascript
if (pressed.join('').includes(secretCode)){
  console.log('DING DIND');
  cornify_add();//以定義好的內容。
}
```

>測試看看吧!若輸入成功可以看到特殊畫面喔!

