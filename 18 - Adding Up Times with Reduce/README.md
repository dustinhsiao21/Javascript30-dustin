# Adding Up Time With Reduce

## 摘要

今日是透過計算複數影片的長度來複習`reduce`及`map`的應用。

## 內容

>利用Map的方式選取時間後，再利用map的方式把時間計算成秒，接者利用reduce把全部加總，並切分成小時:分:秒。

- 記得當使用`document.querySelectorAll('')`時，取到的內容是`NodeList`，無法直接使用`Array.prototype`的方法，儘可以使用`forEach()`。
- 把`NodeList`轉成`Array`的方法有2:
  - `...document.querySelectorAll('')`:使用spread operator
  - `Array.from(document.querySelectorAll())`
- `String.prototype.split('arg')`:依照給的參數切割字串。
- `Number.parseFloat`:把字串(String)型別的數字轉成數字(Int)型別。
- `[].map(function (x){return parseFloat(x)})`可縮寫成`[].map(parseFloat)`
- `Math.floor()`:把參數的數字捨去小數點。
- `Array.prototype.reduce(arg1, arg2)`:`arg1`是要加總的結果，`arg2`是要加總的對象。

```javascript
  const timeNodes = Array.from(document.querySelectorAll('[data-time]'));
  const seconds = timeNodes
                  .map(node => node.dataset.time) //取得時間
                  .map(timeCode => { //分割時間6:30, 轉換成數字型別後加總。
                    const [mins, secs] = timeCode.split(':').map(parseFloat);
                    return mins * 60 + secs;
                  }).reduce((total, vidSeconds) => total + vidSeconds);
  let secondsLeft = seconds; //這邊要拆解成小時:分:秒

  const hours = Math.floor(secondsLeft/3600);
  secondsLeft = secondsLeft % 3600;

  const mins = Math.floor(secondsLeft/60);
  secondsLeft = secondsLeft % 60;

  console.log(hours, mins, secondsLeft);
```

> 今天的練習就到這邊為止了!Wesbos有出另外一個作業，用reduce做完整個功能!請大家試試看囉!

