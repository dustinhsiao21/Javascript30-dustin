# Video speed controller

## 摘要

本日要實作的是製作控制撥放速度的bar，及套用在video上。

## 內容

今日流程:

1. 製作撥放速度的bar
2. 將數值套用在video上。

> 製作撥放速度的bar

製作bar 分成幾個步驟

- 選取bar的元素，並添加事件。

```javascript
	const speed = document.querySelector('.speed');
	const bar = speed.querySelector('.speed-bar');
	
	function handleMove(e){
		console.log(e);
	}
	speed.addEventListener('mousemove', handleMove);
```

可以透過`mousemove`知道目前位置。

- `e.pageY`:目前滑鼠在Y的位置。
- `this.offsetTop`:元素在上方的位置。
- `this.offsetHeight`:元素本身高度。

透過計算可以取得bar的百分比長度。

透過設定`min`,及`max`相減後乘上百分比可以得到數值。此數值就是撥放速度的值。

```javascript
	function handleMove(e){
		const y = e.pageY - this.offsetTop;
		console.log(this.offsetHeight);
		const percent = y / this.offsetHeight;
		const min = 0.4;
		const max = 4;
		const height = Math.round(percent * 100) + '%';
		const playbackRate = percent * ( max - min) + min;
		bar.style.height = height;
		bar.textContent = playbackRate.toFixed(2) + 'x';
	}
```

> 最後再套用在撥放速度上

- `video.playbackRate`:可以設定撥放速度。

```javascript
	const video = document.querySelector('.flex');
	video.playbackRate = playbackRate;
```

>今日練習就到這邊結束
>
>附上完整程式碼

```javascript
<script>
	const speed = document.querySelector('.speed');
	const bar = speed.querySelector('.speed-bar');
	const video = document.querySelector('.flex');

	function handleMove(e){
		const y = e.pageY - this.offsetTop;
		console.log(this.offsetHeight);
		const percent = y / this.offsetHeight;
		const min = 0.4;
		const max = 4;
		const height = Math.round(percent * 100) + '%';
		const playbackRate = percent * ( max - min) + min;
		bar.style.height = height;
		bar.textContent = playbackRate.toFixed(2) + 'x';
		video.playbackRate = playbackRate;
	}
	speed.addEventListener('mousemove', handleMove);
</script>
```

