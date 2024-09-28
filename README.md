<!DOCTYPE html>
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

        .pixel-art {
            width: 40px;
            height: 40px;
            background-color: #ff9900;
            border: 2px solid #ff4081;
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            grid-template-rows: repeat(4, 1fr);
        }

        .pixel-art div {
            width: 100%;
            height: 100%;
            background-color: #ffcc00;
        }

        .pixel-art div:nth-child(2),
        .pixel-art div:nth-child(3),
        .pixel-art div:nth-child(6),
        .pixel-art div:nth-child(7),
        .pixel-art div:nth-child(10),
        .pixel-art div:nth-child(11),
        .pixel-art div:nth-child(14),
        .pixel-art div:nth-child(15) {
            background-color: #333;
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

        .total-spent {
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

    <button class="purchase-button" onclick="purchasePackage()">Purchase 1 package (¥20)</button>

    <!-- Add an audio element for the sound effect -->
    <audio id="drawSound" src="your-sound-file-path.mp3"></audio>

    <script>
        let totalSpent = 0;  // Total money spent
        let availableDraws = 0;  // Available card draws
        const rareProbability = 0.01;  // 1% chance of getting a rare card
        const packagePrice = 20;  // Each package costs ¥20
        const drawsPerPackage = 10;  // Each package gives 10 draws

        // Function to purchase a package
        function purchasePackage() {
            totalSpent += packagePrice;  // Add to total spent
            availableDraws += drawsPerPackage;  // Add 10 draws
            updateTotalSpent();
            alert(`You bought 1 package! You now have ${availableDraws} draw(s) available.`);
        }

        // Function to update total spent display
        function updateTotalSpent() {
            document.getElementById('totalSpent').innerHTML = `Total Spent: ¥${totalSpent}`;
        }

        // Card draw logic with sound effect
        function drawCards(numCards) {
            if (availableDraws < numCards) {
                alert("Not enough draws available. Please purchase more packages.");
                return;
            }

            availableDraws -= numCards;  // Deduct draws
            document.getElementById('cardContainer').innerHTML = '';  // Clear previous cards
            document.getElementById('drawResult').innerHTML = '';     // Clear result text

            let rareCount = 0;

            // Play the sound effect when the draw starts
            const drawSound = document.getElementById('drawSound');
            drawSound.play();

            for (let i = 0; i < numCards; i++) {
                const randomNumber = Math.random();  // Generate a random number between 0 and 1
                let cardType = '';

                if (randomNumber < rareProbability) {
                    // 1% chance of getting a rare card
                    cardType = '🎉 Rare Card!';
                    rareCount++;
                } else {
                    // Common card
                    cardType = '🙂 Common Card';
                }

                // Create card element
                const card = document.createElement('div');
                card.classList.add('card');
                card.innerHTML = `
                    <div class="card-face card-front">
                        <div class="pixel-art">
                            <div></div><div></div><div></div><div></div>
                            <div></div><div></div><div></div><div></div>
                            <div></div><div></div><div></div><div></div>
                            <div></div><div></div><div></div><div></div>
                        </div>
                    </div>
                    <div class="card-face card-back">
                        <div>${cardType}</div>
                    </div>
                `;
                document.getElementById('cardContainer').appendChild(card);

                // Add flip effect
                setTimeout(() => {
                    card.classList.add('is-flipped');
                }, i * 200); // Slight delay for each card flip
            }

            // Show draw results
            setTimeout(() => {
                if (rareCount > 0) {
                    document.getElementById('drawResult').innerHTML = `🎉 You got ${rareCount} rare card(s)! 🎉`;
                } else {
                    document.getElementById('drawResult').innerHTML = `No rare cards this time. Try again!`;
                }
            }, numCards * 200 + 600); // Delay result display until all cards are flipped
        }
    </script>

</body>
</html>
