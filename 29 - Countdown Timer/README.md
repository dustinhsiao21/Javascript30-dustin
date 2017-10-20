# Countdown Timer

## 摘要

今日要練習如何製作倒數計時，可點選已經設置好的時間及可自行輸入時間的功能。

## 內容

預計處理流程：

1. 處理設定時間函數。
2. 顯示倒數時間及顯示預計時間。
3. 設置按鈕及自行輸入欄位。
4. 處理重複時間問題bug 。

>處理設定時間函數。

- `Date.now()`:可以得知目前時間戳記。
- 取得目前時間後，加上輸入的時間，即是倒數後的時間。
- 設置`setInterval(code, minsecs)`:每間隔minsecs執行一次，當時間結束後`clearInterval(name)` 可結束。

```javascript
function timer(seconds){
	const now = Date.now();
	const then = now + seconds * 1000 //Date取得為minsec

	countdown = setInterval(()=>{
		const secondsLeft = Math.floor((then - Date.now()) / 1000);
		// check if we should stop it!
		if (secondsLeft < 0) {
			clearInterval(countdown);
			return;
		}
	}, 1000);
}
```

此時在console內輸入`timer(seconds)`應該可以看到倒數的狀態。

>顯示倒數時間及顯示預計時間。

- `new Date()`可新增一個Date物件，可以用的方法可參考[Date](https://www.w3schools.com/jsref/jsref_obj_date.asp)，這邊可以輸入`timestamp`的到屬於`timestamp` 的時間物件。
- `Date.getHours()`及`Date.getMinutes()` 可分別取得小時及分。
- ​

```javascript
function timer(seconds){
	const now = Date.now();
	const then = now + seconds * 1000 //Date取得為minsec
	displayTimeLeft(seconds);
	displayEndTime(then);

	countdown = setInterval(()=>{
		const secondsLeft = Math.floor((then - Date.now()) / 1000);
		// check if we should stop it!
		if (secondsLeft < 0) {
			clearInterval(countdown);
			return;
		}
		// display it
		displayTimeLeft(secondsLeft);
	}, 1000);
}

function displayTimeLeft(seconds){
	const minutes = Math.floor(seconds / 60);
	const reminderSeconds = seconds % 60;
	const display = `${minutes}:${remainderSeconds < 10 ? '0' : '' }${remainderSeconds}`;
	document.title = display;
	timerDisplay.textContent = display;
}

function displayEndTime(timestamp) {
	const end = new Date(timestamp);
	const hour = end.getHours();
	const adjustedHour = hour > 12 ? hour -12 : hour;
	const minutes = end.getMinutes();
	endTime.textContent = `Be Back At ${adjustedHour}:${minutes < 10 ? '0' : ''}${minutes}`;
}
```

此時在console輸入 `timer(10)`後應該可以看到倒數的畫面啦

>設置按鈕及自行輸入欄位

- 先用事件綁訂在按鈕上，並從`data-time`取值(this.dataset.time)，並用`parseInt()`強制轉換型別。
- 在submit後不要送出，可以用`e.preventDefault()`;處理。
- `this.reset(); 可以輸入欄位。

```javascript
function startTimer() {
	const seconds = parseInt(this.dataset.time);
	timer(seconds);
}

buttons.forEach(button => button.addEventListener('click', startTimer)); //按鈕
document.customForm.addEventListener('submit', function(e){ //表單輸入
	e.preventDefault();
	const mins = this.minutes.value;
	timer(mins *60);
	this.reset();
})
```

此時應該可以利用點選按鈕或輸入去決定要倒數的時間，但是會發現若同時按多個按鈕會重複倒數的情況，所以要在每次按下後取消前一個interval

>處理重複時間問題bug 。

```javascript
function timer(seconds){
	// clear any existing timers
	clearInterval(countdown);
	...
}
```

> 今日練習就到這邊結束。
>
> 附上今日程式碼

```javascript
let countdown;
const timerDisplay = document.querySelector('.display__time-left');
const endTime = document.querySelector('.display__end-time');
const buttons = document.querySelectorAll('[data-time]');

function timer(seconds){
	// clear any existing timers
	clearInterval(countdown);

	const now = Date.now();
	const then = now + seconds * 1000 //Date取得為minsec
	displayTimeLeft(seconds);
	displayEndTime(then);

	countdown = setInterval(()=>{
		const secondsLeft = Math.floor((then - Date.now()) / 1000);
		// check if we should stop it!
		if (secondsLeft < 0) {
			clearInterval(countdown);
			return;
		}
		// display it
		displayTimeLeft(secondsLeft);
	}, 1000);
}

function displayTimeLeft(seconds){
	const minutes = Math.floor(seconds / 60);
	const reminderSeconds = seconds % 60;
	const display = `${minutes}:${remainderSeconds < 10 ? '0' : '' }${remainderSeconds}`;
	document.title = display;
	timerDisplay.textContent = display;
}

function displayEndTime(timestamp) {
	const end = new Date(timestamp);
	const hour = end.getHours();
	const adjustedHour = hour > 12 ? hour -12 : hour;
	const minutes = end.getMinutes();
	endTime.textContent = `Be Back At ${adjustedHour}:${minutes < 10 ? '0' : ''}${minutes}`;
}

function startTimer() {
	const seconds = parseInt(this.dataset.time);
	timer(seconds);
}

buttons.forEach(button => button.addEventListener('click', startTimer));
document.customForm.addEventListener('submit', function(e){
	e.preventDefault();
	const mins = this.minutes.value;
	timer(mins *60);
	this.reset();
})
```







