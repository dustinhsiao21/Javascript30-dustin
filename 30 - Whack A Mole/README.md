# Whack a Hole!

## 摘要

今天要實作的是打地鼠的遊戲，打地鼠的內容如下:

有六個洞會隨機出現地鼠，對著地鼠點擊即可得一分，遊戲時間10秒鐘。

## 內容

可以先規劃一下流程:

1. 設定地鼠出現的時間。
2. 設定地鼠隨機出現的位置。
3. 結合1.2 讓地鼠隨機出現。
4. 最後實作點擊地鼠得分。

>設定地鼠出現的時間。

- 可帶入兩個數字當作區間。
- `Math.round(arg)`:對參數做四捨五入。(`Math.floor()`為無條件進位)

```javascript
  function randomTime(min, max){
    return Math.round(Math.random() * (max-min) + min);
  }
```

>設定地鼠隨機出現的位置。

- 由於直接取值時會取到`All`，所以我們必須隨機產生一個0-5的數字，並用陣列的方式取值。
- 為避免有兩次重複出現，可以設定一個`const lastHole`，紀錄每次產生的`hole`是否相同，若相同則重新執行一次選擇。

```javascript
  	let lastHole;

	function randomHole(holes){
    // console.log(holes);
    const idx = Math.floor(Math.random() * holes.length);
    const hole = holes[idx];
    if(hole === lastHole){
      return randomHole(holes);
    }

    lastHole = hole;
    return hole;
  }
```

>結合1.2 讓地鼠隨機出現。

結合`randomTime(min, max)`, 及`randomHole(holes)`，讓地鼠隨機產生。

- `setTimeout(code, minisecs)`:等待`minisecs`的時間後，執行一次`code`(和`setInterval()`不同，`setInterval()`會不斷執行)。

- 設定`const timeUp`:`true`結束, `false`進行中。

- 在`randomTime()`後若時間還未到，執行`peep()`。

- `hole.up`:被註冊在css內

  - ```css
    .mole {
      background:url('mole.svg') bottom center no-repeat;
      background-size:60%;
      position: absolute;
      top: 100%;
      width: 100%;
      height: 100%;
      transition:all 0.4s;
    }

    .hole.up .mole {
      top:0;
    }
    ```

```javascript
let timeUp = false;

function peep(){
    const time = randomTime(20, 1000);
    const hole = randomHole(holes);
    hole.classList.add('up');
    setTimeout(()=>{
      hole.classList.remove('up');
      if (!timeUp) peep();
    }, time);
  }
```

>最後實作點擊地鼠得分。

- `const score`:用來計算分數。
- 綁擊點擊事件，利用回傳的事件(e)做操作。
- `e.isTrusted`:若事件是由使用者產生的為`true`,若事件物件是由程式碼所建立、修改，或是透過 [`EventTarget.dispatchEvent()`](https://developer.mozilla.org/zh-TW/docs/Web/API/EventTarget/dispatchEvent) 來觸發，則 `isTrusted` 值為 `false`。[可參考](https://developer.mozilla.org/zh-TW/docs/Web/API/Event/isTrusted) 

```javascript
let score = 0;

function bonk(e){
    if(!e.isTrusted) return
    score++;
    this.parentNode.classList.remove('up');
    scoreBoard.textContent = score;
  }

  moles.forEach(mole => mole.addEventListener('click', bonk));
```

> 今日練習就到這邊囉!也恭喜完成了最後一天的練習了!
>
> 附上完整程式碼

```javascript
<script>
  const holes = document.querySelectorAll('.hole');
  const scoreBoard = document.querySelector('.score');
  const moles = document.querySelectorAll('.mole');
  let lastHole;
  let timeUp = false;
  let score = 0;

  function randomTime(min, max){
    return Math.round(Math.random() * (max-min) + min);
  }

  function randomHole(holes){
    // console.log(holes);
    const idx = Math.floor(Math.random() * holes.length);
    const hole = holes[idx];
    if(hole === lastHole){
      return randomHole(holes);
    }

    lastHole = hole;
    return hole;
  }

  function peep(){
    const time = randomTime(20, 1000);
    const hole = randomHole(holes);
    hole.classList.add('up');
    setTimeout(()=>{
      hole.classList.remove('up');
      if (!timeUp) peep();
    }, time);
  }

  function startGame(){
    score = 0
    scoreBoard.textContent = score;
    timeUp = false;
    peep();
    setTimeout(()=> timeUp = true, 10000);
  }

  function bonk(e){
    if(!e.isTrusted) return
    score++;
    this.parentNode.classList.remove('up');
    scoreBoard.textContent = score;
  }

  moles.forEach(mole => mole.addEventListener('click', bonk));

</script>
```









