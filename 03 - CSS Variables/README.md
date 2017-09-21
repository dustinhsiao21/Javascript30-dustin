# Update CSS variable with JS

## 摘要
本篇要做的事情是Javascript 和 CSS3 去改變 CSS的值，範例是改變了內邊距(padding), 模糊度(blur)及顏色，並同時對標題得字進行改變。

大致上的想法如下:

1. 在css中定義變數，並把變數關連到頁面。
2. 利用Javascript抓取改變的量(使用change監聽顏色改變及mouseover事件監聽range)，並更新CSS的變數值。

## 重點提醒

1. `type="range"`會呈現可左右移動的滑動桿。
```html
<input type="range" min="0" max="100" step="1" value="10">
```
2. `:root`是DOM元件的根元素，相當於`<html>`，一般會把CSS的變數聲明在內，CSS3原生的變數表示法: `--variable`;

```css
\\宣告方式
:root{
	--base: #ffc600;
    --blur: 10px;
    --spacing: 10px;
}
\\使用方式
span.hl{
	color: var(--base);
}
img{
	padding: var(--spacing);
    filter: blur(var(--blur));
    background: var(--base);
}
```

3. filter:blur()濾鏡使用，常見的還有blur(模糊度)opacity(透明度)。
4. NodeList vs Array:當使用`querySelectorAll('.controls input')`時會回傳一個NodeList, 可以使用`forEach` function，但是它不是一個Array，可以無法使用像是map, reduce等function。
```
<div class="controls">
	<input >
    <input >
    <input >
```

5. `this.dataset`:會出現該選取項目中所有`data-`的項目及值。舉例來說，如果要選取`data-sizing`的值，可以使用`this.dataset.sizing`，在範例中，`data-sizing`存放的是`px`，所以在取到目標的值之後，還需要加上`px`值才可以運作，不過顏色沒有單位，所以也可以為空，避免報錯。

```
function updateData(e){
    const suffix = this.dataset.sizing || ' ';
    document.documentElement.style.setProperty(`--${this.name}`,this.value + suffix);
}
```