<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Draw Your Cards with Sound and Animation!</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            height: 100vh;
            background-color: #f9f5ff;
            margin: 0;
            font-family: 'Comic Sans MS', cursive, sans-serif;
        }

        .card-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            margin-top: 20px;
        }

        .card {
            width: 120px;
            height: 160px;
            background-color: #fff;
            border: 5px solid #333;
            border-radius: 10px;
            box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
            transform-style: preserve-3d;
            margin: 10px;
            transition: transform 0.6s;
        }

        .card.is-flipped {
            transform: rotateY(180deg);
        }

        .card-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Courier New', monospace;
            font-size: 14px;
            text-align: center;
        }

        .card-front {
            background-color: #e0e0e0;
        }

        .card-back {
            background-color: #fddde6;
            transform: rotateY(180deg);
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            color: #333;
        }

        .draw-options {
            margin-top: 20px;
            display: flex;
            justify-content: center;
            gap: 10px;
        }

        .draw-options button {
            padding: 10px 20px;
            background-color: #ff4081;
            color: #fff;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            font-size: 14px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            transition: background-color 0.3s, box-shadow 0.3s;
        }

        .draw-options button:hover {
            background-color: #d81b60;
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
        }

        .draw-result {
            margin-top: 20px;
            font-family: 'Comic Sans MS', cursive, sans-serif;
            font-size: 18px;
            text-align: center;
            color: #ff4081;
        }

        .total-spent,
        .rare-count,
        .remaining-draws {
            margin-top: 10px;
            font-family: 'Comic Sans MS', cursive, sans-serif;
            font-size: 18px;
            text-align: center;
            color: #333;
        }

        .purchase-button {
            margin-top: 20px;
            padding: 10px 25px;
            background-color: #007bff;
            color: #fff;
            border: none;
            border-radius: 50px;
            cursor: pointer;
            font-size: 16px;
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
        }

        .purchase-button:hover {
            background-color: #0056b3;
        }

        .purchase-count {
            margin-top: 20px;
            font-family: 'Comic Sans MS', cursive, sans-serif;
            font-size: 18px;
            text-align: center;
            color: #333;
        }

    </style>
