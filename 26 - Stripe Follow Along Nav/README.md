# Stripe Follow Along Nav

## 摘要

今天要實作的是製作當滑鼠在NAV橫移時產生的動畫內容

## 內容

今日實作主要分成幾個部分:

1. 釐清要選取的`div`內容，放置事件監聽。
2. 針對`callback`內容加入css效果，包括顯示及移出。
3. 處理快速變動會有殘影的小bug
4. 針對`callback`內容處理外框的位置設定。
5. 針對加入項目造成的跑版進行處理。

> 釐清要選取的`div`內容，放置事件監聽。

首先先釐清幾個項目:

- `<nav class="top">`:是整個`nav div`的項目，包含了很多子項目，如`<div class="dropdownBackground">`，`<ul class="cool">`
- `<div class="dropdownBackground">`:包含箭頭的對話視窗css
- `<ul class="cool">`:包含全部`li`指向個別的資訊。

首先我們先針對這些項目定義，接著對個別資訊(`li`)放置監聽器,   綁定事件`mouseenter`, `mouseleave`。

```javascript
  const nav = document.querySelector('.top');
  const triggers = document.querySelectorAll('.cool > li');
  const background = document.querySelector('dropdownBackground');

  function handleEnter(){
    console.log('Enter!');
  }

  function handleLeave(){
    console.log('Leave!');
  }

  triggers.forEach(trigger => trigger.addEventListener('mouseenter', handleEnter));
  triggers.forEach(trigger => trigger.addEventListener('mouseleave', handleLeave));
```

現在應該可以在console看到移入移出產生的結果。

> 針對`callback`內容處理css效果。

在`mouseenter`後，我們需要製作相關的動畫效果，這邊先定義三個class

- `<li class="trigger-enter">`:由於`li`的下一層`<div class="dropdown">`的`dropdown` 的`css display`為`none`, 這邊可以控制為`block`。
- `<li class="trigger-enter active">`:由於`li`的下一層`<div class="dropdown">`的`dropdown` 的`css opacity`為`0`, 這邊可以控制為`1`，讓資料顯示出來。
- `<div class="dropdownBackground open">`:原先設定`dropdownBackground `的`opacity:0`，控制為`1`，讓資料顯示出來

```css
  .trigger-enter .dropdown {
    display: block;
  }

  .trigger-enter-active .dropdown {
    opacity: 1;
  }

  .dropdownBackground.open {
    opacity: 1;
  }
```

```javascript
  function handleEnter(){
    // console.log('Enter!');
    this.classList.add('trigger-enter');
    setTimeout(()=>this.classList.add('trigger-enter-active'), 150);
    background.classList.add('open');
  }

  function handleLeave(){
    // console.log('Leave!');
    this.classList.remove('trigger-enter', 'trigger-enter-active');
    background.classList.remove('open');
  }
```

此時應該可以看到移動到項目時出現的動畫效果，不過如果當快速移動時，可能會產生資料重複出現的小bug，這邊我們可以在setTimeout()內處理:當`trigger-enter`存在時才進行。

>處理快速變動會有殘影的小bug

- 這邊我們使用`element.classList.contains('className')`來判定是否包含`class`

```javascript
  setTimeout(()=>this.classList.contains('trigger-enter') && this.classList.add('trigger-enter-active'), 150);
```

不過接著發現箭頭對話框的位置都在左上角出現，我們接下來要把它移到正確的位置。

>針對`callback`內容處理外框的位置設定。

- 前幾堂課有提過`element.getBoundingClientRect()`可以知道目前的`element`位置，所以我們可以透過這個去取得內容位置。
- 透過`element.style.setProperty('property', value)`去修改箭頭對化框的大小(`width, height`)，若是要修改對話框的位置(`top, left`)，則要修改`transform:translate(left, top)`。

```javascript
  function handleEnter(){
    // console.log('Enter!');
    this.classList.add('trigger-enter');
    setTimeout(()=>this.classList.add('trigger-enter-active'), 150);
    background.classList.add('open');

    const dropdown = this.querySelector('.dropdown');
    const dropdownCoords = dropdown.getBoundingClientRect();

    const coords = {
      height : dropdownCoords.height,
      width : dropdownCoords.width,
      top : dropdownCoords.top,
      left : dropdownCoords.left,
    }

    background.style.setProperty('width', `${coords.width}px`);
    background.style.setProperty('height', `${coords.height}px`);
    background.style.setProperty('transform', `translate(${coords.left}px, ${coords.top}px)`);
  }
```

這樣就可以看到對話框跟著資訊移動的動畫，但是還有個問題:因為上方有加入了`h2`的標籤，所以造成整個內容被往下擠的狀況，所以我們在判斷時還需要留意`nav`的位置，去處理相對位置。

>針對加入項目造成的跑版進行處理。

```javascript
function handleEnter(){
  ...
      const dropdown = this.querySelector('.dropdown');
    const dropdownCoords = dropdown.getBoundingClientRect();
    const navCoords = nav.getBoundingClientRect();

    const coords = {
      height : dropdownCoords.height,
      width : dropdownCoords.width,
      top : dropdownCoords.top - navCoords.top,
      left : dropdownCoords.left - navCoords.left,
    }
    ...
}

```

> 今天的練習就到這邊了
>
> 附上完整的程式碼

```javascript
<script>
  const nav = document.querySelector('.top');
  const triggers = document.querySelectorAll('.cool > li');
  const background = document.querySelector('.dropdownBackground');

  function handleEnter(){
    // console.log('Enter!');
    this.classList.add('trigger-enter');
    setTimeout(()=>this.classList.add('trigger-enter-active'), 150);
    background.classList.add('open');

    const dropdown = this.querySelector('.dropdown');
    const dropdownCoords = dropdown.getBoundingClientRect();
    const navCoords = nav.getBoundingClientRect();

    const coords = {
      height : dropdownCoords.height,
      width : dropdownCoords.width,
      top : dropdownCoords.top - navCoords.top,
      left : dropdownCoords.left - navCoords.left,
    }

    background.style.setProperty('width', `${coords.width}px`);
    background.style.setProperty('height', `${coords.height}px`);
    background.style.setProperty('transform', `translate(${coords.left}px, ${coords.top}px)`);
  }

  function handleLeave(){
    // console.log('Leave!');
    this.classList.remove('trigger-enter', 'trigger-enter-active');
    background.classList.remove('open');
  }

  triggers.forEach(trigger => trigger.addEventListener('mouseenter', handleEnter));
  triggers.forEach(trigger => trigger.addEventListener('mouseleave', handleLeave));
</script>
```





