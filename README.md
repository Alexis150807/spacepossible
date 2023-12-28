<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flappy Bird-like Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #87CEEB; /* Sky blue background */
        }

        #game-container {
            position: relative;
            height: 100vh;
            background-color: #87CEEB; /* Sky blue background */
        }

        #bird {
            position: absolute;
            bottom: 50%;
            left: 50px;
            width: 40px;
            height: 40px;
            background-color: yellow; /* Bird color */
        }

        .obstacle {
            position: absolute;
            bottom: 0;
            width: 60px;
            height: 200px;
            background-color: green; /* Obstacle color */
        }
    </style>
</head>
<body>
    <div id="game-container">
        <div id="bird"></div>
    </div>

    <script>
        const bird = document.getElementById('bird');
        const gameContainer = document.getElementById('game-container');
        let isJumping = false;

        document.addEventListener('keydown', (event) => {
            if (event.code === 'Space' && !isJumping) {
                jump();
            }
        });

        function jump() {
            isJumping = true;
            let jumpHeight = 80;
            let jumpDuration = 500;

            let startTime;

            function animateJump(timestamp) {
                if (!startTime) startTime = timestamp;

                const elapsedTime = timestamp - startTime;
                const progress = elapsedTime / jumpDuration;

                if (progress < 1) {
                    const newY = jumpHeight * 4 * progress * (1 - progress);
                    bird.style.bottom = newY + 'px';
                    requestAnimationFrame(animateJump);
                } else {
                    bird.style.bottom = '0';
                    isJumping = false;
                }
            }

            requestAnimationFrame(animateJump);
        }

        function createObstacle() {
            const obstacle = document.createElement('div');
            obstacle.className = 'obstacle';
            obstacle.style.left = '100%';
            obstacle.style.animationDuration = `${Math.random() * 2 + 1}s`; // Random duration
            gameContainer.appendChild(obstacle);

            // Remove obstacle when it is out of the screen
            obstacle.addEventListener('animationiteration', () => {
                obstacle.remove();
            });
        }

        function checkCollision() {
            const birdRect = bird.getBoundingClientRect();
            const obstacles = document.querySelectorAll('.obstacle');

            obstacles.forEach(obstacle => {
                const obstacleRect = obstacle.getBoundingClientRect();

                if (
                    birdRect.bottom > obstacleRect.top &&
                    birdRect.top < obstacleRect.bottom &&
                    birdRect.right > obstacleRect.left &&
                    birdRect.left < obstacleRect.right
                ) {
                    alert('Game Over!');
                    location.reload(); // Reload the page for a simple restart
                }
            });
        }

        function gameLoop() {
            checkCollision();
            createObstacle();
            requestAnimationFrame(gameLoop);
        }

        gameLoop();
    </script>
</body>
</html>

  

