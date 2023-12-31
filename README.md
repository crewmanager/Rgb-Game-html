## Rgb Game


`main.mo`

```js
import Int "mo:base/Int";


actor {
  var attempts : Int = 0;
  var rightGuess : Int = 0;
  var wrongGuess : Int = 0;

  public func attemptsRightWrong(a : Int, r : Int, w : Int) {
    attempts := a;
    rightGuess := r;
    wrongGuess := w;
  };

  public query func getAttempts() : async Int{
    return attempts;
  };

public query func getRightGuess() : async Int{
    return rightGuess;
  };

  public query func getWrongGuess() : async Int{
    return wrongGuess;
  }

};
```

`main.css`

```css
body {
	background-color: #232323;
	font-family: "Raleway", Sans-serif;
	font-size: 62.5%; /*1 em = 10px */
	margin: 0;
}

#container {
	max-width: 600px;
	margin: 0 auto;
	padding-top: 20px;

}
.square {
	width: 30%;
	background-color: purple;
	padding-bottom: 30%;
	float: left;
	margin: 1.66%;
	transition: background .5s;
	-webkit-transition: background .5s;
	-moz-transition: background .5s;
	o-transition: background .5s;
} 

h1 {
	color: white;
	background-color: #2C8E99;
	font-size: 3.5em;
	text-align: center;
	text-transform: uppercase;
	margin: 0;
	padding: 10px 0;
}

#color-display {
	display: block;
	font-size: 2em;
}

#stripe {
	background: white;
	height: 30px;
	text-align: center;
	color: black;
}

#message {
	text-transform: uppercase;
	color: #2C8E99;
	font-size: 1.5em;
	display: inline-block;
	width: 20%;
}

button {
	outline: none;
	color: #2C8E99;
	font-family: "Raleway", Sans-serif;
	text-transform: uppercase;
	font-size: 1.5em;
	background-color: white;
	height: 100%;
	letter-spacing: 1px;
	transition: all 0.3s;
	-webkit-transition: all 0.3s;
	-moz-transition: all 0.3s;
	o-transition: all 0.3s;
}

button:hover {
	background-color: #2C8E99;
	color: white;
}

.selected {
	background-color: #2C8E99;
	color: white;
}

.square {
	border-radius: 20%;
}

* {
	border: 0px solid red;
}
```
`index.html`

```html
<html>
<head>
	<title>Color Game</title>
	<link rel="stylesheet" type="text/css" href="main.css">
	<link href='https://fonts.googleapis.com/css?family=Raleway:300' rel='stylesheet' type='text/css'>	
<style>
	 #score {
            font-size: 24px;
            font-weight: bold;
            /* color: #4CAF50;  */
			color: white;
			/* Green color */
            margin-top: 420px;
			margin-left : 550px;
        }
</style>
	

</head>
<body>
	<h1>The Great <span id="color-display">RGB</span> Guessing Game</h1>
	<div id ="stripe">
		<button id="reset">New Colors</button>
		<span id="message"></span>
		<button class="mode">Easy</button>
		<button class="mode selected">Hard</button>
	</div>
	<div id="container">
		<div class="square"></div>
		<div class="square"></div>
		<div class="square"></div>
		<div class="square"></div>
		<div class="square"></div>
		<div class="square"></div>
	</div>
  <div id="score"></div>

	<!-- <script type="text/javascript" src="script.js"></script> -->
<!-- 	<script type="text/javascript" src="colorGame.js"></script>
 --></body>
</html>
```

`index.js`

```js
import { rgb_backend } from '../../declarations/rgb_backend';


var numSquares = 6;
var colors = [];
var pickedColor;
var attempts = 0;
var rightGuess = 0;
var wrongGuess = 0;



var squares = document.querySelectorAll(".square");
var colorDisplay = document.querySelector("#color-display");
var messageDisplay = document.querySelector("#message");
var h1 = document.querySelector("h1");
var resetButton = document.querySelector("#reset");
var modeButtons = document.querySelectorAll(".mode");
var easyButton = document.querySelector(".mode");

init();

function init() {
	colorDisplay.textContent = pickedColor;
	setupSquares();
	setupMode();
	reset();
}

resetButton.addEventListener("click", function () {
	reset();
});

const score = document.getElementById("score");


function setupSquares() {
	for (var i = 0; i < squares.length; i++) {
		squares[i].style.backgroundColor = colors[i];
		squares[i].addEventListener("click", function () {
			var clickedColor = this.style.backgroundColor;
			if (clickedColor === pickedColor) {
				messageDisplay.textContent = "Correct";
				resetButton.textContent = "Play Again";
				changeColors(pickedColor);
				attempts = attempts + 1;
				rightGuess++
				rgb_backend.attemptsRightWrong(attempts, rightGuess, wrongGuess);
				score.textContent = "Attempts: "+ attempts+ " Right Guess: "+ rightGuess + "   Wrong Guess: " + wrongGuess;
				console.log(rightGuess, wrongGuess, attempts, 'r', 'w', 'a');
			}
			else {
				this.style.backgroundColor = "#232323";
				messageDisplay.textContent = "try again";
				attempts++;
				wrongGuess++;
				rgb_backend.attemptsRightWrong(attempts, rightGuess, wrongGuess);
				score.textContent = "Attempts: "+ attempts+ " Right Guess: "+ rightGuess + "   Wrong Guess: " + wrongGuess;
				console.log(rightGuess, wrongGuess, attempts, 'r', 'w', 'a');
			}
		});
	}
}




function setupMode() {
	for (var i = 0; i < modeButtons.length; i++) {
		modeButtons[i].addEventListener("click", function () {
			for (var i = 0; i < modeButtons.length; i++) {
				modeButtons[i].classList.remove("selected");
			}
			this.classList.add("selected");
			if (this.textContent === "Easy") {
				numSquares = 3;
			}
			else {
				numSquares = 6;
			}
			reset();
		});
	}
}

function reset() {
	colors = genRandomColors(numSquares);
	pickedColor = chooseColor();
	colorDisplay.textContent = pickedColor;
	h1.style.backgroundColor = "#2C8E99";
	resetButton.textContent = "New Colors";
	messageDisplay.textContent = "";
	for (var i = 0; i < squares.length; i++) {
		if (colors[i]) {
			squares[i].style.display = "block";
			squares[i].style.backgroundColor = colors[i];
		}
		else {
			squares[i].style.display = "none";
		}
	}
}

function changeColors(color) {
	for (var i = 0; i < squares.length; i++) {
		squares[i].style.backgroundColor = color;
		h1.style.backgroundColor = color;
	}
}

function chooseColor() {
	var random = Math.floor(Math.random() * colors.length);
	return colors[random];
}

function genRandomColors(num) {
	var arr = [];
	for (var i = 0; i < num; i++) {
		arr.push(makeColor());
	}
	return arr;
}

function makeColor() {
	var r = Math.floor(Math.random() * 256);
	var g = Math.floor(Math.random() * 256);
	var b = Math.floor(Math.random() * 256);
	return "rgb(" + r + ", " + g + ", " + b + ")";
}




console.log("right", rightGuess);
console.log("wrong", wrongGuess);
console.log("attempts", attempts);


console.log(rightGuess, wrongGuess, attempts, 'r', 'w', 'a');

```


