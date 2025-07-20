<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Simon Game</title>
  <link rel="stylesheet" href="style simon.css" />
</head>
<body>
  <h1 id="level-title">Press A Key to Start</h1>
  <div class="container">
    <div class="btn green" id="green" type="button"></div>
    <div class="btn red" id="red" type="button"></div>
    <div class="btn yellow" id="yellow"type="button"></div>
    <div class="btn blue" id="blue" type="button"></div>
  </div>
  <script src="simon.js"></script>
</body>
</html>

body {
  text-align: center;
  background-color: #000;
  color: white;
  font-family: 'Arial', sans-serif;
}

h1 {
  margin-top: 40px;
  font-size: 2rem;
}

.container {
  margin: 100px auto;
  width: 300px;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.btn {
  width: 140px;
  height: 140px;
  border-radius: 20px;
  cursor: pointer;
  opacity: 0.8;
  transition: 0.2s;
}

.green { background-color: green; }
.red { background-color: red; }
.yellow { background-color: yellow; }
.blue { background-color: blue; }

.pressed {
  box-shadow: 0 0 20px white;
  opacity: 1;
}


const buttonColors = ["red", "blue", "green", "yellow"];
let gamePattern = [];
let userClickedPattern = [];

let level = 0;
let started = false;

document.addEventListener("keydown", function () {
  if (!started) {
    document.getElementById("level-title").textContent = "Level " + level;
    nextSequence();
    started = true;
  }
});

document.querySelectorAll(".btn").forEach(btn => {
  btn.addEventListener("click", function () {
    const userChosenColor = this.id;
    userClickedPattern.push(userChosenColor);

    playSound(userChosenColor);
    animatePress(userChosenColor);

    checkAnswer(userClickedPattern.length - 1);
  });
});

function nextSequence() {
  userClickedPattern = [];
  level++;
  document.getElementById("level-title").textContent = "Level " + level;

  const randomColor = buttonColors[Math.floor(Math.random() * 4)];
  gamePattern.push(randomColor);

  setTimeout(() => {
    flashButton(randomColor);
    playSound(randomColor);
  }, 500);
}

function flashButton(color) {
  const button = document.getElementById(color);
  button.classList.add("pressed");
  setTimeout(() => button.classList.remove("pressed"), 200);
}

function playSound(name) {
  const audioUrl = name === "wrong"
    ? "https://s3.amazonaws.com/adam-recvlohe-sounds/error.wav"
    : `https://s3.amazonaws.com/freecodecamp/simonSound${buttonColors.indexOf(name) + 1}.mp3`;

  const audio = new Audio(audioUrl);
  audio.play();
}

function animatePress(currentColor) {
  const btn = document.getElementById(currentColor);
  btn.classList.add("pressed");
  setTimeout(() => btn.classList.remove("pressed"), 100);
}

function checkAnswer(currentIndex) {
  if (gamePattern[currentIndex] === userClickedPattern[currentIndex]) {
    if (userClickedPattern.length === gamePattern.length) {
      setTimeout(() => nextSequence(), 1000);
    }
  } else {
    playSound("wrong");
    document.body.classList.add("game-over");
    document.getElementById("level-title").textContent = "Game Over! Press Any Key to Restart";
    setTimeout(() => {
      document.body.classList.remove("game-over");
    }, 200);

    startOver();
  }
}

function startOver() {
  level = 0;
  gamePattern = [];
  started = false;
}

