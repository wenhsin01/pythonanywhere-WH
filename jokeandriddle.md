# _Random Joke & Riddle Generator_

在 PythonAnywhere 上建立的 Flask Web App，提供隨機笑話與隨機謎語，並且有封面與選擇頁。

## 目錄

- [Flask介紹]
- [專案介紹]
- [功能特色]
- [pythonanywhere結構]
- [環境設置]

## Flask介紹

Flask 是用 Python 撰寫的輕量級 Web 框架，專注於處理 HTTP 請求與回應，不強制特定專案結構；透過 @app.route 定義路由並支援多種 HTTP 方法與
動態參數，使開發 REST API 與網頁視圖直覺簡單；內建 Jinja2 模板引擎與預設的 static 資料夾，方便產生動態 HTML 並管理 CSS/JS/圖片等靜態資
源；核心保持簡潔，常見功能如資料庫操作、表單驗證與使用者登入可透過豐富擴充套件靈活引入；適合快速原型、小型或中型專案，並能搭配生產環境伺服器
與反向代理部署到雲端。

## 專案介紹

這是一個使用 Flask 框架打造的 Web 應用，部署於 PythonAnywhere。  

使用者到達首頁（Cover）→ 選擇「隨機笑話」或「隨機謎語」→ 進入對應的產生器頁面→ 按鈕點擊即可獲得新的笑話或謎語並且背景也會隨機更換。  

整體設計追求簡潔、專業，並且對行動裝置友好。

## 功能特色:使用大圖搭配半透明遮罩與醒目按鈕，引導使用者進入主體流程

