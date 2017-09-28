# Custom Video Player

## 摘要

今天要練習的是如何製作一個客製化的撥放器。我們會把chrome預設的撥放器取消，然後放上客製化的撥放器。會學到關於撥放器的事件及屬性。

## 內容

> 首先我們先把每個設定好的class取出，方便之後事件處理。

```javascript
const player = document.querySelector('.player'); //最外層的div
const video = player.querySelector('.viewer'); //影片的div
const progress = player.querySelector('.progress'); //影片播放bar的外層div
const progressBar = player.querySelector('.progress__filled');//影片播放bar的div 
const toggle = player.querySelector('.toggle'); //播放按鈕的div
const skipButtons = player.querySelectorAll('[data-skip]'); //兩個`skip`的div
const ranges = player.querySelectorAll('.player__slider'); //聲音大小及撥放速度bar的div
```

> 接著我們要先設定撥放和暫停的控制。

- 當我們對`video` 及 `控制鈕(toggle)`按下時會撥放/暫停。影片是否在撥放中可以藉由方法`video.paused`判定，並可以驅動`play()or video[play]()`及`pause()or video[pause]()`方法操控影片。

```javascript
function togglePlay(){
	const method = video.paused ? 'play' : 'pause';
	video[method]();
}
video.addEventListener('click', togglePlay);
toggle.addEventListener('click', togglePlay);
```

> 接著我們要改變按鈕的樣式

- 當我們對影片播放`play()`或暫停`pause()`時會更改撥放鍵的樣式。更改樣式使用`toggle.textContent`更改內容。

```javascript
function updateButton(){
	const icon = this.paused ? '►' : '❚ ❚';
	toggle.textContent = icon;
}

video.addEventListener('play', updateButton);
video.addEventListener('pause', updateButton);
```

> 接著我們要控制skip

- `skip`共有兩個區域-10及+25，我們利用選取`data-xxx`當作標籤內的屬性。此時可以用`querySelectorAll([data-xxx])`選取，並逐一(`forEach`)增添事件`click`，觸發`skip`方法。而取資料時可以用`this.dataset.xxx`的方式取到該值。
- 由於取到的值是`string`，我們要用`parseFloat()`強制轉換成`number`。
- `video.currentTime`可以知道目前影片播放的時間。

```javascript
function skip(){
	const skip = this.dataset.skip;
	video.currentTime += parseFloat(this.dataset.skip);
}
skipButtons.forEach(button => button.addEventListener('click', skip));
```

> 接著我們要控制聲音及撥放速度。

- 利用`change`or`mouseover`去控制，並利用選取`this.name`的方式給值`this.value`。
- `video[volumn]`及`video[playbackRate]`可以分別設定聲音及撥放速度。
- `input type="range"`可以用來當作調整設定:
  - `min`最小值
  - `max`最大值
  - `step`最小移動值
  - `value`預設數值

```javascript
function handleRangeUpdate(){
	video[this.name] = this.value;
}
ranges.forEach(range => range.addEventListener('change', handleRangeUpdate));
ranges.forEach(range => range.addEventListener('mouseover', handleRangeUpdate));
```

> 接著我們要處理撥放bar的顯示。

- 我們先利用`video.currentTime`取當前值，並除以`video.duration`(全長)並乘上100並得知進度條的比例位置。
- 接著控制CSS的呈現，設定`progressBar.style.flexBasis = ${percent}%`;
- 控制的事件可以使用`timeUpdate`或是`progress`可以得到一樣的效果。

```javascript
function handleProgress(){
	const percent = (video.currentTime / video.duration) *100;
	progressBar.style.flexBasis = `${percent}%`;
}
video.addEventListener('timeupdate', handleProgress);
```

>最後處理拖拉撥放bar的效果。

- 利用傳遞事件值進來，利用`e.offsetX`取得在當下該div的x值，除以div的全長` progress.offsetWidth`可以得到百分比，乘以`video.duration`可以知道目前的撥放時間。並放到`video.currentTime`。
- 設定`mousedown = false`當作flag。當按下(`mousedown)`為`true`，放開時`(mouseup)`為`false`，在移動時(`mouseover`)判定若`mousedown`參數為`true`時執行事件(scrub)。

```javascript
function scrub(e) {
  const scrubTime = (e.offsetX / progress.offsetWidth) * video.duration;
  video.currentTime = scrubTime;
}
let mousedown = false;
progress.addEventListener('click', scrub);
progress.addEventListener('mousemove', (e) => mousedown && scrub(e));
progress.addEventListener('mousedown', () => mousedown = true);
progress.addEventListener('mouseup', () => mousedown = false);
```

> 今日的課程就到這邊結束拉!
>
> 不過老師有出個作業:請試試看做一個螢幕放大鍵，按下後會把撥放螢幕放大最大，請各位試試看囉!

