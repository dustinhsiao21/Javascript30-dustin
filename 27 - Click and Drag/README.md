# Click and Drag

## 摘要

今天要練習的是如何實作滑鼠點擊後`(mousedown)`，左右橫移移動內容的效果。

## 內容

首先規劃整個流程:

1. 先定義監聽事件(`mousedown, mouseleave, mouseup, mousemove`)，及啟動參數。
2. 處理相對位置問題。

> 先定義監聽事件(`mousedown, mouseleave, mouseup, mousemove`)，及啟動參數。

- `startX`:啟動位置。
- `scrollLeft`:已經被移動過的距離。
- `.items .active`:會有放大效果`scale(0.98)->scale(1)`。

```javascript
    const slider = document.querySelector('.items');
    let isDown = false;
    let startX;
    let scrollLeft;
    
    slider.addEventListener('mousedown', (e) => {
        isDown = true;
        slider.classList.add('active');
    });
    slider.addEventListener('mouseleave', () => {
        isDown = false;
        slider.classList.remove('active');
    });

    slider.addEventListener('mouseup', () => {
        isDown = false;
        slider.classList.remove('active');
    });

    slider.addEventListener('mousemove', (e) => {
        if(!isDown) return; //stop the fn running
    });
```

>處理相對位置問題。

- `element.scrollLeft`:已經被移動過(scroll)的y位置。
- `MouseEvent.pageX`:在當前點擊位置對應瀏覽器左邊框的距離。
- `Element.scrollLeft`:當前容器對應瀏覽器左邊框的距離。

這邊要集中在`mousedown` 及`mousemove`處理。

- `mousedown`紀錄點擊時在瀏覽器的位置(起始位置)，及已經被移動過多少`scrollLeft`。
- `mousemove`處理點擊後移動的距離(`walk`)，接著把移動過的距離(`scrollLeft`)減去這次移動的距離(`walk`)就會變成整個`mousemove`的`scrollLeft`的距離。

```javascript
    slider.addEventListener('mousedown', (e) => {
        isDown = true;
        slider.classList.add('active');
        startX = e.pageX - slider.offsetLeft; //當前滑鼠點擊位置-當前滑鼠點擊容器到瀏覽器左邊位置。
        scrollLeft = slider.scrollLeft; //點擊當下已經被移動的scrollLeft距離。
    });
    
    slider.addEventListener('mousemove', (e) => {
        if(!isDown) return; //stop the fn running
        e.preventDefault();
        const x = e.pageX - slider.offsetLeft; //當前滑鼠點擊位置-當前滑鼠點擊容器到瀏覽器左邊位置，因為是mousemove，所以會隨著滑鼠移動時修正。
        const walk = (x - startX) * 3; //移動的距離。
        slider.scrollLeft = scrollLeft - walk; //當左拉時，實際上會是往前滑動，所以會用扣的，並回傳到［被滑動到的距離］更新。
    });
```

>今天的練習就到這邊。
>
>附上這次的程式碼

```javascript
<script>
    const slider = document.querySelector('.items');
    let isDown = false;
    let startX;
    let scrollLeft;

    slider.addEventListener('mousedown', (e) => {
        isDown = true;
        slider.classList.add('active');
        startX = e.pageX - slider.offsetLeft;
        scrollLeft = slider.scrollLeft;
    });

    slider.addEventListener('mouseleave', () => {
        isDown = false;
        slider.classList.remove('active');
    });

    slider.addEventListener('mouseup', () => {
        isDown = false;
        slider.classList.remove('active');
    });

    slider.addEventListener('mousemove', (e) => {
        if(!isDown) return; //stop the fn running
        e.preventDefault();
        const x = e.pageX - slider.offsetLeft;
        const walk = (x - startX);
        slider.scrollLeft = scrollLeft - walk;
    });

</script>
```



