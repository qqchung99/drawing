<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>你画我猜 - 儿童版</title>
    <style>
        body {
            font-family: 'Arial Rounded MT Bold', 'Arial', sans-serif;
            background-color: #FFEBF3;
            text-align: center;
            margin: 0;
            padding: 20px;
            color: #333;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background-color: white;
            border-radius: 20px;
            padding: 20px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        h1 {
            color: #FF6B9E;
            font-size: 2.5em;
            margin-bottom: 10px;
        }
        .subtitle {
            color: #FF9E4F;
            margin-top: 0;
            margin-bottom: 30px;
        }
        .game-area {
            margin: 20px 0;
        }
        .card {
            background-color: #FFF9C4;
            border-radius: 15px;
            padding: 30px;
            margin: 20px 0;
            font-size: 2em;
            font-weight: bold;
            min-height: 100px;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 3px 10px rgba(0,0,0,0.1);
            position: relative;
        }
        .level-indicator {
            position: absolute;
            top: 10px;
            right: 15px;
            font-size: 0.6em;
            color: white;
            background-color: #FF9E4F;
            padding: 3px 10px;
            border-radius: 10px;
        }
        .easy .level-indicator {
            background-color: #4CAF50;
        }
        .hard .level-indicator {
            background-color: #F44336;
        }
        .buttons {
            margin: 20px 0;
        }
        button {
            background-color: #FF6B9E;
            color: white;
            border: none;
            border-radius: 50px;
            padding: 12px 25px;
            font-size: 1.2em;
            cursor: pointer;
            margin: 0 10px;
            transition: all 0.3s;
            box-shadow: 0 3px 5px rgba(0,0,0,0.1);
        }
        button:hover {
            background-color: #FF4785;
            transform: translateY(-2px);
            box-shadow: 0 5px 8px rgba(0,0,0,0.15);
        }
        button:disabled {
            background-color: #CCCCCC;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }
        .instructions {
            background-color: #E3F2FD;
            padding: 15px;
            border-radius: 10px;
            margin-top: 20px;
            text-align: left;
        }
        .welcome-page {
            display: block;
        }
        .game-page {
            display: none;
        }
        .difficulty-buttons {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin: 30px 0;
        }
        .difficulty-btn {
            padding: 15px 30px;
            font-size: 1.3em;
        }
        .easy-btn {
            background-color: #4CAF50;
        }
        .hard-btn {
            background-color: #F44336;
        }
        .back-btn {
            background-color: #9E9E9E;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="container welcome-page" id="welcome-page">
        <h1>🎨 你画我猜 🎨</h1>
        <p class="subtitle">儿童欢乐版 - 画出你看到的词语！</p>
        
        <div class="instructions">
            <h3>游戏说明：</h3>
            <p>1. 选择游戏难度开始</p>
            <p>2. 根据卡片上的词语画画，让其他人猜</p>
            <p>3. 猜对后点击"下一个"获得新词语</p>
            <p>4. 如果太难可以"跳过"当前词语</p>
            <p>5. 简单词语更容易画，困难词语更有挑战性！</p>
        </div>
        
        <div class="difficulty-buttons">
            <button class="difficulty-btn easy-btn" id="easy-btn">简单级别</button>
            <button class="difficulty-btn hard-btn" id="hard-btn">困难级别</button>
        </div>
    </div>
    
    <div class="container game-page" id="game-page">
        <h1>🎨 你画我猜 🎨</h1>
        
        <div class="game-area">
            <div class="card" id="card">
                <div class="level-indicator">难度</div>
                <div id="word">准备开始！</div>
            </div>
        </div>
        
        <div class="buttons">
            <button id="next-btn">下一个</button>
            <button id="skip-btn">跳过</button>
            <button class="back-btn" id="back-btn">返回首页</button>
        </div>
    </div>

    <script>
        // 游戏词汇
        const words = {
            easy: ["猫", "狗", "兔子", "鱼", "苹果", "香蕉", "西瓜", "草莓", "书包", "铅笔", "太阳", "气球", "吃饭"],
            hard: ["彩虹", "闪电", "流星", "火箭", "汉堡", "冰淇淋", "披萨", "独角兽（马+角）", "美人鱼（鱼+人）", "会飞的房子", "长头发的太阳"]
        };
        
        // 游戏变量
        let currentWord = "";
        let currentLevel = "";
        
        // DOM元素
        const welcomePage = document.getElementById("welcome-page");
        const gamePage = document.getElementById("game-page");
        const wordElement = document.getElementById("word");
        const cardElement = document.getElementById("card");
        const easyBtn = document.getElementById("easy-btn");
        const hardBtn = document.getElementById("hard-btn");
        const nextBtn = document.getElementById("next-btn");
        const skipBtn = document.getElementById("skip-btn");
        const backBtn = document.getElementById("back-btn");
        
        // 按钮事件监听
        easyBtn.addEventListener("click", () => startGame("easy"));
        hardBtn.addEventListener("click", () => startGame("hard"));
        nextBtn.addEventListener("click", nextWord);
        skipBtn.addEventListener("click", nextWord);
        backBtn.addEventListener("click", backToWelcome);
        
        function startGame(level) {
            currentLevel = level;
            
            // 切换页面
            welcomePage.style.display = "none";
            gamePage.style.display = "block";
            
            // 更新卡片样式
            cardElement.className = "card " + level;
            cardElement.querySelector(".level-indicator").textContent = 
                level === "easy" ? "简单" : "困难";
            
            // 显示第一个词
            nextWord();
        }
        
        function nextWord() {
            // 从当前难度中随机选词
            const wordList = words[currentLevel];
            currentWord = wordList[Math.floor(Math.random() * wordList.length)];
            
            // 更新显示
            wordElement.textContent = currentWord;
        }
        
        function backToWelcome() {
            // 返回欢迎页面
            gamePage.style.display = "none";
            welcomePage.style.display = "block";
        }
        
        // 初始化显示第一个词
        nextWord();
    </script>
</body>
</html>
