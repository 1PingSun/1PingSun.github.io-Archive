# 當資安遇上密室逃脫：一群高中生以資安為主題製作密室逃脫的故事

![](posts/2025-Cybersecurity-escape-room/image.webp)

在上學期（113–1）校內科技進階課程中，老師規劃帶領我們共同製作一場密室逃脫活動，並藉由學校期末的學習分享會活動進行展示及開放外賓遊玩體驗。在這篇文章當中，將會先介紹最終的遊玩方式，接著紀錄製作過程以及製作時遇到的各種挑戰。

## 遊玩方式紀錄

### 故事背景

玩家是一群特工，被指派任務修復造成時空扭曲的時間機器。同時這是一場資安密室逃脫，因此會融入一些資安知識以及意料之外的解謎方式。

### 第一關

![](posts/2025-Cybersecurity-escape-room/image2.webp)

玩家會在電腦上看到一個輸入框，以及螢幕右下角的摩斯電碼（超大）便條紙，玩家需要將摩斯電碼對照桌面上的對照表解開題目，才能獲得下一關的提示，經過對照後可以得到密碼為「tschool」。在這一關當中，主要想呈現的資安觀念是，不要講密碼隨意的寫在便條紙並貼在螢幕上。

其實我們還埋了一個伏筆，這個登入介面使用前端驗證，因此玩家可以查看網頁原始碼直接看到 Hard Code 的密碼，或也可以直接看到按鈕點擊後跳到的下一個頁面，直接將其打開查看下一關的提示。

過關後會得到以下提示：

![](posts/2025-Cybersecurity-escape-room/image3.webp)

### 第二關

![](posts/2025-Cybersecurity-escape-room/image4.webp)

來到第二關看到一個三位數的密碼鎖保險箱，根據上一關提供的線索「密碼好像跟時間有關」，可以猜測密碼為文件的日期，嘗試輸入密碼「213」後就成功開啟保險箱。在這關當中，主要想強調弱密碼的危害。

接著就拿到了一些道具以及一張提示卡：

![](posts/2025-Cybersecurity-escape-room/image5.webp)

### 第三關

![](posts/2025-Cybersecurity-escape-room/image6.webp)

來到第三關，發現是一個密碼保險箱，依據上一關的提示「上次實驗室停電的時候箱子突然解鎖」，可以嘗試將保險箱旁的電源（行動電源）拔除，缺乏電力後密碼箱的電磁貼失效，即可開啟保險箱。

### 第四關

![](posts/2025-Cybersecurity-escape-room/image7.webp)

來到第四關看到地上散落的指紋（RFID 磁扣）依序嘗試即可開啟保險箱，此關卡主要想表達應保護好自己的個人資料及指紋等，同時也想藉由這關表達暴力解（爆破）的概念。

### 第五關

![](posts/2025-Cybersecurity-escape-room/image8.webp)

第五關是一個電流急急棒的遊戲，玩家需要嘗試通過電流急急棒遊戲才可以打開保險箱，然而直接通關的可能性較低。因此我們設計了一種通過的方式，玩家可以先將後方導線拔除，接著將電流急急棒移動到重點再接上導線，以表達資安競賽經常遇到的「非預期解」。

### 修復時間機器

經過五個關卡的解謎後，會得到一些道具，玩家需要嘗試將其按照提示正確的安裝，以修復時間機器。

![](posts/2025-Cybersecurity-escape-room/image9.webp)

機器上面有數條導線和零件需要組裝，機器上方的燈條會依據完成度進行不同的變化。

![](posts/2025-Cybersecurity-escape-room/image10.webp)

---

## 製作歷程

我在當中負責時間機器的製作，因此下方會先簡單說明一下其他關卡的製作方式，再詳細介紹時間機器在製作時的細節。

### 登入系統

第一關使用靜態網頁撰寫，僅包含兩個 HTML 檔，登入介面的檔案內容如下：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Password Check</title>
    <style>
        body {
            background-image: url("img/123.jpg");
            background-size: cover;
            background-blend-mode: multiply;
            background-color: rgba(255, 255, 255, 0.5);
            text-align: center;
            background-repeat: no-repeat;
            background-position: center;
            background-attachment: fixed;
        }
        form {
            width: 300px;
            margin: 0 auto;
            padding: 20px;
            background-color: #fff;
            border: 1px solid #ddd;
            border-radius: 5px;
            background-color: rgba(255, 255, 255, 0.75);
        }
        label {
            display: block;
            margin-bottom: 10px;
        }
        input[type="password"],
        input[type="button"] {
            width: 100%;
            padding: 10px;
            margin-bottom: 20px;
            border: 1px solid #ccc;
        }
        input[type="button"] {
            background-color: rgb(60, 164, 162);
            color: #fff;
            border: none;
            cursor: pointer;
        }
        input[type="password"] {
            margin-left: -10px;
        }
    </style>
