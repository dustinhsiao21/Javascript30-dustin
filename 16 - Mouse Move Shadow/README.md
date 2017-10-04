# Mouse Move Shadow

## 摘要

今日要實作的是滑鼠在圖片的四周移動，會產生不一樣的效果，會應用到`offset`的操作。

## 內容

>首先先建立監聽事件及選取元素

```javascript
  const hero = document.querySelector('.hero');
  const text = hero.querySelector('h1');

  function shadow(e){
	console.log(e)
  }

  hero.addEventListener('mousemove', shadow);
```

此時應該可以在`console`看到回傳的內容。

> 定義關注的參數。

- 這邊會用到`ES6`的語法:

  ```javascript
  const xxx = a.aaa;
  const yyy = a.bbb;
  //可簡化為
  const { aaa:xxx, bbb:yyy} = a
  ```

- `hero.offsetWidth`:顯示`.hero`原本的高度(不會因為滑鼠修正)。

- `e.offsetX 及 e.offsetY`才會是滑鼠移動時的位置。

```javascript
function shadow(e){
	const { offsetWidth : width, offsetHeight : height} = hero;
    let {offsetX : x, offsetY : y } = e;
    console.log(x, y)
}
```

但是這邊出現一個問題：滑鼠移動時座標顯示都正常，但是當移到`h1`(const text)的區域時，會只計算`h1`內的座標，而移出`h1`的範圍時又會恢復正常。所以我們在計算`h1`內的座標時須加上`e.target.offsetLeft`及`e.target.offsetTop`

- `this vs e.target`
  - `this(e.currentTarget)`在javascript中，若`DOM element`被事件綁定，`this`代表的是該`Dom element`，儘管事件也有綁定到子元素，`this`還是代表被綁定的那個元素。
  - `e.target`則是代表被觸發的那個`Dom element`。若事件被綁定在巢狀結構的父元件，當觸發的點是子元件時，會回傳子元件的內容。

```javascript
  function shadow(e){
    const { offsetWidth : width, offsetHeight : height} = hero;
    let {offsetX : x, offsetY : y } = e;

    if (this !== e.target){
      x = x + e.target.offsetLeft;
      y = y + e.target.offsetTop;
    }
  }
```

>最後要處理動畫顯示。

- 先定義一個`const walk`，作用是在為整個`hero`提供一個動畫顯示的基準點。
  - 以`const walk = 100`為例，我們會先把 (實際的位置/全部的長度)*100 - (100 / 2)，這樣會以中心點為(0.0)，左上及右下分別為(-50, -50)及(50,50)。
- 要製作`const text`的動畫顯示這邊用`text.style.textShadow`:參數分別代表:`x, y, blur, color(rgba)`。
- `Math.round()`:數字顯示到個位數。

```javascript
function shadow(e){
    const { offsetWidth : width, offsetHeight : height} = hero;
    let {offsetX : x, offsetY : y } = e;

    if (this !== e.target){
      x = x + e.target.offsetLeft;
      y = y + e.target.offsetTop;
    }

    const xWalk = Math.round(( x / width *walk) - (walk / 2));
    const yWalk = Math.round(( y / height *walk) - (walk / 2));

    text.style.textShadow = `
      ${xWalk}px ${yWalk}px 0 rgba(255,0,255,0.7),
      ${xWalk * -1}px ${yWalk}px 0 rgba(0,255,255,0.7),
      ${yWalk}px ${xWalk * -1}px 0 rgba(0,255,0,0.7),
      ${yWalk * -1}px ${xWalk}px 0 rgba(0,0,255,0.7)
    `;
  }
```

> 今天的項目比較簡單，主要是注意this&e.target的選擇，及注意座標位置的參數計算。



