# 10 Hold Shift and Check Checkboxes

## 摘要

本篇主要是要實作透過點擊checkbox後按下shift鍵之後一次選取多個checkbox的功能。



## 內容

> 首先先選取目標物並加入監聽事件`click`，並先定義 click後要施作的方法。

```javascript
const checkboxes = document.querySelectorAll('inbox input[type="checkbox"]');

checkboxes.forEach(chechbox.addEventListener('click' , handleCheck));
```

>撰寫handleCheck 方法。

- 先來談一下邏輯的設置方式：`shift`的使用方式應該是在點擊`checkbox`後再按下`shift`，可以由`shiftKey`判定是否按下，當兩者皆啟動後，再點擊另外一個 `checkbox`才會出現效果，這邊可以先定義最後一個點擊的是`lastChecked`，而這邊用`inBetween`參數當做在兩個`checkbox` 之間的內容，若在兩個之間為`true`, 其他則會`false`。所以最後只要判斷`inBetween`為`true`時打勾即可。

```javascript
let lastChecked;

function handleChecj(e){
  let inBetween = false;
  if (e.shiftKey || this.checked){
    checkboxes.forEach(checkbox => {
      if (checkbox === this || checkbox === lastChecked){
        inBetween = !inBetween;
      }
      if (inBetween){
        checkbox.checked = true;
      }
    });
  }
  lastChecked = this
}
```



> 本篇原則上到這邊就結束了，本篇的項目沒有運用到新的方法，只有邏輯方面需要構思一下。

