<script>
    import { onMount, onDestroy } from 'svelte';
    onMount(() => {
        reset();
        document.addEventListener('keydown', listenForKeyboard);
    });
    onDestroy(() => {
        document.removeEventListener('keydown', listenForKeyboard);
    });

    const canvasSize = 480;
    const snakeSize = 20;
    const GAME = {
        INIT: 'INITIALIZING',
        READY: 'GAME_READY',
        SETUP: 'GAME_SETUP',
        PLAYING: 'GAME_PLAYING',
        OVER: 'GAME_OVER',
    };
    const DIRECTION = {
        RIGHT: 'RIGHT',
        LEFT: 'LEFT',
        UP: 'UP',
        DOWN: 'DOWN',
    };
    const keys = {
        'arrowup': DIRECTION.UP,
        'arrowright': DIRECTION.RIGHT,
        'arrowdown': DIRECTION.DOWN,
        'arrowleft': DIRECTION.LEFT,
        'w': DIRECTION.UP,
        'a': DIRECTION.LEFT,
        's': DIRECTION.DOWN,
        'd': DIRECTION.RIGHT,
    };
    const acceptableKeys = Object.keys(keys);
    
    function listenForKeyboard(e) {
        const key = e.key.toLowerCase();
        if (acceptableKeys.includes(key)) {
            changeDirection(keys[key]);
        }
    }
    
    let gameState = GAME.INIT;
    let canvas;
    let ctx;
    let speedMs;
    let direction; 
    let bodyPositions = [];
    let fruit;
    let highScore = 0;
    $: score = (bodyPositions.length - 4) * 10;
    $: highScore = score > highScore ? score : highScore || 0;
    
    function reset() {
        if (ctx) {
            ctx.clearRect(0, 0, canvasSize, canvasSize);
        }
        speedMs = 200;
        direction = DIRECTION.RIGHT; 
        bodyPositions = [
            { x: 100, y: 100 },
            { x: 80, y: 100 },
            { x: 60, y: 100 },
        ];
        gameState = GAME.READY;
        fruit = undefined;
    }

    function setupGame() {
        canvas = document.getElementById('canvas');
        ctx = canvas.getContext('2d');
        gameState = GAME.PLAYING;
        drawSnake();
    }

    function drawSnake() {
        bodyPositions = bodyPositions.map(({ x, y }, i) => {
            const isHead = i === 0;
            let nextX, nextY;
            
            switch(direction) {
                case DIRECTION.RIGHT:
                    nextX = isHead ? x + snakeSize : bodyPositions[i - 1].x;
                    nextY = isHead ? y : bodyPositions[i - 1].y;
                    break;
                case DIRECTION.LEFT:
                    nextX = isHead ? x - snakeSize : bodyPositions[i - 1].x;
                    nextY = isHead ? y : bodyPositions[i - 1].y;
                    break;
                case DIRECTION.DOWN:
                    nextX = isHead ? x : bodyPositions[i - 1].x;
                    nextY = isHead ? y + snakeSize : bodyPositions[i - 1].y;
                    break;
                case DIRECTION.UP:
                default:
                    nextX = isHead ? x : bodyPositions[i - 1].x;
                    nextY = isHead ? y - snakeSize : bodyPositions[i - 1].y;
                    break;
            }

            let bitesSelf = false;
            if (isHead) {
                const bitten = bodyPositions.find(part => (part.x === nextX && part.y === nextY));
                bitesSelf = !!bitten;
            }
            if (
                nextX >= canvasSize || 
                nextX < 0 || 
                nextY >= canvasSize || 
                nextY < 0 ||
                bitesSelf
            ) {
                gameState = GAME.OVER;
            }

            if (isHead && fruit && nextX === fruit.x && nextY === fruit.y) {
                fruit = undefined;
            }
            
            return { x: nextX, y: nextY };
        });

        if (!fruit) {
            const currentTail = bodyPositions[bodyPositions.length - 1];
            bodyPositions = [...bodyPositions, { ...currentTail }];
            speedMs *= 0.95;
        }

        createAndDrawFruit();
        
        ctx.fillStyle = 'blue';
        bodyPositions.forEach(({ x, y }) => {
            ctx.fillRect(x, y, snakeSize, snakeSize);
        });

        setTimeout(() => {
            if (gameState !== GAME.OVER) {
                ctx.clearRect(0, 0, canvasSize, canvasSize);
                drawSnake();    
            }
        }, speedMs);
    }

    function generateFruitCoords() {
        const x = Math.floor(Math.random() * (canvasSize / snakeSize)) * snakeSize;
        const y = Math.floor(Math.random() * (canvasSize / snakeSize)) * snakeSize;
        
        const conflictsWithSnake = bodyPositions.find(part => (part.x === x && part.y === y));
        if (conflictsWithSnake) {
            return generateFruitCoords();
        }
        return { x, y };
    }

    function createAndDrawFruit() {
        if (!fruit) {
            fruit = generateFruitCoords();
        }
        ctx.fillStyle = 'red';
        ctx.fillRect(fruit.x, fruit.y, snakeSize, snakeSize);
    }

    function changeDirection(nextDirection) {
        switch(nextDirection) {
            case DIRECTION.LEFT:
                if (direction !== DIRECTION.RIGHT) {
                    direction = nextDirection;
                }
                break;
            case DIRECTION.RIGHT:
                if (direction !== DIRECTION.LEFT) {
                    direction = nextDirection;
                }
                break;
            case DIRECTION.UP:
                if (direction !== DIRECTION.DOWN) {
                    direction = nextDirection;
                }
                break;
            case DIRECTION.DOWN:
                if (direction !== DIRECTION.UP) {
                    direction = nextDirection;
                }
                break;
        }
    }
</script>

<main class="main">
    <header>
        <div>Score: {bodyPositions.length !== 3 ? score : '0'}</div>
        <div>High score: {highScore}</div>
    </header>
    <div class="canvasContainer">
        <canvas id="canvas" width={canvasSize} height={canvasSize}></canvas>
        {#if gameState === GAME.OVER || gameState === GAME.READY}
            <div class="gameDialog">
                {#if gameState === GAME.READY}
                    SNAKE
                {:else}
                    GAME OVER	
                {/if}
                <button on:click={() => { reset(); setupGame(); }}>
                    {#if gameState === GAME.READY}
                        Play
                    {:else}
                        Restart
                    {/if}
                </button>
            </div>
        {/if}
    </div>
</main>

<style>
    .main {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        margin: 0 auto;
        font-family: Arial, sans-serif;
    }
    header {
        width: 100%;
        height: 60px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 24px;
    }
    #canvas {
        border: 2px solid #000; 
        background-color: #f0f0f0; 
    }
    .canvasContainer {
        position: relative;
    }
    .gameDialog {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgba(255, 255, 255, .8);
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        font-size: 24px;
    }
    .gameDialog button{
        color: #333;
    }
</style>
