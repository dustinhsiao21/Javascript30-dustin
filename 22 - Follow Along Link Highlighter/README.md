# Follow Along Link Highlight

## 摘要

今日要練習的是動畫效果，當游標移往含有`a`的element時，加入`span`，並在`span`在加入style用css做出反白的動畫效果。

## 內容

今日的運作流程:

1. 建立監控項目`(mouseenter)`。
2. 監控項目加入動畫效果。
3. 修正動畫效果。

> 建立監控項目`(mouseenter)`。

- `mouseenter`:當滑鼠移往該項目時驅動。
  - `mouseover` vs `mouseenter`:[詳見比較](http://api.jquery.com/mouseover/)
    - `mouseover`:當項目的子項目被指到時也會驅動。
    - `mouseenter`:只會受當下的項目(bound element)影響。

```javascript
const triggers = documentSelectorAll('a');
const highlight = document.createElement('span');
highlight.classList.add('highlight');
document.body.appendChild(highlight);

function highlightLink(){
  console.log(this);
}
triggers.forEach(a => a.addEventListener('mouseenter', highlightLink));
```

此時用瀏覽器開啟後在`body`的最底部應該可以看到`<span class="highlight"></span>`以及當游標移往`a`的項目時在console可以看到該項目。

> 監控項目加入動畫效果

- `this.getBoundingClientRect()`回傳`DOMRect物件`可以取得當前項目的位置資料。[可參考](https://developer.mozilla.org/en-US/docs/Web/API/DOMRect)
  - botton:項目底部到當前視窗上緣的距離。(=y or y+height)
  - height:項目本身的高度。( = botton - top)
  - left:項目左邊到視窗當前最左的距離。(=x or x+width)
  - right:項目右邊到視窗最左的距離。(=x or x+width)
  - top:項目頂部到到視窗當前上緣的距離。(=y or y+height)
  - width:項目本身寬度。( = right - left)
  - x:該項目在視窗中的x距離
  - y:該項目在視窗中的y距離
- `translate(x, y)`:從原本項目移動x,y距離。

```javascript
    function highlightLink(){
      // console.log(this);
      const linkCoords = this.getBoundingClientRect();
      // console.log(linkCoords);

      highlight.style.width = `${linkCoords.width}px`;// 在span內加入style.width
      highlight.style.height = `${linkCoords.height}px`;// 在span內加入style.height
      highlight.style.transform = `translate(${linkCoords.left}px, ${linkCoords.top}px)`;// 在span內加入style.transform效果。translate(x, y);
    }
```

此時應該可以看到效果了!

但是會發現當視窗往下移時顯示位置會無法對應!原因是因為使用`getBoundingClientRect()`取得是當前視窗的{相對距離}，我們還需要加上window本身的{絕對距離}。所以我們要家`DOMRect.left + window.scrollX`及`DOMRect.left + window.scrollY`搭配使用。

- `window.scrollY`:視窗已經被移動過的上緣距離。
- `window.scrollX`:視窗已經被移動過的左緣距離。

```javascript
    function highlightLink(){
      // console.log(this);
      const linkCoords = this.getBoundingClientRect();
      console.log(linkCoords);
      const coords = {
        width : linkCoords.width,
        height : linkCoords.height,
        top : linkCoords.top + window.scrollY,
        left : linkCoords.left + window.scrollX
      };

      highlight.style.width = `${coords.width}px`;
      highlight.style.height = `${coords.height}px`;
      highlight.style.transform = `translate(${coords.left}px, ${coords.top}px)`;
    }
```

> 今日項目就到今天為止囉!
>
> 另外附上完整原始碼

```javascript
  <script>
    const triggers = document.querySelectorAll('a');
    const highlight = document.createElement('span');
    highlight.classList.add('highlight');
    document.body.appendChild(highlight);

    function highlightLink(){
      // console.log(this);
      const linkCoords = this.getBoundingClientRect();
      console.log(linkCoords);
      const coords = {
        width : linkCoords.width,
        height : linkCoords.height,
        top : linkCoords.top + window.scrollY,
        left : linkCoords.left + window.scrollX
      };

      highlight.style.width = `${coords.width}px`;
      highlight.style.height = `${coords.height}px`;
      highlight.style.transform = `translate(${coords.left}px, ${coords.top}px)`;
    }

    triggers.forEach(a => a.addEventListener('mouseover', highlightLink));
  </script>
```

