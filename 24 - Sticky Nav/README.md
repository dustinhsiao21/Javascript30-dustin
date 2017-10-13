# Sticky Nav

## 摘要

今日要介紹的是如何實作當scroll超過Nav時，把Nav 固定在上方的效果。

## 內容

以下是主要流程：

1. 選取要比較的數值：scrollY vs topOfNav
2. 比對數值，並修改Css效果。
3. 針對jerky jump效果進行處理。
4. 加入css的動畫效果修正。

> 選取要比較的數值：scrollY vs topOfNav

- `window.scrollY`:已經scroll的距離。
- `element.offsetTop`:元素到頂端(window)的距離。

```javascript
  const nav = document.querySelector('#main'); //選取nav
  const topOfNav = nav.offsetTop; // 設定nav到window的距離

  function fixNav(){
    console.log(topOfNav, window.scrollY);
  }
  window.addEventListener('scroll', fixNav); //綁定事件scroll
```

此時可以觀察`topOfNav`及`window.scrollY`的變化。

`topOfNav`會固定不變，`window.scrollY` 會隨scroll變化。



> 比對數值，並修改Css

修改`fixNav function`，若高度超過，則在`body`新增`fixed-nav` 的class，並在css  內增加效果，反之則移除。

```javascript
  function fixNav(){
    // console.log(topOfNav, window.scrollY);
    if (window.scrollY >=  topOfNav){
      document.body.classList.add('fixed-nav');
    }else{
      document.body.classList.remove('fixed-nav');
    }
  }
```

```css
body.fixed-nav nav {
  position: fixed;
  box-shadow: 0 5px 0 rgba(0,0,0,0.1);
}
```

此時應該可以發現當scroll超過nav時將被固定住。

> 針對jerky jump效果進行處理。

這邊會出現一個問題，當nav被固定住的瞬間，因為我們是對nav 下fixed ，所以nav會跳脫出原本的文檔，會造成讓下方的資料產生往上跳動(jerky jump)的效果，所以在跳動前，我們需要在文檔內加入一個空白，讓文檔不要往上跳躍。

```javascript
  function fixNav(){
    // console.log(topOfNav, window.scrollY);
    if (window.scrollY >=  topOfNav){
      document.body.style.paddingTop = nav.offsetHeight + 'px';
      document.body.classList.add('fixed-nav');
    }else{
      document.body.classList.remove('fixed-nav');
      document.body.style.paddingTop = 0;
    }
  }
```

此時可以發現跳動的效果消失了(被padding取代)。

>加入css的動畫效果修正。

 最後加入動畫效果：

```css
// 讓fixed-nav 時 logo跳出，在li.logo內已經定義好動畫的時間(transition:all 0.2s)
.fixed-nav li.logo { 
  max-width: 500px;
}
//讓fixed-nav 產生時，放大文本內容，從0.98->1
body.fixed-nav .site-wrap {
  transform: scale(1);
}
```

> 今天的項目就到這邊為止。
>
> 附上完整程式碼：

```javascript
<script>
  const nav = document.querySelector('#main');
  const topOfNav = nav.offsetTop;

  function fixNav(){
    // console.log(topOfNav, window.scrollY);
    if (window.scrollY >=  topOfNav){
      document.body.style.paddingTop = nav.offsetHeight + 'px';
      document.body.classList.add('fixed-nav');
    }else{
      document.body.classList.remove('fixed-nav');
      document.body.style.paddingTop = 0;
    }
  }

  window.addEventListener('scroll', fixNav);

</script>
```

```css
body.fixed-nav .site-wrap {
  transform: scale(1);
}

body.fixed-nav nav {
  position: fixed;
  box-shadow: 0 5px 0 rgba(0,0,0,0.1);
}

body.fixed-nav li.logo {
  max-width: 500px;
}
```

