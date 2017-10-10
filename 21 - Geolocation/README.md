# Geolocation

## 摘要

本日要介紹如何使用`Navigator.geolocation`的API去擷取當前的地理位置狀況，包含 指向 及 速度，詳情可以參考[Web API](https://developer.mozilla.org/zh-TW/docs/Web/API/Navigator/geolocation)。

另外此項目一樣要取得安全同源(secure origin)，所以一樣要用`https`的方式或是`localhost`。

建議使用模器裝置模擬手機設備或是直接用手機使用此功能。Mac用戶可以用[Xcode](https://developer.apple.com/xcode/)去設定模擬。

## 內容

通過調用`Navigator.geolocation`的方法去擷取資訊。

- `Navigator.geolocation.getPosition()`:用以一次性地取得當前的地理位置。
- `Navigator.geolocation.watchPosition(data, err)`:用以監控的方式連續取得當前地理位置。利用`data.coords.{property}`可以取得資訊。第二個參數若失敗會執行的`callback`錯誤訊息。
  - `accurency`":目前位置的精確度。
  - `heading`:目前位置指向。
  - `latitude`及`longitude`:經度及緯度。
  - `speed`:當前速度。
- 另外使用`element.style.transform = rotate()deg`的方式旋轉指針。

```javascript
  const arrow = document.querySelector('.arrow');
  const speed = document.querySelector('.speed-value');

  navigator.geolocation.watchPosition((data) => {
    console.log(data);
    speed.textContent = data.coords.speed;
    arrow.style.transform = `rotate(${data.coords.heading}deg)`;
  }, (err) => {
    console.error(err);
    alert('You should ACCESS the permission!')
  });
```

> 今天的練習就到這邊為止!

