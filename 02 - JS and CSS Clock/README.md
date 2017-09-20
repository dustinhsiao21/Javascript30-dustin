# JS&CSS CLOCK

## 摘要

1. 橫線使用`transform:rotate(90deg)`會變成直線，預設情況下，會以中心點當作軸心旋轉(50%)，若要移動軸心則要使用`transform-origin:100%`(最右側)。
2. `transition-timing-function:ease`可當作動畫的呈現效果。
3. 設置每次執行時間`setInterval(setDate, 1000)`，設置每次執行時的內容。
4. 取時間使用`now = new Date()`, 可以取得的分秒時`now.getSeconds()`。

```
const secondHand = document.querySelector('.second-hand');
function serDate(){
	const new = new Date();
	const seconds = now.getSeconds();
	const secondsDegree = ((seconds / 60)*360)+90;
	secondHand.style.transform = `rotate(${secondsDegree}deg)`
}
```