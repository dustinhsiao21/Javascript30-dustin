# Sort Without Articles

# 摘要

今日要透過編排文章排序來複習`sort`, `map`, `join` 及`replace`, `trim`的使用。

## 內容

> 首先先把文章編排。

- `Array.prototype.sort(function (a, b){})`: 放入a, b，若a>b， a要往前擺則回傳+1, 往後擺則回傳-1。
- `Array.prototype.map(funciton(name) => ...)`:重組每一個陣列。
- `Array.prototype.join('')`:合併陣列內的內容。若直接用`toString()`會留下逗號。

```javascript
const sortedBands = bands.sort((a, b) => a > b ? 1: -1);

document.querySelector('#bands').innerHTML = 
  sortedBands.map(band => `<li>${band}</li>`)
  .join('');
```

>影片內多了一個排序說明：排序時不要參考a, an 及the

- 利用正規表達式排序，為了避免修改陣列內容，我們僅在比較時排序。不改變陣列本身。
- `String.prototype.replace`:替換掉`字串`內容，通常會用正規表達式。
- `String.prototype.trim()`:會消除`字串`左右`空白`。

```javascript
function strip(bandName){
  return bandName.replace(/^(a |an |the)/i, '').trim();
}
...strip(a)> strip(b)? 1 : -1;
```

>今日課程就到這邊結束囉!以下是完整程式碼

```javascript
<script>
const bands = ['The Plot in You', 'The Devil Wears Prada', 'Pierce the Veil', 'Norma Jean', 'The Bled', 'Say Anything', 'The Midway State', 'We Came as Romans', 'Counterparts', 'Oh, Sleeper', 'A Skylit Drive', 'Anywhere But Here', 'An Old Dog'];

function strip(bandName) {
  return bandName.replace(/^(a |the |an )/i, '').trim();
}

const sortedBands = bands.sort((a, b) => strip(a) > strip(b) ? 1: -1);

document.querySelector('#bands').innerHTML =
  sortedBands.map(band => `<li>${band}</li>`)
  .join('');

</script>
```







