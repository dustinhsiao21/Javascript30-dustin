# Drum kit

### 摘要

1. 利用輸入鍵盤的案件事件`keydown`觸發功能，利用`keyCode`取值，並將`keyCode`的值對應到`data-key`，接者對相對應的`audioaudio[data-key="${e.keyCode}"]`放出音樂`audio.play()`。

```
window.addEventListener('keydown', playsound);
```

2. 可以把音樂撥放時間重置 `audio.currentTime = num`
3. 利用`selector.classList.add('playing')`，可將選定的標籤加入class的後綴字；同理用`selector.classList.remove('playing')`可移除選定的class後綴字。
4. 事件`transitionend`使用方式:當使用過`transition`後執行callback內容。

### CSS概念

1. flex基本用法:
	- align-items : center; //垂直置中
	- justify-content:center; //水平置中
2. transition動畫效果用法: property duration timing-function delay;ex:transition:all 0.07s ease
	- property:有width, color...
	- timing function:
		- ease cubic-bezier(0.25, 0.1, 0.25, 1.0)
		- liner cubic-bezier(0.0, 0.0, 1.0, 1.0)
		- ease-in cubic-bezier(0.42, 0.0, 1.0, 1.0)
		- ease-out cubic-bezier(0.0, 0.0, 0.58, 1.0)
		- ease-in-out cubic-bezier(0.42, 0.0, 0.58, 1.0)
3. 可以直接註冊標籤，並在css內敘述標籤功能。
4. 背景圖片

```
html{
	background:url(http://...)
    background-size:cover
}
```