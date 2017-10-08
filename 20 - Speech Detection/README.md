# Speech Detection

## 摘要

今日要介紹如何使用browser內建的**語音轉換API**`web speech api`。須注意和昨日相同，需要同意授權使用麥克風才能使用API，同理也需要建置本地端伺服器。

## 內容

**記得先架設本地端伺服器。**

`npm install`, `npm run start`。

> 首先我們先建立一個語音識別的物件，並賦值。

- `window.SpeechRecognition`:驅動語音轉換的API。
- `window.webkitSpeechRecognition`:Firefox用語音轉換API。
- `recognition.interimResults = true`:控制語音辨識期間是否返回，若為`true`會一直返回，若`SpeechRecognitionResult.isFinal`為`true`時，即結束當前對話。
- `recognition.lang = 'en-US';`可以設定辨識語言。繁體中文:`zh-tw`。

```javascript
  window.SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;

  const recognition = new SpeechRecognition();
  recognition.interimResult = true;
  recognition.lang = 'en-US';
```

> 添加輸出元素到頁面上，並加入監聽事件result。

- `.appendChild()`:添加元素。
- `recognition.start()`:開始監聽。

```javascript
  let p = document.createElement('p');
  const words = document.querySelector('.words');
  words.appendChild(p);

  recognition.addEventListener('result', e =>{
    console.log(e);
  });

  recognition.start();
```

此時對著麥克風說話應該可以在`console`看到回傳的事件`SpeechRecognitionEvent`。

打開事件後可以發現`results[0][transcript]`內看到轉換的內容。

> 接著我們要編輯回傳的事件。

- 取得`results`的內容。並重新排列成字串。
- 接著若語音暫停，則會另外創造一個段落。語音暫停可以用`e.result[0].isFianl=true`得知。
- 若暫停之後則須重啟，須增加監聽事件`  recognition.addEventListener('end', recognition.start);`並重新啟動。

```javascript
  recognition.addEventListener('result', e =>{
    const transcript = Array.from(e,results)
                        .map(result => result[0])
                        .map(result => result.transcript)
                        .join('');

    if (e.results[0].isFinal) {
        p = document.createElement('p');
        words.appendChild(p);
      }
  });

  recognition.addEventListener('end', recognition.start);
```

內容到這邊就差不多結束了。另外wesbos另外增加了一個功能，若提到`poop, poo, shit, dump`則會自動轉換成圖樣。

```javascript
      const poopScript = transcript.replace(/poop|poo|shit|dump/gi, '💩');
      p.textContent = poopScript;
```

> 今日項目就到這邊為止。以下是完整程式碼

```javascript
<script>
  window.SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;

  const recognition = new SpeechRecognition();
  recognition.interimResult = true;
  recognition.lang = 'en-US';

  let p = document.createElement('p');
  const words = document.querySelector('.words');
  words.appendChild(p);

  recognition.addEventListener('result', e =>{
    const transcript = Array.from(e,results)
                        .map(result => result[0])
                        .map(result => result.transcript)
                        .join('');

    const poopScript = transcript.replace(/poop|poo|shit|dump/gi, '💩');
    p.textContent = poopScript;

    if (e.results[0].isFinal) {
        p = document.createElement('p');
        words.appendChild(p);
      }
  });

  recognition.addEventListener('end', recognition.start);

  recognition.start();

</script>
```

