# blop-dash
<!DOCTYPE html>
<html>
<body style="margin:0; overflow:hidden; background:#222;">
    <canvas id="gameCanvas"></canvas>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        // Variabel Game
        let player = { x: 100, y: 0, w: 40, h: 40, dy: 0, jump: -12, gravity: 0.6, onGround: false };
        let obstacles = [];
        let score = 0;
        let gameOver = false;

        // Fungsi Lompat
        window.addEventListener("keydown", (e) => {
            if (e.code === "Space" && player.onGround) {
                player.dy = player.jump;
                player.onGround = false;
            }
            if (gameOver) location.reload(); // Restart jika game over
        });

        function update() {
            if (gameOver) return;

            // Gravitasi & Gerak Pemain
            player.dy += player.gravity;
            player.y += player.dy;

            // Batas Lantai
            let groundY = canvas.height - 100;
            if (player.y + player.h > groundY) {
                player.y = groundY - player.h;
                player.dy = 0;
                player.onGround = true;
            }

            // Munculkan Rintangan (Segitiga)
            if (Math.random() < 0.02) {
                obstacles.push({ x: canvas.width, y: groundY - 40, w: 40, h: 40 });
            }

            // Update Rintangan
            obstacles.forEach((obs, index) => {
                obs.x -= 8; // Kecepatan gerak rintangan

                // Deteksi Tabrakan
                if (player.x < obs.x + obs.w && player.x + player.w > obs.x &&
                    player.y < obs.y + obs.h && player.y + player.h > obs.y) {
                    gameOver = true;
                }

                // Hapus rintangan yang lewat
                if (obs.x + obs.w < 0) {
                    obstacles.splice(index, 1);
                    score++;
                }
            });
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Gambar Lantai
            ctx.fillStyle = "#555";
            ctx.fillRect(0, canvas.height - 100, canvas.width, 100);

            // Gambar Pemain (Kotak)
            ctx.fillStyle = "#00FF00";
            ctx.fillRect(player.x, player.y, player.w, player.h);

            // Gambar Rintangan (Merah)
            ctx.fillStyle = "#FF0000";
            obstacles.forEach(obs => ctx.fillRect(obs.x, obs.y, obs.w, obs.h));

            // Teks Skor
            ctx.fillStyle = "white";
            ctx.font = "30px Arial";
            ctx.fillText(`Score: ${score}`, 50, 50);

            if (gameOver) {
                ctx.fillText("GAME OVER! Press Any Key to Restart", canvas.width/2 - 250, canvas.height/2);
            }
        }

        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        gameLoop();
    </script>
</body>
</html>

