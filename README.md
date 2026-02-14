<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Catch The Hearts 💘</title>

<style>
body {
  margin: 0;
  overflow: hidden;
  font-family: Arial, sans-serif;
  background: #ff6f91;
  color: white;
}

/* Floating flowers */
#flowers {
  position: fixed;
  width: 100%;
  height: 100%;
  z-index: -3;
}

.flower {
  position: absolute;
  font-size: 28px;
  opacity: 0.3;
  animation: float linear infinite;
}

@keyframes float {
  from { transform: translateY(100vh); }
  to { transform: translateY(-100vh); }
}

/* Word reveal */
#wordReveal {
  position: fixed;
  top: 40%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 38px;
  font-weight: bold;
  opacity: 0;
  transition: opacity 0.5s ease;
  text-align: center;
  text-shadow: 0 0 10px rgba(255,255,255,0.8);
  z-index: -1;
}

/* Final message */
#finalMessage {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 44px;
  font-weight: bold;
  color: pink;
  text-align: center;
  text-shadow:
    0 0 10px #ff69b4,
    0 0 20px #ff1493,
    0 0 30px #ff1493;
  display: none;
}

/* Basket */
#basket {
  position: absolute;
  bottom: 130px;
  width: 110px;
  height: 70px;
  background: linear-gradient(145deg, #ffb6d9, #ff69b4);
  border-radius: 0 0 35px 35px;
  border: 4px solid #ff4fa3;
  box-shadow: 0 8px 20px rgba(0,0,0,0.25);
  overflow: hidden;
}

#basket::after {
  content: "";
  position: absolute;
  top: -18px;
  left: -5px;
  width: 120px;
  height: 35px;
  background: linear-gradient(to bottom, #ffc1e3, #ff69b4);
  border-radius: 50%;
  border: 4px solid #ff4fa3;
}

.heart {
  position: absolute;
  font-size: 30px;
  transition: transform 0.2s ease, opacity 0.2s ease;
}
</style>
</head>
<body>

<div id="flowers"></div>
<div id="wordReveal"></div>
<div id="finalMessage">Happy Valentine's Day Baby 💖</div>
<div id="basket"></div>

<script>
let basket = document.getElementById("basket");
let wordReveal = document.getElementById("wordReveal");
let finalMessage = document.getElementById("finalMessage");
let flowerLayer = document.getElementById("flowers");

let basketX = window.innerWidth / 2 - 55;
basket.style.left = basketX + "px";

document.addEventListener("mousemove", e => basketX = e.clientX - 55);
document.addEventListener("touchmove", e => basketX = e.touches[0].clientX - 55);

let hearts = [];
let collectedHearts = 0;
let gameActive = true;

/* Sentence words */
let sentence = "So let's start this. It took a long time to make but i hope you're enjoying this, I'm glad we're together and love you so so so much for it.";
let words = sentence.split(" ");
let wordIndex = 0;

/* Word queue */
let wordQueue = [];
let wordDisplaying = false;

/* Background flowers */
for (let i = 0; i < 50; i++) {
  let flower = document.createElement("div");
  flower.classList.add("flower");
  flower.innerHTML = ["🌸","🌺","🌷","🌹"][Math.floor(Math.random()*4)];
  flower.style.left = Math.random() * 100 + "vw";
  flower.style.animationDuration = (6 + Math.random() * 8) + "s";
  flowerLayer.appendChild(flower);
}

function createHeart() {
  if (!gameActive) return;

  let heart = document.createElement("div");
  heart.classList.add("heart");
  heart.innerHTML = "💖";
  heart.style.left = Math.random() * (window.innerWidth - 30) + "px";
  heart.style.top = "0px";
  document.body.appendChild(heart);

  hearts.push({
    element: heart,
    x: parseFloat(heart.style.left),
    y: 0,
    speed: 2.2
  });
}

/* Word reveal queue processor */
function processQueue() {
  if (wordDisplaying || wordQueue.length === 0) return;
  wordDisplaying = true;
  let word = wordQueue.shift();
  wordReveal.innerText = word;
  wordReveal.style.opacity = "1";

  setTimeout(() => {
    wordReveal.style.opacity = "0";
    setTimeout(() => {
      wordDisplaying = false;
      processQueue();
    }, 200); // tiny gap before next word
  }, 1000);
}

function revealWord() {
  if (wordIndex >= words.length) {
    endGame();
    return;
  }
  wordQueue.push(words[wordIndex]);
  wordIndex++;
  processQueue();
}

function endGame() {
  gameActive = false;
  finalMessage.style.display = "block";

  // Flowers and small fireworks
  for (let i = 0; i < 60; i++) {
    let burst = document.createElement("div");
    burst.classList.add("heart");
    burst.innerHTML = ["🌸","💐","✨","🌷"][Math.floor(Math.random()*4)];
    burst.style.left = Math.random() * 100 + "vw";
    burst.style.top = Math.random() * 100 + "vh";
    document.body.appendChild(burst);
    setTimeout(() => burst.remove(), 1200);
  }
}

function updateGame() {
  if (!gameActive) return;

  basket.style.left = basketX + "px";

  for (let i = hearts.length - 1; i >= 0; i--) {
    let heart = hearts[i];

    heart.y += heart.speed;
    heart.element.style.top = heart.y + "px";

    if (
      heart.y > window.innerHeight - 210 &&
      heart.x > basketX &&
      heart.x < basketX + 110
    ) {
      collectedHearts++;

      if (collectedHearts > 3) {
        revealWord();
      }

      heart.element.style.transform = "scale(1.8)";
      heart.element.style.opacity = "0";
      setTimeout(() => heart.element.remove(), 200);

      hearts.splice(i, 1);
    }

    if (heart.y > window.innerHeight) {
      heart.element.remove();
      hearts.splice(i, 1);
    }
  }

  requestAnimationFrame(updateGame);
}

setInterval(createHeart, 1200); // slower spawn
updateGame();
</script>

</body>
</html>
