<script>
    import { onMount } from 'svelte';
    import { goto } from '$app/navigation';
    import { base } from '$app/paths';

    let isPlaying = false;
    let isGameOver = false;
    let score = 0;
    let finalScore = 0;

    let obstacleSpeed = 4;
    let obstacleInterval;
    let gameLoopId;
    let obstacles = [];

    const spaceEmojis = ["🪐", "🛸", "☄️", "🌑", "⭐", "🌕"];

    let playerPositionPercent = 50;
    let playerMoveSpeed = 1.8;
    let isMovingLeft = false;
    let isMovingRight = false;

    // Bound DOM elements
    let gameContainer;
    let player;

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

    function handleKeyDown(e) {
        if (!isPlaying) return;
        if (e.key === 'ArrowLeft') {
            isMovingLeft = true;
        }
        if (e.key === 'ArrowRight') {
            isMovingRight = true;
        }
    }

    function handleKeyUp(e) {
        if (e.key === 'ArrowLeft') {
            isMovingLeft = false;
        }
        if (e.key === 'ArrowRight') {
            isMovingRight = false;
        }
    }

    // Game Logic
    function startGame() {
        isPlaying = true;
        isGameOver = false;
        score = 0;
        obstacleSpeed = 5;

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
        isPlaying = false;
        isGameOver = false;
        obstacles.forEach((obs) => obs.element.remove());
        obstacles = [];
        resetGameVariables();
    }

    function goHome() {
        goto(base + '/');
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
        if (!isPlaying || !gameContainer) return;
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

    function adjustDifficulty() {
        if (score > 0 && score % 5 === 0) {
            obstacleSpeed += 0.3;
            clearInterval(obstacleInterval);
            const newTime = Math.max(500, 1000 - score * 20);
            obstacleInterval = setInterval(spawnObstacle, newTime);
        }
    }

    function updatePlayerPosition() {
        if (!player) return;
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

            if (player) {
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
            }

            if (gameContainer && obs.y > gameContainer.offsetHeight) {
                obs.element.remove();
                obstacles.splice(index, 1);
                score++;
                adjustDifficulty();
            }
        });
        gameLoopId = requestAnimationFrame(gameLoop);
    }

    function gameOver() {
        isPlaying = false;
        finalScore = score;
        isGameOver = true;
        resetGameVariables();
    }

    onMount(() => {
        updatePlayerPosition();
    });
</script>

<svelte:window on:keydown={handleKeyDown} on:keyup={handleKeyUp} />
<main>

    <div id="game-container" bind:this={gameContainer} on:touchstart|preventDefault={handleTouch} on:touchend={stopTouch} on:touchcancel={stopTouch}>
        <div id="score-board">Score: <span id="score">{score}</span></div>

        <div id="start-screen" class:hidden={isPlaying || isGameOver}>
            <div style="font-size: 70px; margin-bottom: 10px;">🐱🌈</div>
            <h1>Space Meow Meow</h1>
            <p class="subtitle">
                Met the cute little space cat. <br />Dodge the planets and
                stars!
            </p>
            <p style="font-size: 0.8rem; color: #aaa; margin-top:30px;">
                Tap Left/Right
            </p>
            <button on:click={startGame}>Play</button>
            <button class="secondary" on:click={goHome}>Meow Home</button>
        </div>

        <div id="game-over-screen" class:hidden={!isGameOver}>
            <h1>Game Over</h1>
            <p class="game-over-text">You hit a planet! 💥</p>
            <p class="game-over-text">
                Final Score: <span id="final-score">{finalScore}</span>
            </p>

            <div class="button-container">
                <button on:click={startGame}>Try Again</button>
                <button class="secondary" on:click={exitToMenu}>Exit</button>
                <button class="secondary" on:click={goHome}>Meow Home</button>
            </div>
        </div>

        <div id="player" bind:this={player}>
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
            <div class="cat-head">
                <div class="ear l"></div>
                <div class="ear r"></div>
                <div class="eye l"></div>
                <div class="eye r"></div>
                <div class="nose"></div>
                <div class="cheek l"></div>
                <div class="cheek r"></div>
            </div>
        </div>
    </div>
</main>

