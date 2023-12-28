<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alien Jump Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
        }

        #game-container {
            position: relative;
            height: 100vh;
            background-color: #000;
            overflow: hidden;
        }

        #alien {
            position: absolute;
            bottom: 0;
            left: 50px;
            width: 50px;
            height: 50px;
            background-color: green;
            transition: bottom 0.5s ease-out; /* Smoothing the jump animation */
        }

        .planet {
            position: absolute;
            width: 100px;
            height: 100px;
            background-color: blue;
        }
    </style>
</head>
<body>
    <div id="game-container">
        <div id="alien"></div>
        <!-- Initial planets -->
        <div class="planet" style="bottom: 50px; left: 200px;"></div>
        <div class="planet" style="bottom: 50px; left: 400px;"></div>
        <div class="planet" style="bottom: 50px; left: 600px;"></div>
    </div>

    <script>
        const alien = document.getElementById('alien');
        const gameContainer = document.getElementById('game-container');
        let planets = document.querySelectorAll('.planet');

        let isJumping = false;

        document.addEventListener('keydown', (event) => {
            if (event.code === 'Space' && !isJumping) {
                jump();
            }
        });

        function jump() {
            isJumping = true;
            const jumpHeight = 100;
            const jumpDuration = 500;
            let startTime;

            function animateJump(timestamp) {
                if (!startTime) startTime = timestamp;

                const elapsedTime = timestamp - startTime;
                const progress = elapsedTime / jumpDuration;

                if (progress < 1) {
                    const newY = jumpHeight * 4 * progress * (1 - progress);
                    alien.style.bottom = newY + 'px';
                    requestAnimationFrame(animateJump);
                } else {
                    alien.style.bottom = '0';
                    isJumping = false;
                }
            }

            requestAnimationFrame(animateJump);
        }

        function addPlanet() {
            const newPlanet = document.createElement('div');
            newPlanet.className = 'planet';
            newPlanet.style.bottom = '50px';
            newPlanet.style.left = `${Math.random() * (window.innerWidth - 100)}px`; // Random x position
            gameContainer.appendChild(newPlanet);
            planets = document.querySelectorAll('.planet'); // Update planets NodeList
        }

        function removePassedPlanets() {
            planets.forEach(planet => {
                const planetRect = planet.getBoundingClientRect();
                if (planetRect.bottom < 0) {
                    planet.remove();
                    addPlanet(); // Add a new planet when one is removed
                }
            });
        }

        function checkCollision() {
            const alienRect = alien.getBoundingClientRect();

            planets.forEach(planet => {
                const planetRect = planet.getBoundingClientRect();

                if (
                    alienRect.bottom > planetRect.top &&
                    alienRect.top < planetRect.bottom &&
                    alienRect.right > planetRect.left &&
                    alienRect.left < planetRect.right
                ) {
                    // Jump successful, place the alien on top of the planet
                    alien.style.bottom = planetRect.bottom + 'px';
                    isJumping = false;
                }
            });
        }

        function gameLoop() {
            checkCollision();
            removePassedPlanets();
            requestAnimationFrame(gameLoop);
        }

        gameLoop();
    </script>
</body>
</html>
