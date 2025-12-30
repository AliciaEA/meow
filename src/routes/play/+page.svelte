<script>
    const gameContainer = document.getElementById("game-container");
    const player = document.getElementById("player");
    const scoreDisplay = document.getElementById("score");
    const finalScoreDisplay = document.getElementById("final-score");
    const startScreen = document.getElementById("start-screen");
    const gameOverScreen = document.getElementById("game-over-screen");

    let isPlaying = false;
    let score = 0;
    let obstacleSpeed = 4;
    let obstacleInterval;
    let gameLoopId;
    let obstacles = [];

    const spaceEmojis = ["🪐", "🛸", "☄️", "🌑", "⭐", "🌕"];

    let playerPositionPercent = 50;
    let playerMoveSpeed = 1.8;
    let isMovingLeft = false;
    let isMovingRight = false;

    gameContainer.addEventListener("touchstart", handleTouch, {
        passive: false,
    });
    gameContainer.addEventListener("touchend", stopTouch, { passive: false });
    gameContainer.addEventListener("touchcancel", stopTouch, {
        passive: false,
    });

    function handleTouch(e) {
        if (!isPlaying) return;
        e.preventDefault();
        const screenWidth = window.innerWidth;
        const touchX = e.touches[0].clientX;
        if (touchX < screenWidth / 2) {
            isMovingLeft = true;
            isMovingRight = false;
        } else {
            isMovingRight = true;
            isMovingLeft = false;
        }
    }

    function stopTouch(e) {
        if (e.touches.length === 0) {
            isMovingLeft = false;
            isMovingRight = false;
        }
    }

    document.addEventListener("keydown", (e) => {
        if (!isPlaying) return;
        if (e.key === "ArrowLeft") {
            isMovingLeft = true;
        }
        if (e.key === "ArrowRight") {
            isMovingRight = true;
        }
    });

    document.addEventListener("keyup", (e) => {
        if (e.key == "ArrowLeft") {
            isMovingLeft = false;
        }
        if (e.key === "ArrowRight") {
            isMovingRight = false;
        }
    });

    // Game Logiccc
    function startGame() {
        startScreen.classList.add("hidden");
        gameOverScreen.classList.add("hidden");
        isPlaying = true;
        score = 0;
        obstacleSpeed = 5;
        scoreDisplay.innerText = score;

        playerPositionPercent = 50;
        updatePlayerPosition();
        isMovingLeft = false;
        isMovingRight = false;

        obstacles.forEach((obs) => obs.element.remove());
        obstacles = [];

        if (gameLoopId) cancelAnimationFrame(gameLoopId);
        gameLoopId = requestAnimationFrame(gameLoop);

        clearInterval(obstacleInterval);
        obstacleInterval = setInterval(spawnObstacle, 1000);
    }

    function exitToMenu() {
        gameOverScreen.classList.add("hidden");
        startScreen.classList.remove("hidden");
        isPlaying = false;
        obstacles.forEach((obs) => obs.element.remove());
        obstacles = [];
        resetGameVariables();
    }

    function resetGameVariables() {
        if (gameLoopId) cancelAnimationFrame(gameLoopId);
        clearInterval(obstacleInterval);
        isMovingLeft = false;
        isMovingRight = false;
        playerPositionPercent = 50;
        updatePlayerPosition();
    }

    function spawnObstacle() {
        if (!isPlaying) return;
        const obstacle = document.createElement("div");
        obstacle.classList.add("obstacle");

        const randomEmoji =
            spaceEmojis[Math.floor(Math.random() * spaceEmojis.length)];
        obstacle.innerText = randomEmoji;

        const randomX = Math.floor(Math.random() * 85) + 5;
        obstacle.style.left = randomX + "%";
        obstacle.style.top = "-60px";
        gameContainer.appendChild(obstacle);
        obstacles.push({ element: obstacle, y: -60 });
    }

    if (score > 0 && socre % 5 === 0) {
        obstacleSpeed += 0.3;
        clearInterval(obstacleInterval);
        let newTime = Math.max(500, 1000 - score * 20);
        obstacleInterval = setInterval(spawnObstacle, newTime);
    }

    function updatePlayerPosition() {
        if (playerPositionPercent < 6) playerPositionPercent = 6;
        if (playerPositionPercent > 94) playerPositionPercent = 94;
        player.style.left = playerPositionPercent + "%";
    }

    function gameLoop() {
        if (!isPlaying) return;

        if (isMovingLeft) playerPositionPercent -= playerMoveSpeed;
        if (isMovingRight) playerPositionPercent += playerMoveSpeed;

        updatePlayerPosition();

        obstacles.forEach((obs, index) => {
            obs.y += obstacleSpeed;
            obs.element.style.top = obs.y + "px";

            const playerRect = player.getBoundingClientRect();
            const obsRect = obs.element.getBoundingClientRect();
            const hitboxPaddingX = 20;
            const hitboxPaddingY = 15;

            if (
                playerRect.top + hitboxPaddingY < obsRect.bottom &&
                playerRect.bottom - hitboxPaddingY > obsRect.top &&
                playerRect.left + hitboxPaddingX < obsRect.right &&
                playerRect.right - hitboxPaddingX > obsRect.left
            ) {
                gameOver();
            }

            if (obs.y > gameContainer.offsetHeight) {
                obs.element.remove();
                obstacles.splice(index, 1);
                score++;
                scoreDisplay.innerText = score;
            }
        });
        gameLoopId = requestAnimationFrame(gameLoop);
    }

    function gameOver(){
        isPlaying = false;
        resetGameVariables();
        finalScoreDisplay.innerText = score;
        gameOverScreen.classList.remove('hidden');

    }
    updatePlayerPosition();
</script>

<main>
    <div id="game-container">
        <div id="score-board">Score: <span id="score">0</span></div>

        <div id="start-screen">
            <div style="font-size: 70px; margin-bottom: 10px;">🐱🌈</div>
            <h1>Space Meow Meow</h1>
            <p class="subtitle">
                Met the cute little space cat. <br />Dodge the planets and
                stars!
            </p>
            <p style="font-size: 0.8rem; color: #aaa; margin-top:30px;">
                Tap Left/Right
            </p>
            <button onclick={startGame()}>Play</button>
        </div>

        <div id="game-over-screen" class="hidden">
            <h1>Game Over</h1>

            <p class="game-over-text"> You hit a planet! 💥</p>
            <p class="game-over-text">Final Score: <span id="final-score">0</span></p>

            <div class="button-container">
                <button onclick={startGame()}>Try Again</button>
                <button class="secondary" onclick={exitToMenu()}>Exit</button>
            </div>
        </div>

        <div id="player">
            <div class="rainbow">
                <div class="r-stripe r1"></div>
                <div class="r-stripe r2"></div>
                <div class="r-stripe r3"></div>
                <div class="r-stripe r4"></div>
                <div class="r-stripe r5"></div>
                <div class="r-stripe r6"></div>
            </div>

            <div class="cat-body">
                <div class="pop-tart-frosting">
                    <div class="sprinkle s1"></div>
                    <div class="sprinkle s2"></div>
                    <div class="sprinkle s3"></div>
                    <div class="sprinkle s4"></div>
                </div>
                <div class="foot f1"></div>
                <div class="foot f2"></div>
            </div>
        </div>
    </div>
</main>

<style>
    body {
        background-color: #000;
        overflow: hidden;
        touch-action: none;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        -webkit-user-select: none;
        user-select: none;
        color: white;
    }
</style>