<style>
    main {
        
        overflow: hidden;
        touch-action: none;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        -webkit-user-select: none;
        user-select: none;
        
    }

    #game-container {
        position: relative;
        width: 100%;
        max-width: 500px;
        height: 100vh;

        background: linear-gradient(to bottom, #0f0c29, #302b63, #24243e);
        overflow: hidden;
        box-shadow: 0 0 30px rgba(0, 255, 255, 0.2);
        border-left: 2px solid #4a4a4a;
        border-right: 2px solid #4a4a4a;
    }

    #game-container::before {
        content: ".";
        position: absolute;
        color: white;
        top: -50px;
        left: 0;
        width: 1px;
        height: 1px;

        box-shadow:
            10vw 10vh 2px white,
            20vw 80vh 1px white,
            40vw 30vh 2px white,
            60vw 90vh 1px white,
            80vw 50vh 2px white,
            90vw 10vh 1px white,
            30vw 40vh 1px white,
            70vw 20vh 2px white;
        z-index: 1;
    }

    #score-board {
        position: absolute;
        top: 20px;
        left: 20px;
        font-size: 24px;
        color: #fff;
        text-shadow: 2px 2px 0 #000;
        font-weight: bold;
        z-index: 20;
        background: rgba(0, 0, 0, 0.5);
        padding: 5px 15px;
        border-radius: 5px;
        pointer-events: none;
        border: 2px solid #fff;
    }

    #start-screen,
    #game-over-screen {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgb(116, 86, 100);
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        z-index: 30;
        text-align: center;
        padding: 20px;
        box-sizing: border-box;
    }

    h1 {
        color: #fc88d3;
        font-size: 3rem;
        margin-bottom: 10px;
        text-shadow: 3px 3px 0px #000;
        font-weight: 800;
    }

    .subtitle {
        color: #aaddff;
        font-size: 1.2rem;
        line-height: 1.5;
        margin-bottom: 40px;
        max-width: 90%;
        text-shadow: 1px 1px 0 #000;
    }

    .game-over-text {
        color: #ff6b6b;
        font-size: 1.5rem;
        margin-bottom: 20px;
        text-shadow: 1px 1px 0 #000;
    }

    .button-container {
        display: flex;
        gap: 20px;
        flex-wrap: wrap;
        justify-content: center;
    }

    button {
        background-color: #fc88d3;
        color: white;
        border: 3px solid #fff;
        padding: 15px 40px;
        font-size: 1.2rem;
        font-weight: bold;
        cursor: pointer;
        box-shadow: 5px 5px 0px rgba(0, 0, 0, 0.5);
        transition: transform 0.1s;
        text-transform: uppercase;
    }

    button:active {
        transform: translateX(2px, 2px);
        box-shadow: 2px 2px 0px rgba(0, 0, 0, 0.5);
    }

    button.secondary {
        background-color: #555;
        color: #ddd;
    }

    .hidden {
        display: none !important;
    }

    #player {
        position: absolute;
        bottom: 30px;
        left: 50%;
        width: 80px;
        height: 50px;
        transform: translateX(-50%);
        z-index: 10;
        pointer-events: none;
        transition: left 0.08s linear;
    }

    .rainbow {
        position: absolute;
        left: -20px;
        top: 10px;
        width: 30px;
        height: 30px;
        animation: rainbowFlow 0.4s infinite alternate;
        z-index: -1;
    }

    .r-stripe {
        height: 6px;
        width: 100%;
    }
    .r1 {
        background: #ff0000;
    }
    .r2 {
        background: #ff9900;
    }
    .r3 {
        background: #ffff00;
    }
    .r4 {
        background: #33ff00;
    }
    .r5 {
        background: #0099ff;
    }
    .r6 {
        background: #6633ff;
    }

    @keyframes rainbowFlow {
        0% {
            transform: scaleX(0.8);
        }
        100% {
            transform: scale(1.2);
        }
    }

    .cat-body {
        position: absolute;
        left: 10px;
        top: 5px;
        width: 44px;
        height: 36px;
        background-color: #ffcc99;
        border: 3px solid #000;
        border-radius: 4px;
        z-index: 5;
    }

    .pop-tart-frosting {
        position: absolute;
        top: 3px;
        left: 3px;
        width: 32px;
        height: 24px;
        background-color: #fc88d3;
    }

    .sprinkle {
        position: absolute;
        width: 3px;
        height: 3px;
        background: #f00;
    }
    .s1 {
        top: 5px;
        left: 8px;
        background: red;
    }
    .s2 {
        top: 15px;
        left: 5px;
        background: blue;
    }
    .s3 {
        top: 8px;
        left: 20px;
        background: white;
    }
    .s4 {
        top: 18px;
        left: 24px;
        background: yellow;
    }

    .cat-head {
        position: absolute;
        right: 0px;
        top: 0px;
        width: 30px;
        height: 25px;
        background-color: #999;
        border: 3px solid #000;
        border-radius: 5px;
        z-index: 6;
    }

    .ear {
        position: absolute;
        top: -6px;
        width: 0;
        height: 0;
        border-left: 5px solid transparent;
        border-right: 5px solid transparent;
        border-bottom: 8px solid #999;
    }
    .ear.l {
        left: 2px;
    }
    .ear.r {
        right: 2px;
    }

    /* Face */
    .eye {
        position: absolute;
        top: 8px;
        width: 4px;
        height: 4px;
        background: #000;
    }
    .eye.l {
        left: 6px;
    }
    .eye.r {
        right: 6px;
    }
    .nose {
        position: absolute;
        top: 10px;
        left: 13px;
        width: 2px;
        height: 2px;
        background: #000;
    }
    .cheek {
        position: absolute;
        top: 12px;
        width: 4px;
        height: 4px;
        background: #faa;
        opacity: 0.7;
        border-radius: 50%;
    }
    .cheek.l {
        left: 2px;
    }
    .cheek.r {
        right: 2px;
    }

    .foot{
        position: absolute;
        bottom: -3px;
        width: 6px;
        height: 6px;
        background: #999;
        border: 2px solid #000;

    }

    .f1{
        left: 12px;
    }

    .f2{
        left: 40px;
    }

    #player{
        animation:floatCat 0.5s infinite alternate
    }
    @keyframes floatCat {
        from {
            margin-bottom: 0;
        } to {margin-bottom: 5px;}
    }

    :global(.obstacle){
        position: absolute;
        font-size: 40px;
        text-align: center;
        line-height: 40px;
        z-index: 15;
        filter: drop-shadow(0 0 5px rgba(255,255,255,0.5));
    }
</style>
