# Gallery

## 摘要
本日重點主要是利用 `flex` 的特性及 `transition` 的動畫效果做出點擊後的效果呈現。
可參考[flex.io](https://www.flex.io/)。


## 內容

>作業一開始事呈現圖片為上倒下排序，所以第一步要做的事情是先把圖片依左右排序，並且等寬排列。

- 透過新增 `panels` 屬性`display:flex`首先可以達到左右排序的效果，`panel`為`panels` 的子元件，透過在`panel`新增 `flex:1`，可以讓子元件可以等寬填滿。

```css
.panels{
	...
	display: flex;

.panel {
	...
	flex:1;
}
```

>接著要處理字的效果問題，這邊我們可以先把字標註起來，讓之後的效果比較好觀察：

```css
.panel > * {
	...
    border: 1px solid red;
    }

```

>接下來要開始把字置中

- `justify-content:center` 可以讓字成水平置中，`align-items:center`可以讓字成垂直置中。宣告`display:flex`可以讓該container使用flex的方式呈現。所以以上項目都需放在`.panel`內。此時文字顯示應該是由左至右顯示，若要改成上到下的方式可以透過`flex-direction:column`的方式修改。此時並至中排列。

```css
.panel {
	...
	justify-content:center;
    align-items:center;
    display:flex;
}

```

>接下來要開始把字分三等份排好。

- 這個時候要操控`.panel > *`，並加入`flex:1 0 auto;`及上述的至中方式，讓文字在自己的flex內至中。此時應該是呈現文字分三等份排列，並且都是至中的狀態。

```css
.panel > * {
	...
	flex:1 0 auto;
	justify-content:center;
    align-items:center;
    display:flex;
}
```

>接下來要先處理動畫部分，首先先把字往上下移動。

- 先透過選取的方式取到上下的文字方塊，此時可以用`.panel > *:first-child`取第一個 `.panel > *:last-child`取最後一個，並透過`transform`的方法製造動畫效果，這邊使用的是`translateY(-100%)`向上移動，`translateY(100%)`向下移動。並且順便定義動畫啟動時的效果。`translateY(0)` ，透過新增`open-active`驅動，此時應該在該欄位屬性內加入`open-active`可以看到動畫效果。

```css
.panel > *:first-child {transform:translateY(-100%);}
.panel.open-active > *:first-child {transform:translateY(0);}
.panel > *:last-child {transform:translateY(100%);}
.panel.open-active > *:last-child {transform:translateY(0);}
```

>接下來要處理動畫效果：讓該項目使用`open`的時候可以讓圖片變大五倍。

- 先前有設定過每個`panel`的`flex :1`，所以此時我們只要修改被插入`open` 名稱的`panel`執行五倍的效果即可`flex:5`。而這些動畫效果都已經被標記在`.panel` 內的`transition`。

```css
.panel open {
	...
	flex:5;
}
```

> 最後我們要 Javascript 處理動畫觸發：當點擊後，該`panel`會圖片放大，接著文字會已動畫方式回覆。

- 首先先選取`.panel`的nodeList，並在每個nodeList放置監聽器，監聽效果為`click`後驅動`toggleOpen`放大圖片，放大圖片的方式是用`this.classList.toggle('open')`把`open`加到被選取的`panel`，接著等放大圖片的動畫過後透過`transitionend`驅動`toggleActive`呈現文字動畫效果，驅動後要先判定被監聽回傳的內容是不是包含`flex`名稱，依瀏覽器不同safari會回傳`flex`, 其他的則是`flex-flow`，最後判定後利用`this.classList.toggle('open-active')`直線文字效果。

```javascript
const panels = document.querySelectorAll('.panel');

function toggleOpen(){
	this.classList.toggle('open');
}

function toggleActive(){
	this.classList.toggle('open-active');
}


panels.forEach(panel => panel.addEventListener('click', toggleOpen));
panels.forEach(panel => panel.addEventListener('transitionend', toggleActive));
```

最後記得把文字框border消除，就完成今天的項目囉！