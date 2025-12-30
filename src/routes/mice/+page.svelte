<script>
    import { onMount } from 'svelte';
    import { goto } from '$app/navigation';

    let isPlaying = false;
    let isGameOver = false;
    let score = 0;
    let finalScore = 0;

    let obstacleSpeed = 5;
    let obstacleInterval;
    let gameLoopId;
    let obstacles = [];

    const hazards = ["🐱", "🐾", "🪤", "💣", "🔨"];

    let playerPositionPercent = 50;
    let playerMoveSpeed = 2.0;
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
        goto('/');
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

        const randomEmoji = hazards[Math.floor(Math.random() * hazards.length)];
        obstacle.innerText = randomEmoji;

        const randomX = Math.floor(Math.random() * 85) + 5;
        obstacle.style.left = randomX + "%";
        obstacle.style.top = "-60px";
        gameContainer.appendChild(obstacle);
        obstacles.push({ element: obstacle, y: -60 });

        // difficulty tweaks handled on score increment
    }

    function adjustDifficulty() {
        if (score > 0 && score % 10 === 0) {
            obstacleSpeed += 0.5;
            clearInterval(obstacleInterval);
            const newTime = Math.max(400, 1000 - score * 15);
            obstacleInterval = setInterval(spawnObstacle, newTime);
        }
    }

    function updatePlayerPosition() {
        if (!player) return;
        if (playerPositionPercent < 8) playerPositionPercent = 8;
        if (playerPositionPercent > 92) playerPositionPercent = 92;
        player.style.left = playerPositionPercent + "%";

        const wrapper = player.querySelector(".mouse-wrapper");
        if (wrapper) {
            wrapper.style.transform = isMovingLeft ? "scaleX(-1)" : "scaleX(1)";
        }
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
            <div style="font-size: 60px; margin-bottom: 0px;">🐱⚡🐭</div>
            <h1>Tom & Jerry</h1>
            <p class="subtitle">...But you are the mouse!</p>
            <div class="instructions">
                Tom is chasing you!<br />
                Tap <b>Left</b> or <b>Right</b> to dodge.
            </div>
            <button on:click={startGame}>Play</button>
            <button class="secondary" on:click={goHome}>Inicio</button>
        </div>

        <div id="game-over-screen" class:hidden={!isGameOver}>
            <div style="font-size: 50px;">💥🤕</div>
            <h1 style="font-size: 2.5rem;">CAUGHT!</h1>
            <p class="subtitle">Tom got you.</p>
            <p
                style="font-size: 1.2rem; font-weight: bold; margin-bottom:20px;"
            >
                Score: <span id="final-score">{finalScore}</span>
            </p>

            <div class="button-container">
                <button on:click={startGame}>Try Again</button>
                <button class="secondary" on:click={exitToMenu}>Menu</button>
                <button class="secondary" on:click={goHome}>Inicio</button>
            </div>
        </div>

        <div id="player" bind:this={player}>
            <div class="mouse-wrapper">
                <div class="sweat"></div>
                <div class="ear l"><div class="ear-inner"></div></div>
                <div class="ear r"><div class="ear-inner"></div></div>
                <div class="mouse-head">
                    <div class="eye l"><div class="pupil"></div></div>
                    <div class="eye r"><div class="pupil"></div></div>
                    <div class="nose"></div>
                    <div class="mouth"></div>
                </div>
                <div class="mouse-body">
                    <div class="mouse-belly"></div>
                </div>
                <div class="tail"></div>
            </div>
        </div>
    </div>
</main>

<style>
    main {
        margin: 0;
        padding: 0;
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
        background-color: #d68c45;
        background-image: repeating-linear-gradient(
            90deg,
            #cd853f 0px,
            #cd853f 48px,
            #b06c2e 50px
        );
        overflow: hidden;
        box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
        border-left: 5px solid #5d3a1a;
        border-right: 5px solid #5d3a1a;
    }

    #game-container::after {
        content: "";
        position: absolute;
        top: 0;
        left: 0%;
        width: 100%;
        height: 100%;
        box-shadow: inset 0 0 50px rgba(0, 0, 0, 0.3);
        pointer-events: none;
        z-index: 2;
    }

    #score-board {
        position: absolute;
        top: 20px;
        left: 20px;
        font-size: 28px;
        color: #fff;
        text-shadow: 3px 3px 0 #000;
        font-weight: bold;
        z-index: 20;
        background: rgba(0, 0, 0, 0.4);
        padding: 5px 15px;
        border-radius: 10px;
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
        background: rgba(255, 228, 196, 0.95); /* Creamy color */
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        z-index: 30;
        text-align: center;
        padding: 20px;
        box-sizing: border-box;
        border: 10px solid #8b4513;
    }

    h1 {
        color: #d32f2f;
        font-size: 3.5rem;
        margin-bottom: 5px;
        text-shadow:
            2px 2px 0px #fff,
            4px 4px 0px #000;
        font-weight: 900;
        line-height: 1;
    }

    .subtitle {
        color: #8b4513;
        font-size: 1.5rem;
        font-weight: bold;
        margin-bottom: 10px;
        text-shadow: 1px 1px 0 #fff;
    }

    .instructions {
        font-size: 1rem;
        color: #555;
        margin-top: 20px;
        margin-bottom: 30px;
    }

    .button-container {
        display: flex;
        gap: 20px;
        flex-wrap: wrap;
        justify-content: center;
    }

    button {
        background-color: #ffcc00;
        color: #8b4513;
        border: 4px solid #8b4513;
        padding: 15px 40px;
        font-size: 1.5rem;
        font-family: inherit;
        font-weight: bold;
        cursor: pointer;
        border-radius: 15px;
        box-shadow: 0 6px 0px #8b4513;
        transition: transform 0.1s;
        text-transform: uppercase;
    }

    button:active {
        transform: translateY(4px);
        box-shadow: 0 2px 0px #8b4513;
    }

    button.secondary {
        background-color: #e0e0e0;
        color: #555;
        border-color: #555;
        box-shadow: 0 6px 0px #555;
    }

    button.secondary:active {
        box-shadow: 0 2px 0px #555;
    }
    .hidden {
        display: none !important;
    }

    #player {
        position: absolute;
        bottom: 30px;
        left: 50%;
        width: 60px;
        height: 80px;
        transform: translateX(-50%);
        z-index: 10;
        pointer-events: none;
        transition: left 0.08s linear;
    }
    .mouse-wrapper {
        width: 100%;
        height: 100%;
        position: relative;
        animation: shake 0.2s infinite;
    }

    @keyframes shake {
        0% {
            transform: translateY(0) rotate(0deg);
        }
        25% {
            transform: translateY(-2px) rotate(-2deg);
        }
        50% {
            transform: translateY(0) rotate(0deg);
        }
        75% {
            transform: translateY(-2px) rotate(2deg);
        }
    }

    .mouse-body {
        position: absolute;
        bottom: 0;
        left: 10px;
        width: 40px;
        height: 50px;
        background-color: #a0522d;
        border-radius: 50% 50% 10px 10px;
        z-index: 5;
        box-shadow: inset -3px 0 5px rgba(0, 0, 0, 0.2);
    }

    .mouse-belly {
        position: absolute;
        bottom: 5px;
        left: 8px;
        width: 24px;
        height: 35px;
        background-color: #deb887; /* Burlywood */
        border-radius: 50%;
    }

    .mouse-head {
        position: absolute;
        top: 10px;
        left: 5px;
        width: 50px;
        height: 45px;
        background-color: #a0522d;
        border-radius: 50%;
        z-index: 6;
    }
    .ear {
        position: absolute;
        top: -15px;
        width: 28px;
        height: 28px;
        background-color: #a0522d;
        border-radius: 50%;
        z-index: 4;
    }
    .ear.l {
        left: -5px;
    }
    .ear.r {
        right: -5px;
    }
    .ear-inner {
        position: absolute;
        top: 4px;
        left: 4px;
        width: 20px;
        height: 20px;
        background-color: #ffc0cb; /* Pink */
        border-radius: 50%;
    }

    /* Face (Worried) */
    .eye {
        position: absolute;
        top: 10px;
        width: 14px;
        height: 14px;
        background-color: #fff;
        border-radius: 50%;
        z-index: 7;
        border: 1px solid #555;
    }
    .eye.l {
        left: 10px;
    }
    .eye.r {
        right: 10px;
    }
    .pupil {
        position: absolute;
        top: 4px;
        left: 4px;
        width: 4px;
        height: 4px;
        background-color: #000;
        border-radius: 50%;
    }

    .nose {
        position: absolute;
        top: 25px;
        left: 21px;
        width: 8px;
        height: 6px;
        background-color: #000;
        border-radius: 50%;
        z-index: 7;
    }

    .mouth {
        position: absolute;
        top: 35px;
        left: 20px;
        width: 10px;
        height: 4px;
        background-color: #333;
        border-radius: 5px;
    }

    .sweat {
        position: absolute;
        top: 0px;
        right: 0px;
        width: 10px;
        height: 15px;
        background-color: #00bfff;
        border-radius: 0 50% 50% 50%;
        transform: rotate(45deg);
        animation: sweatDrop 1s infinite;
        opacity: 0.8;
        z-index: 10;
    }

    @keyframes sweatDrop {
        0% {
            top: 0px;
            opacity: 1;
        }
        100% {
            top: 10px;
            opacity: 0;
        }
    }

    .tail {
        position: absolute;
        bottom: 5px;
        right: -10px;
        width: 30px;
        height: 5px;
        background-color: #a0522d;
        border-radius: 5px;
        transform: rotate(-20deg);
        z-index: 3;
        transform-origin: left;
        animation: tailWag 0.2s infinite alternate;
    }

    @keyframes tailWag {
        from {
            transform: rotate(-20deg);
        }
        to {
            transform: rotate(10deg);
        }
    }

    :global(.obstacle) {
        position: absolute;
        font-size: 45px;
        text-align: center;
        line-height: 45px;
        z-index: 15;
        filter: drop-shadow(2px 2px 2px rgba(0, 0, 0, 0.3));
    }
</style>