</head>
<body>

    <h1>Draw Your Cards with Sound and Animation!</h1>

    <div class="draw-options">
        <button onclick="drawCards(1)">Draw 1 Card</button>
        <button onclick="drawCards(10)">Draw 10 Cards</button>
    </div>

    <div id="cardContainer" class="card-container"></div>

    <div class="draw-result" id="drawResult"></div>

    <div class="total-spent" id="totalSpent">Total Spent: ¥0</div>
    <div class="remaining-draws" id="remainingDraws">Remaining Draws: 0</div>
    <div class="rare-count" id="rareCount">Rare Cards Obtained: 0</div>

    <!-- Updated buttons with rare probability information -->
    <button class="purchase-button" onclick="purchasePackage1()">Purchase Package 1 (¥20, 10 draws, 1% rare each draw)</button>
    <button class="purchase-button" onclick="purchasePackage2()">Purchase Package 2 (¥20, 1 draw, 10% rare each draw)</button>

    <!-- Display purchase counts for each package -->
    <div class="purchase-count" id="package1Count">Package 1 Purchased: 0 times</div>
    <div class="purchase-count" id="package2Count">Package 2 Purchased: 0 times</div>

    <!-- Add an audio element for the sound effect -->
    <audio id="drawSound" src="your-sound-file-path.mp3"></audio>

    <script>
        let totalSpent = 0;  // 总花费
        let availableDraws = 0;  // 可用抽卡次数
        let rareCount = 0;  // 已获得的稀有卡数量
        let currentPackage = null;  // 当前 package 类型
        let rareProbability = 0;  // 当前稀有卡概率
        let package1Count = 0;  // Package 1 购买次数
        let package2Count = 0;  // Package 2 购买次数

        const packagePrice = 20;  // 每个 package 的价格
        const package1Draws = 10;  // Package 1 提供的抽卡次数
        const package2Draws = 1;  // Package 2 提供的抽卡次数
        const package1RareProbability = 0.01;  // Package 1 的稀有卡概率
        const package2RareProbability = 0.1;  // Package 2 的稀有卡概率

        // 更新总花费
        function updateTotalSpent() {
            document.getElementById('totalSpent').innerHTML = `Total Spent: ¥${totalSpent}`;
        }

        // 更新剩余的抽卡次数
        function updateRemainingDraws() {
            document.getElementById('remainingDraws').innerHTML = `Remaining Draws: ${availableDraws}`;
        }

        // 更新获得的稀有卡数量
        function updateRareCount() {
            document.getElementById('rareCount').innerHTML = `Rare Cards Obtained: ${rareCount}`;
        }

        // 更新 Package 1 购买次数
        function updatePackage1Count() {
            document.getElementById('package1Count').innerHTML = `Package 1 Purchased: ${package1Count} times`;
        }

        // 更新 Package 2 购买次数
        function updatePackage2Count() {
            document.getElementById('package2Count').innerHTML = `Package 2 Purchased: ${package2Count} times`;
        }

        // 购买 Package 1
        function purchasePackage1() {
            if (availableDraws > 0) {
                alert("You must use all your current draws before purchasing a new package.");
                return;
            }

            totalSpent += packagePrice;
            availableDraws = package1Draws;
            rareProbability = package1RareProbability;
            currentPackage = 1;
            package1Count++;  // 记录 Package 1 的购买次数
            updateTotalSpent();
            updateRemainingDraws();
            updatePackage1Count();
            alert(`You purchased Package 1! You now have ${availableDraws} draw(s) available (1% rare card chance).`);
        }

        // 购买 Package 2
        function purchasePackage2() {
            if (availableDraws > 0) {
                alert("You must use all your current draws before purchasing a new package.");
                return;
            }

            totalSpent += packagePrice;
            availableDraws = package2Draws;
            rareProbability = package2RareProbability;
            currentPackage = 2;
            package2Count++;  // 记录 Package 2 的购买次数
            updateTotalSpent();
            updateRemainingDraws();
            updatePackage2Count();
            alert(`You purchased Package 2! You now have ${availableDraws} draw(s) available (10% rare card chance).`);
        }

        // 抽卡逻辑
        function drawCards(numCards) {
            if (availableDraws < numCards) {
                alert("Not enough draws available. Please purchase more packages.");
                return;
            }

            availableDraws -= numCards;  // 扣除抽卡次数
            document.getElementById('cardContainer').innerHTML = '';  // 清空之前的卡片
            document.getElementById('drawResult').innerHTML = '';     // 清空结果文字

            let rareCardCount = 0;

            // 播放抽卡音效
            const drawSound = document.getElementById('drawSound');
            drawSound.play();

            for (let i = 0; i < numCards; i++) {
                const randomNumber = Math.random();  // 生成一个 0 到 1 之间的随机数
                let cardType = '';

                if (randomNumber < rareProbability) {
                    // 通过稀有卡概率判断是否获得稀有卡
                    cardType = '🎉 Rare Card!';
                    rareCardCount++;
                    rareCount++;
                } else {
                    // 普通卡
                    cardType = '🙂 Common Card';
                }

                // 创建卡片元素
                const card = document.createElement('div');
                card.classList.add('card');
                card.innerHTML = `
                    <div class="card-face card-front">
                        <div>${i + 1}</div>
                    </div>
                    <div class="card-face card-back">
                        <div>${cardType}</div>
                    </div>
                `;
                document.getElementById('cardContainer').appendChild(card);

                // 添加翻转效果
                setTimeout(() => {
                    card.classList.add('is-flipped');
                }, i * 200);  // 每张卡片的翻转稍有延迟
            }

            // 显示抽卡结果
            setTimeout(() => {
                if (rareCardCount > 0) {
                    document.getElementById('drawResult').innerHTML = `🎉 You got ${rareCardCount} rare card(s)! 🎉`;
                } else {
                    document.getElementById('drawResult').innerHTML = `No rare cards this time. Try again!`;
                }
            }, numCards * 200 + 600);  // 在所有卡片翻转后显示结果

            updateRemainingDraws();
            updateRareCount();
        }
    </script>

</body>
</html>