- 封面（Cover Page）與「開始體驗」按鈕:使用大圖搭配半透明遮罩與醒目按鈕，引導使用者進入主體流程

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>歡迎來到隨機笑話謎語產生器</title>
  <style>
    * { margin:0; padding:0; box-sizing:border-box; }
    body {
      font-family: Arial, sans-serif;
      background: url("/static/images/cover.png") center/cover no-repeat;
      min-height: 100vh;
      display: flex; align-items: center; justify-content: center;
      text-align: center; color: #fff;
    }
    .cover-box {
      background: rgba(0,0,0,0.6);
      padding: 40px; border-radius: 12px;
    }
    h1 { font-size: 3rem; margin-bottom: 20px; }
    .btn-start {
      padding: 12px 30px; font-size: 1.2rem;
      background: #e91e63; color: #fff;
      border:none; border-radius:50px;
      cursor:pointer; text-decoration:none;
    }
    .btn-start:hover { background: #d81b60; }
  </style>
</head>
<body>
  <div class="cover-box">
    <h1>歡迎來到隨機笑話、謎語產生器</h1>
    <p>點擊下面按鈕，開始你的遊玩之旅！</p>
    <a href="/choice" class="btn-start">開始體驗</a>
  </div>
</body>
</html>
```

- 選擇頁（Choice Page)切換笑話／謎語:提供簡潔明瞭的按鈕選單，讓使用者自由選擇要體驗的內容類型

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>選擇你的體驗</title>
  <style>
    html, body { height:100%; margin:0; }
    body {
      font-family: Arial, sans-serif;
      background: url("/static/images/boy.png") center/cover no-repeat;
      display:flex; align-items:center; justify-content:center;
      text-align:center; color:#fff;
    }
    .box {
      background: rgba(0,0,0,0.6);
      padding: 40px; border-radius:12px;
      width: 90%; max-width:400px;
    }
    h1 { margin-bottom:20px; }
    .btn {
      display:block; margin:10px auto;
      padding:12px 20px; width:80%;
      background:#e91e63; color:#fff;
      border:none; border-radius:50px;
      font-size:1rem; cursor:pointer;
      text-decoration:none;
    }
    .btn:hover { background:#d81b60; }
  </style>
</head>
<body>
  <div class="box">
    <h1>請選擇</h1>
    <a href="/joke"   class="btn">隨機笑話</a>
    <a href="/riddle" class="btn">隨機謎語</a>
  </div>
</body>
</html>
```

- 隨機笑話生產器（Random Joke Generator）:每次進入或按下「再來一則」都會從笑話清單中隨機挑選一則新笑話

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>隨機笑話產生器</title>
    <style>
        html, body {
            height: 100%;
            margin: 0;
        }

        /* 全域重置與字體 */
        * { margin:0; padding:0; box-sizing:border-box; }
        body {
            font-family: 'Helvetica Neue', Arial, sans-serif;
            color: #fff;
            background:
                linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)), /* 遮罩 */
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-align: center;
        }

        /* 容器 */
        .container {
            max-width: 600px;
            padding: 40px 20px;
            background: rgba(255,255,255,0.1);
            border-radius: 12px;
                backdrop-filter: blur(8px); /* 背後模糊 */
        }

        /* 標題 */
        h1 {
            font-size: 2.5rem;
            font-weight: 700;
            margin-bottom: 20px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.6);
        }

        /* 笑話文字 */
        .joke-text {
            font-size: 1.2rem;
            line-height: 1.6;
            margin-bottom: 30px;
            padding: 0 10px;
        }

        /* 按鈕 */
        .btn {
            display: inline-block;
            padding: 12px 30px;
            font-size: 1rem;
            font-weight: 600;
            color: #fff;
            background-color: #e91e63;
            border: none;
            border-radius: 50px;
            cursor: pointer;
            box-shadow: 0 6px 12px rgba(0,0,0,0.3);
            transition: transform 0.2s, box-shadow 0.2s;
            text-decoration: none;
        }
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 16px rgba(0,0,0,0.4);
        }

        /* 響應式：小螢幕 */
        @media (max-width: 480px) {
            .container {
                padding: 20px 10px;
            }
            h1 { font-size: 2rem; }
            .joke-text { font-size: 1rem; }
            .btn { padding: 10px 20px; }
        }

        /* 按鈕點擊時的「縮一下再彈回」動畫 */
        .btn:active {
            transform: scale(0.96);
            transition: transform 0.1s;
        }

        /* 另一個淡入效果：整個 container 載入時 */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to   { opacity: 1; transform: translateY(0);     }
        }
        .container {
            animation: fadeIn 0.6s ease-out;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>隨機笑話產生器</h1>
        <p class="joke-text">{{ joke }}</p>
        <button class="btn" onclick="location.reload()">再來一則</button>
    </div>
    <script>
        // 1. 建立一個陣列，列出所有背景圖的路徑
        const backgrounds = [
            '/static/images/monster.png',
            '/static/images/animal.png',
            '/static/images/shark.png'
        ];

        // 2. 定義一個函式，用來隨機選圖並設定到 body
        function setRandomBackground() {
            // 產生一個 0 到 (背景圖數量-1) 的隨機整數
            const idx = Math.floor(Math.random() * backgrounds.length);
            // 把 body 的背景圖設為陣列中的隨機一張
            document.body.style.backgroundImage =
                `linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)), url("${backgrounds[idx]}")`;
            document.body.style.backgroundSize = 'center';
            document.body.style.backgroundPosition = 'center';
        }

        // 3. 當網頁「完全載入完」時，呼叫一次
        window.addEventListener('DOMContentLoaded', setRandomBackground);

        // 4. 當使用者按下「再來一則」按鈕時，也呼叫一次
        document.querySelector('.btn')
            .addEventListener('click', setRandomBackground);
    </script>
</body>
</html>
```

- 隨機謎語生產器（Random Riddle Generator）:同理地，每次操作都會隨機顯示一則謎語，增添互動趣味

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>隨機謎語產生器</title>
    <style>
        html, body {
            height: 100%;
            margin: 0;
        }

        /* 全域重置與字體 */
        * { margin:0; padding:0; box-sizing:border-box; }
        body {
            font-family: 'Helvetica Neue', Arial, sans-serif;
            color: #fff;
            background:
                linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)), /* 遮罩 */
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-align: center;
        }

        /* 容器 */
        .container {
            max-width: 600px;
            padding: 40px 20px;
            background: rgba(255,255,255,0.1);
            border-radius: 12px;
                backdrop-filter: blur(8px); /* 背後模糊 */
        }

        /* 標題 */
        h1 {
            font-size: 2.5rem;
            font-weight: 700;
            margin-bottom: 20px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.6);
        }

        /* 笑話文字 */
        .riddle-text {
            font-size: 1.2rem;
            line-height: 1.6;
            margin-bottom: 30px;
            padding: 0 10px;
        }

        /* 按鈕 */
        .btn {
            display: inline-block;
            padding: 12px 30px;
            font-size: 1rem;
            font-weight: 600;
            color: #fff;
            background-color: #e91e63;
            border: none;
            border-radius: 50px;
            cursor: pointer;
            box-shadow: 0 6px 12px rgba(0,0,0,0.3);
            transition: transform 0.2s, box-shadow 0.2s;
            text-decoration: none;
        }
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 16px rgba(0,0,0,0.4);
        }

        /* 響應式：小螢幕 */
        @media (max-width: 480px) {
            .container {
                padding: 20px 10px;
            }
            h1 { font-size: 2rem; }
            .riddle-text { font-size: 1rem; }
            .btn { padding: 10px 20px; }
        }

        /* 按鈕點擊時的「縮一下再彈回」動畫 */
        .btn:active {
            transform: scale(0.96);
            transition: transform 0.1s;
        }

        /* 另一個淡入效果：整個 container 載入時 */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to   { opacity: 1; transform: translateY(0);     }
        }
        .container {
            animation: fadeIn 0.6s ease-out;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>隨機謎語產生器</h1>
        <p class="riddle-text">{{ riddle }}</p>
        <button class="btn" onclick="location.reload()">再來一則</button>
    </div>
    <script>
        // 1. 建立一個陣列，列出所有背景圖的路徑
        const backgrounds = [
            '/static/images/think.png',
            '/static/images/animall.png',
            '/static/images/small.png'
        ];

        // 2. 定義一個函式，用來隨機選圖並設定到 body
        function setRandomBackground() {
            // 產生一個 0 到 (背景圖數量-1) 的隨機整數
            const idx = Math.floor(Math.random() * backgrounds.length);
            // 把 body 的背景圖設為陣列中的隨機一張
            document.body.style.backgroundImage =
                `linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)), url("${backgrounds[idx]}")`;
            document.body.style.backgroundSize = 'center';
            document.body.style.backgroundPosition = 'center';
        }

        // 3. 當網頁「完全載入完」時，呼叫一次
        window.addEventListener('DOMContentLoaded', setRandomBackground);

        // 4. 當使用者按下「再來一則」按鈕時，也呼叫一次
        document.querySelector('.btn')
            .addEventListener('click', setRandomBackground);
    </script>
</body>
</html>
```

- 背景圖隨機切換:在每次頁面載入或按鈕觸發時自動更換不同背景，營造視覺新鮮感

1.上傳多張背景圖

2.在 /body 前加 JavaScript

```html
<script>
  // 1. 背景圖檔名陣列
  const backgrounds = [
    '/static/images/bg1.jpg',
    '/static/images/bg2.jpg',
    '/static/images/bg3.jpg'
  ];

  // 2. 隨機挑一張
  function setRandomBackground() {
    const idx = Math.floor(Math.random() * backgrounds.length);
    document.body.style.backgroundImage =
      `linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)), url("${backgrounds[idx]}")`;
    document.body.style.backgroundSize = 'cover';
    document.body.style.backgroundPosition = 'center';
  }

  // 3. 載入時、或按鈕時都可以換
  window.addEventListener('DOMContentLoaded', setRandomBackground);
  document.querySelector('.btn').addEventListener('click', setRandomBackground);
</script>
```

- 卡片置中、玻璃模糊效果  
- 響應式設計，手機與桌機皆可友好瀏覽