</head>
<body style="display: flex; align-items: center; justify-content: left; height: 100vh;">
    <form id="passwordForm" style="text-align: center;">
        <label for="password">Enter Password：</label>
        <input type="password" id="password" name="password">
        <input type="button" value="Go" onclick="checkPassword()">
        <p>破解後可以獲得實驗室的更多資料</p>
    </form>

    <script>
        function checkPassword() {
            var password = document.getElementById('password').value;
            if (password === 'tschool' || password === 'Tschool') {
                //！！！！！！！！！被發現了😢
                alert('Password correct! Redirecting...');
                window.location.href = "uu.html";
            } else {
                alert('Incorrect password. Please try again.');
            }
        }

        // Add event listener for form submission
        document.getElementById('passwordForm').addEventListener('submit', function(event) {
            event.preventDefault(); // Prevent the form from refreshing the page
            checkPassword();
        });
    </script>
</body>
</html>
```

### 雷切密碼鎖保險箱

第三關使用電磁鐵作為鎖著保險箱的關鍵。製作者在設計時，使用熱溶膠固定電磁鐵，但因電磁鐵發熱的關係，造成電磁鐵脫落；另外，也因為只使用 5V 的電磁鐵，在吸力不足的情況下，有許多玩家直接將其暴力解開（當然，因為是資安密室逃脫，所以我們也讓他過關）。

### 指紋 RFID 保險箱

第四關使用 RFID 感測器搭配伺服馬達製作保險箱，製作者還很貼心的設計了一張工作人員專用卡，讓密室逃脫在運行時，工作人員能夠快速的復原場地。

### 電流急急棒保險箱

第五關使用 3D 列印製作不規則的軌道，再搭配鋁箔紙包裹在外測，把手則使用鐵絲設計。

### 時間機器製作

時間機器包含一條提供數位時鐘電源的導線、三條提供燈條訊號及電源的導線、一個 DC 電源接頭能夠插上「漏斗」道具零件、一個同軸電纜接頭能夠接上天線以及一個 USB 接頭能夠接上 USB 實體安全金鑰。

* 數位時鐘僅需正負極的電源，因此該導線使用兩孔防呆接頭供玩家連接。
* 燈條的三條線分別為正負極兩條以及一條訊號線，為了避免接錯造成燈條燒毀，我使用繼電器作為阻攔，並透過不同電阻值的電阻作為判斷是否連接正確的判斷依據，藉由此種方式製作出類似 Routing filters 的概念。
* 為了能夠判斷漏洞是否連接上，我將漏斗黏上 DC 電源接頭，並將其正負極相連，藉此製作類似開關的方式供單晶片進行判斷。
* 天線部分使用 3D 列印搭配鐵絲製作而成，將其黏上一條同軸電纜線，並將其中間的導線及外部的地線隔離層相連，製作與漏斗道具類似的方式進行偵測判斷。
* USB 則使用樹莓派進行密鑰的判斷，最後會控制門口的無線燈條（使用 D1 mini 單晶片搭配 WS2812 燈條製作）切換顏色通關。

---

## 挑戰與總結

* 在製作密室逃脫前，老師有帶大家去玩了一次密室逃脫。加上自己過往的經驗，發下密室逃脫的故事劇情總是相當的牽強，因此在本次密室逃脫設計時，我就希望能夠讓故事是通順符合邏輯的，但最後因為沒有被分配到故事組，所以無法控制故事劇情。
* 本來預期八週的製作其成，因為安排上的問題，最後僅使用兩週就將其完成，但也因為這樣，加上學期期末課業繁忙，造成團隊壓力很大，甚至有許多組員在最後一天才趕工完成，也造成了許多的矛盾。
* 在開始密室逃脫的前一天準備日，有的組員還沒做完份內的關卡設計，加上並無任何聲光效果，環境搭建相當不樂觀。好險後來組員都有盡力的完成（或是我下海營救XD），加上我使用 [cmatrix](https://github.com/abishekvashok/cmatrix) 連接大螢幕播放裝逼（誤）的駭客電影效果，以及使用 KTV 氣氛燈、播放音樂等，最後創造了一場相當成功的密室逃脫遊戲。

![](posts/2025-Cybersecurity-escape-room/image11.webp)

經過這次的經驗我發現可以透過遊戲的方式傳達知識或理念，這次以「資安」為主軸進行設計，或許也可透過遊戲方式傳達其他議題的想法。另外，在這次合作過程中，可以發現不同個性的組員要如何合作（對付及利用），以及最重要的是，應該提早開工，避免最後壓力山大。

如果有任何疑問或建議，歡迎來信詢問：sunyipingtw@icloud.com。