# WebCam Fun

## 摘要

今日要使用原生的Javascript來驅動`webcam`來紀錄影像資訊，並輸出到`canvas`上，並用`canvas`對圖像進行拍照及濾鏡處理。

## 內容

> 在驅動webcam前我們須先建立一個localhost服務器。

我們要調用的`getUserMedia()`api需要在`安全連線(secure origin)`下使用。安全連線主要是包含要有`HTTPS`, `Localhost`等。[可參考文件](https://www.chromium.org/Home/chromium-security/prefer-secure-origins-for-powerful-new-features)

而在文件中已經有提供了架設server的方法。我們會利用`npm`來安裝`browser-sync`一個可以在本地端架設server的套件。首先我們先確定本機端已經安裝了node.js，若還沒有，則請到[node.js](https://nodejs.org/en/)下載。

下載後可以執行`npm`的相關指令，可以在[node.js](https://nodejs.org/en/)上查找，`npm`執行的內容主要是依照`package.json`的腳本產生，你可以看到在資料夾內有個`package.json`檔案。

- `devDependencies`是會下載的套件。並會處理相依關係。
- `scripts`是可執行的指令。用法:`npm run {scripts}`，如`npm run start`。

所以請在command端執行以下步驟

```javascript
npm install //安裝package.json內的套件
npm run start //開啟browser-sync 套件。系統會自動啟動。可以點及ctrl+右鍵點及連結。
```

>接者介紹一下預設好的配置

- `video`:原始鏡頭的位置
- `canvas`:擷取鏡頭的內容渲染在畫布上，也是特效會呈現的位置。
- `strip`:產生圖檔的位置。
- `snap`:點擊照相時產生的音效(click)。

> 首先先取得webcam的使用。

- 用`navigator.mediaDevices.getUserMedia()`取得攝影鏡頭的權限。會返回一個`promise`對象。若是點選允許，則會回傳一個`resolved`狀態並回調函數為一個`MediaStream`物件。若是點選拒絕(deny)，會回傳一個`Reject`狀態，且以`PermissionDeniedError`的回調函數。所以會用`.then`處理成功訊息，用`catch`處理失敗訊息。
- `window.URL.createObjectURL(localMediaStream)`:用於建立一個帶有URL的`DOMString`，代表參數值中傳入的物件。該URL的生命週期與創造它的window一致。新的物件代表所指定的`File`物件或是`Blob`物件

```javascript
function getVideo(){
	navigator.mediaDevices.getUserMedia({ video : true, audio : false})
		.then(localMediaStream => {
			console.log(localMediaStream);
			video.src = window.URL.createObjectURL(localMediaStream);
			video.play();
		}).catch(err => {
			console.log(`You must deny the webcam permission`, err);
		});
}

getVideo();
```

此時應該可以在瀏覽器看到權限請求，點擊允許後可以在右上角看到攝像頭。

> 接者要把擷取的資料放到Canvas畫布上

- 先固定畫布的大小。
- `setInterval(function(){}, minSecs)`:設定運行頻率。單位為mini secs。
- `ctx.drawImage()`:能將畫面擷取下來。`drawImage(img, x, y, width, heigth)`
- `ctx.getImageData()`:反回一個ImageData對象，用來描述canvas區域內的像素數據。`getImageData(x, y, width, height)`。
- `ctx.putImageData()`:將數據從已有的ImageData對象繪製到圖像的方法。`putImageData(img, x, y)`。
- 綁定事件`canplay`:當攝影頭準備使用時觸發(已完成buffer程序可隨時開始)。

```javascript
function paintToCanvas(){
	const width = video.videoWidth;
	const height = video.videoHeight;
	canvas.width = width;
	canvas.height = height;

	return setInterval(() => {
		ctx.drawImage(video, 0, 0, width, height);
		//take the pixels out
		let pixels = ctx.getImageData(0, 0, width, height);

		//put them back
		ctx.putImageData(pixels, 0, 0);
	}, 16);
}
getVideo();
video.addEventListener('canplay', paintToCanvas);
```

在擷取檔案`ctx.getImageData`及放回檔案`ctx.putImageData(pixels, 0, 0);`之間我們可以對`ImageData`做些自訂濾鏡效果。ImageData包含了大量的數據，其中包括了rbga值(red, blue, green, alpha)。a用來顯示不透明度(opacity)，1為飽滿0為透明。

> 製作拍照功能。

拍照分兩部分處理:

1. 按下後產生聲音。

```javascript
function takePhoto(){
	snap.currentTime = 0;
  	snap.play();   
}
```

2. 擷取畫面。

- `canvas.toDataURL('image/jpeg')`:方法返回包括圖片展示的URL, type可在參數內自訂。[詳細可參考文檔](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLCanvasElement/toDataURL)
- 我們可以創建一個a元素，並設置href值，插入文檔中，可以看到截圖效果。
- 若想要設置下載功能:`link.setAttribute('download', 'fileName');`
- `strip.insertBefore`:插入功能。`strip.insertBefore(data, location)`。
  - location使用:`firstChild`, `lastChild`, `childNode[key]`。

```javascript
function takePhoto() {
  // played the sound
  snap.currentTime = 0;
  snap.play();

  // take the data out of the canvas
  const data = canvas.toDataURL('image/jpeg');
  const link = document.createElement('a');
  link.href = data;
  link.setAttribute('download', 'handsome');
  link.innerHTML = `<img src="${data}" alt="Handsome Man" />`;
  strip.insertBefore(link, strip.firsChild);
}
```

此時應該可以時做拍照，及下載功能。

> 最後要來處理特效功能。

效果處理方式其實只是把RGB的像素加工，輸入的`pixels.data[]`陣列內分別以`red(data[i]), green(data[i+1]), blue(data[i+2]), alpha(data[i+3])`排列。故在取資料時須每個`rgba`組一起處理`(i+=4)`。

##### redEffect()

 - 把red的項目加深處理，藍綠的項目-50及減半。

```javascript
function redEffect(pixels) {
  for(let i = 0; i < pixels.data.length; i+=4) {
    pixels.data[i + 0] = pixels.data[i + 0] + 200; // RED
    pixels.data[i + 1] = pixels.data[i + 1] - 50; // GREEN
    pixels.data[i + 2] = pixels.data[i + 2] * 0.5; // Blue
  }
  return pixels;
}
```

並在`paintToCanvas()`內取`ImageData`之後加入使效果即可。

```
return setInterval(() => {
		ctx.drawImage(video, 0, 0, width, height);
		//take the pixels out
		let pixels = ctx.getImageData(0, 0, width, height);

		//mess with them
		 pixels = redEffect(pixels);

		//put them back
		ctx.putImageData(pixels, 0, 0);
	}, 16);
```

##### rgbSplit()

- 同理redEffect()，這邊是把rgb像素分離。

```javascript
function rgbSplit(pixels) {
  for(let i = 0; i < pixels.data.length; i+=4) {
    pixels.data[i - 150] = pixels.data[i + 0]; // RED
    pixels.data[i + 500] = pixels.data[i + 1]; // GREEN
    pixels.data[i - 550] = pixels.data[i + 2]; // Blue
  }
  return pixels;
}
```

同樣的需要在`paintToCanvas()`內取`ImageData`之後加入使效果即可。

最後是greenScreen，在實作時須先解除html內的註解。可以看到六個`range` bar。分別代表rgb的數值。

```javascript
function greenScreen(pixels) {
  const levels = {};

  document.querySelectorAll('.rgb input').forEach((input) => {
    levels[input.name] = input.value;
  });

  for (i = 0; i < pixels.data.length; i = i + 4) {
    red = pixels.data[i + 0];
    green = pixels.data[i + 1];
    blue = pixels.data[i + 2];
    alpha = pixels.data[i + 3];

    if (red >= levels.rmin
      && green >= levels.gmin
      && blue >= levels.bmin) {
      // take it out!
      pixels.data[i + 3] = 0;
    }
  }
  return pixels;
}
```

> 今日內容比較特別，可能需要多花時間消化吸收。大家加油~
>
> 一樣附上完整程式碼內容

```javascript
const video = document.querySelector('.player');
const canvas = document.querySelector('.photo');
const ctx = canvas.getContext('2d');
const strip = document.querySelector('.strip');
const snap = document.querySelector('.snap');

function getVideo(){
	navigator.mediaDevices.getUserMedia({ video : true, audio : false})
		.then(localMediaStream => {
			console.log(localMediaStream);
			video.src = window.URL.createObjectURL(localMediaStream);
			video.play();
		}).catch(err => {
			console.log(`You must deny the webcam permission`, err);
		});
}

function paintToCanvas(){
	const width = video.videoWidth;
	const height = video.videoHeight;
	canvas.width = width;
	canvas.height = height;

	return setInterval(() => {
		ctx.drawImage(video, 0, 0, width, height);
		//take the pixels out
		let pixels = ctx.getImageData(0, 0, width, height);

		// mess with them
		// pixels = redEffect(pixels);

		// pixels = rgbSplit(pixels);

		pixels = greenScreen(pixels);
		//put them back
		ctx.putImageData(pixels, 0, 0);
	}, 16);
}

function takePhoto() {
  // played the sound
  snap.currentTime = 0;
  snap.play();

  // take the data out of the canvas
  const data = canvas.toDataURL('image/jpeg');
  // console.log(data);
  const link = document.createElement('a');
  link.href = data;
  link.setAttribute('download', 'handsome');
  link.innerHTML = `<img src="${data}" alt="Handsome Man" />`;
  strip.insertBefore(link, strip.firsChild);
}

function redEffect(pixels) {
  for(let i = 0; i < pixels.data.length; i+=4) {
    pixels.data[i + 0] = pixels.data[i + 0] + 200; // RED
    pixels.data[i + 1] = pixels.data[i + 1] - 50; // GREEN
    pixels.data[i + 2] = pixels.data[i + 2] * 0.5; // Blue
  }
  return pixels;
}

function rgbSplit(pixels) {
  for(let i = 0; i < pixels.data.length; i+=4) {
    pixels.data[i - 150] = pixels.data[i + 0]; // RED
    pixels.data[i + 500] = pixels.data[i + 1]; // GREEN
    pixels.data[i - 550] = pixels.data[i + 2]; // Blue
  }
  return pixels;
}

function greenScreen(pixels) {
  const levels = {};

  document.querySelectorAll('.rgb input').forEach((input) => {
    levels[input.name] = input.value;
  });

  for (i = 0; i < pixels.data.length; i = i + 4) {
    red = pixels.data[i + 0];
    green = pixels.data[i + 1];
    blue = pixels.data[i + 2];
    alpha = pixels.data[i + 3];

    if (red >= levels.rmin
      && green >= levels.gmin
      && blue >= levels.bmin) {
      // take it out!
      pixels.data[i + 3] = 0;
    }
  }

  return pixels;
}

getVideo();

video.addEventListener('canplay', paintToCanvas);
```

