# Speech Synthesis

## 摘要

今日要使用`SpeechSynthesisUtterance`物件，透過可以驅動瀏覽器說話的API`speechSynthesis`，來實作類似google小姐的說話(utterance)功能，包含講話速度(rate)及音頻(pitch)的高低。

## 內容

 處理流程：

1. 匯入API 提供的語言選單。
2. 設定所選語言為說話的語言。
3. 監聽rate 及 pitch 更動，並切換重新說話。 
4. 透過start 及 stop 按鈕開啟及結束說話。



>匯入API 提供的語言選單。

- 首先先建立`let utterance = new SpeechSynthesisUtterance()`物件。物件內容包括：[詳見](https://developer.mozilla.org/zh-TW/docs/Web/API/SpeechSynthesisUtterance)

  - `utterance.text` : 說話的內容。
  - `utterance.lang`: 說話的語言。
  - `utterance.pitch`:說話的音頻。
  - `utterance.rate`:說話的速度。
  - `utterance.voice`:說話的聲音。
  - `utterance.volume`:說話的音量。

- 接著可以利用`speechSynthesis`操作`SpeechSynthesisUtterance()`物件。

  方法：

  - `speechSynthesis.speck()`:說話。
  - `speechSynthesis.cancel()`取消說話。
  - `speechSynthesis.pause`:暫停說話。
  - `speechSynthesis.getVoices()`:取的所有`SpeechSynthesisVoice`語音物件。
    - `SpeechSynthesisVoice`物件包含`lang`, `name`等屬性。
  - `speechSynthesis.resume()`:讓暫停的重啟。

  監聽事件：

  `speechSynthesis.onvoicechanged`:若語言改變(`speechSynthesis.getVoices`)時驅動。

```javascript
  msg.text = document.querySelector('[name="text"]').value;

  function populateVoices() {
    voices = this.getVoices();
    //console.log(voices);
    voicesDropdown.innerHTML = voices.
      map(voice => `<option value="${voice.name}">${voice.name} (${voice.lang})</option>`)
      .join('');
  }

  speechSynthesis.addEventListener('voiceschanged', populateVoices);
```

此時可以看到選取欄位內有全部的語言。

>  設定所選語言為說話的語言。

- 設定一個`toggle`方法，當做驅動說話的開關。

```javascript
  function setVoice(){
    msg.voice = voices.find(voice => voice.name == this.value);
    toggle();
  }

  function toggle(startOver = true){
    speechSynthesis.cancel();
    if (startOver) {
      speechSynthesis.speak(msg);
    }
  }
  
  voicesDropdown.addEventListener('change', setVoice);
```

此時若重新選取語言應該會發出對話。

> 監聽rate 及 pitch 更動，並切換重新說話。 

`const options = document.querySelectorAll('[type="range"], [name="text"]');`

options的選取總共有三個，名稱為`rate`, `pitch`及`text`，剛好和`SpeechSynthesisUtterance`物件的屬性符合，所以可以監聽後插入改變的數值，並執行`toggle`。

```javascript
  function setOption() {
    console.log(this.name, this.value);
    msg[this.name] = this.value;
    toggle();
  }
  
  options.forEach(option => option.addEventListener('change', setOption));
```

此時應該可以藉由切換rate, pitch, text 後聽到聲音發出。

>透過start 及 stop 按鈕開啟及結束說話。

當我們在處理事件監聽綁定函數需要參數時**無法**直接在函數內帶入參數如

`speakButton.addEventListener('click', toggle(false));//x`

 若要帶入參數可參考以下作法

```javascript
speakButton.addEventListener('click', ()=>toggle(false)); //用匿名函數
speakButton.addEventListener('click', toggle.bind(null, false));//透過為回調函數帶入參數。
```



```javascript
  speakButton.addEventListener('click', toggle);//開
  stopButton.addEventListener('click', () => toggle(false));//觀
```

> 最後若想要在選取語言時只顯示英文

- 利用`.filter()`及`includes('arg')`即可

```javascript
voicesDropdown.innerHTML = voices.
      .filter(voice => voice.lang.includes('en'))
      map(voice => `<option value="${voice.name}">${voice.name} (${voice.lang})</option>`)
      .join('');
```

> 今日練習就到此為止。
>
> 另附上完整程式碼

```javascript
<script>
  // 創立SpeechSynthesisUtterance物件
  const msg = new SpeechSynthesisUtterance();
  let voices = [];
  const voicesDropdown = document.querySelector('[name="voice"]');
  const options = document.querySelectorAll('[type="range"], [name="text"]');
  const speakButton = document.querySelector('#speak');
  const stopButton = document.querySelector('#stop');
  //設定text為說話範本
  msg.text = document.querySelector('[name="text"]').value;

  function populateVoices() {
    voices = this.getVoices();
    // console.log(voices);
    voicesDropdown.innerHTML = voices
      .filter(voice => voice.lang.includes('en'))
      .map(voice => `<option value="${voice.name}">${voice.name} (${voice.lang})</option>`)
      .join('');
  }

  function setVoice(){
    msg.voice = voices.find(voice => voice.name == this.value);
    toggle();
  }

  //作為開關, 預設帶入為true
  function toggle(startOver = true){
    speechSynthesis.cancel();
    if (startOver) {
      speechSynthesis.speak(msg);
    }
  }

  function setOption() {
    console.log(this.name, this.value);
    msg[this.name] = this.value;
    toggle();
  }

  speechSynthesis.addEventListener('voiceschanged', populateVoices);
  voicesDropdown.addEventListener('change', setVoice);
  options.forEach(option => option.addEventListener('change', setOption));
  speakButton.addEventListener('click', toggle);
  stopButton.addEventListener('click', () => toggle(false));

</script>
```



