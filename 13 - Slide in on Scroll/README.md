# Slide in on Scroll

## 摘要

今日要介紹照片的移入移出效果製作。當視窗移到照片的區域以動畫方式出現。

## 內容

> 首先先定義要選取的物件，並設定監聽事件。

- `Scroll`監聽事件:當移動右方scroll bar 時觸發，一般是鎖定在`window.addEventListener()`但是由於此事件的是發頻率會過多，所以一般會加以時間限制。這邊使用的是lodash的方法`debounce`，並將監聽設為20minisec

```javascript
const sliderImages = document.querySelectorAll('.slide-in');
function checkslide(){
  sliderImages.forEach(sliderImage => {
    
  })
}

window.addEventListener('scroll', debounce(checkSlide));
```

>接著要設置讀取照片位置的方法。

- `window.scroll`:滑動時的上緣。
- `window.innerHeight`:視窗的上下範圍。故`window.scroll+window.innerHeight`等於滑動時的下緣。
- `sliderImage.height`:圖片本身的上下長度。
- `slideInAt=window.scroll+window.innerHeight - sliderImage.height/2`代表滑下來時到圖片一半的全長度。
- ``

