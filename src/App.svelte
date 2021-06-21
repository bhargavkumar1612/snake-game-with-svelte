<script>
	let gridSize = 20;
	let snakeHead = [parseInt(gridSize/2),parseInt(gridSize/2)];
	let grid = [...Array(gridSize)].map(()=>[...Array(gridSize)].map(()=>'empty'))
	let direction = [0,1]
	let moveTime = 350;
	let dead = false;
	let foodPos = [0,0]
	let foodIn = false;
	let score = 0;
	let snakeBody = [snakeHead, [snakeHead[0],snakeHead[1]-1],[snakeHead[0],snakeHead[1]-2]]
	let Interval
	function move(){
		Interval = setInterval(()=>{
			if (snakeHead[0]>=0 && snakeHead[0] <= gridSize-1 && snakeHead[1]>=0 && snakeHead[1] <= gridSize-1){
				grid[snakeBody[snakeBody.length-1][0]][snakeBody[snakeBody.length-1][1]] = 'empty'
				snakeBody = [[snakeBody[0][0]+direction[0], snakeBody[0][1]+direction[1]], ...snakeBody.slice(0,snakeBody.length-1)]
				snakeHead = snakeBody[0]
				if (snakeBody.slice(1,snakeBody.length).some((cell)=>{
					return snakeHead[0] === cell[0] && snakeHead[1] === cell[1] 
				})){
					clearInterval(Interval);
					dead = true;
				}
			}else{
				clearInterval(Interval);
				dead = true;
			}
		}, moveTime)
	}
	move()
	function getRandomNumber(){
		return Math.floor(Math.random()*(gridSize));
	}
	function giveFood(){
		if (!foodIn){
			while (true){
				foodPos = [getRandomNumber(), getRandomNumber()]
				if (!snakeBody.some((cell)=>{
					return foodPos[0] === cell[0] && foodPos[1] === cell[1]
				})){
					break;
				}
			}
			grid[foodPos[0]][foodPos[1]] = 'food'
			foodIn = true;
		}
	}

	function speedIncrement(){
		return 350 - 250*(1-Math.exp(-score/30))
	}

	function eatFood(){
		if (snakeHead[0]===foodPos[0] && snakeHead[1]===foodPos[1]){
			score+=1;
			foodIn=false;
			snakeBody.push(foodPos);
			clearInterval(Interval)
			moveTime = speedIncrement()
			move()
		}
		
	}
	function makeSnakeBody(){
		for (let [r,c] of snakeBody){
			grid[r][c] = 'snake'
		}
		grid[snakeHead[0]][snakeHead[1]] = 'snakeHead'
	}
	$: {
		if (snakeHead[1]<gridSize && snakeHead[1]>=0 && snakeHead[0]<gridSize && snakeHead[0]>=0){
			makeSnakeBody()
		}
		giveFood()
		eatFood()
	}

	function restart(){
		if (dead){
			snakeHead = [parseInt(gridSize/2),parseInt(gridSize/2)];
			grid = [...Array(gridSize)].map(()=>[...Array(gridSize)].map(()=>'empty'))
			direction = [0,1]
			foodIn = false;
			moveTime = 350;
			score = 0;
			snakeBody = [snakeHead, [snakeHead[0],snakeHead[1]-1],[snakeHead[0],snakeHead[1]-2]]
			dead = false;
			move()
		}
		
	}

// touch event handling functions


// window.addEventListener('load', function(){
 
//  var touchsurface = document.getElementById('game-container'),
// 	 startX,
// 	 startY,
// 	 dist,
// 	 threshold = 150, //required min distance traveled to be considered swipe
// 	 allowedTime = 200, // maximum time allowed to travel that distance
// 	 elapsedTime,
// 	 startTime

//  function handleswipe(isrightswipe){
// 	 if (isrightswipe)
// 		 touchsurface.innerHTML = 'Congrats, you\'ve made a <span style="color:red">right swipe!</span>'
// 	 else{
// 		 touchsurface.innerHTML = 'Condition for right swipe not met yet'
// 	 }
//  }

//  touchsurface.addEventListener('touchstart', function(e){
// 	 touchsurface.innerHTML = ''
// 	 var touchobj = e.changedTouches[0]
// 	 dist = 0
// 	 startX = touchobj.pageX
// 	 startY = touchobj.pageY
// 	 startTime = new Date().getTime() // record time when finger first makes contact with surface
// 	 e.preventDefault()
//  }, false)

//  touchsurface.addEventListener('touchmove', function(e){
// 	 e.preventDefault() // prevent scrolling when inside DIV
//  }, false)

//  touchsurface.addEventListener('touchend', function(e){
// 	 var touchobj = e.changedTouches[0]
// 	 dist = touchobj.pageX - startX // get total dist traveled by finger while in contact with surface
// 	 elapsedTime = new Date().getTime() - startTime // get time elapsed
// 	 // check that elapsed time is within specified, horizontal dist traveled >= threshold, and vertical dist traveled <= 100
// 	 var swiperightBol = (elapsedTime <= allowedTime && dist >= threshold && Math.abs(touchobj.pageY - startY) <= 100)
// 	 handleswipe(swiperightBol)
// 	 e.preventDefault()
//  }, false)

// }, false) // end window.onload

</script>

<body>
<div class="game-container" id="game-container">
	<div class="snake-box">
		<h1 class="title">Snake game</h1>
		{#each grid as row}
			<div class='row'>
				{#each row as cell}
					<div class={`cell ${cell=='empty'? 'empty': (cell=='snake'? 'snake': (cell=='food'? 'food' : 'snake-head' ))}`}></div>
				{/each}
			</div>
		{/each}
	</div>
	
	<div class='md-move-buttons'>
		{#if dead}
			<h2 class='dead'>You Lost!!</h2>
			<button class="md-restart-button" on:click={restart}>Restart</button>
		{:else}
			<div class="single-btn">
				<button class="dir-btn" on:click={()=>{direction=[-1, 0]}}>{"^"}</button>
			</div>
			<div class='double-btn'>
				<button class="dir-btn-double" on:click={()=>{direction=[0,-1]}}>{"<"}</button>
				<button class="dir-btn-double" on:click={()=>{direction=[0,1]}}>{">"}</button>
			</div>
			<div class="single-btn">
				<button class="dir-btn" on:click={()=>{direction=[1, 0]}}>{"v"}</button>
			</div>
		{/if}
	</div>
	<div class='score-card'>
		<h1>Score <span class="score">{score}</span></h1>
		{#if dead}
			<h2 class='dead-lg'>You Lost!!</h2>
			<h2 class='lg-info'>Hit ENTER to restart.</h2>
		{/if}
	</div>
</div>
</body>

<style>
	.game-container{
		display: flex;
		justify-content: center;
		width: 100%;
		background-color: #2f456789;
		height: 100%;
	}
	.md-move-buttons{
		display: none;
	}
	.snake-box{
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		max-width: 70%;
		margin-right: 10%;
	}
	
	.row{
		display: flex;
	}
	.cell{
		height: 20px;
		width: 20px;
		margin: .5px;
	}
	.empty{
		border: 1px solid black;
		background-color: rgb(72, 134, 189);
	}
	.snake{
		border: 1px solid black;
		background-color: rgb(157, 219, 133);
	}
	.snake-head{
		border: 1px solid black;
		background-color: rgb(39, 233, 39);
	}
	.food{
		border: 1px solid black;
		background-color: rgb(248, 248, 226);
		border-radius: 50%;
	}
	
	.score-card{
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		max-width: 30%;
	}
	.title{
		color: white;
		font-size: 30px;
	}
	.score{
		color: rgb(231, 193, 193);
		font-size: 30px;
	}
	.dead{
		color: red;
	}
	.dead-lg{
		color: red;
	}
	.lg-info{
		color: green;
	}
	@media (max-width: 770px){
		.game-container{
			display: block;
		}
		.snake-box{
			max-width: 100%;
		}
		.score-card{
			max-width: 100%;
			order: -1;
		}
		.cell{
			height: 10px;
			width: 10px;
			margin: .5px;
		}
		.lg-info{
			display: none;
		}
		.md-restart-button{
			color: green;
			border: 0;
			padding: 10px;
			font-size: 20px;
			font-weight: 600;
			background-color: antiquewhite;
		}
		.md-move-buttons{
			display:block;
			display:flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			padding: 20px;
		}
		.dir-btn{
			width: 50px;
			height: 50px;
			color: black;
			font-weight: 900;
			font-size: 20px;
			background-color: antiquewhite;
		}
		.dir-btn-double{
			width: 50px;
			height: 50px;
			margin-right: 25px;
			margin-left: 25px;
			color: black;
			font-weight: 900;
			font-size: 20px;
			background-color: antiquewhite;
		}
		.dead-lg{
			display: none;
		}
	}
</style>

<svelte:window
  on:keydown={(e) => {
    switch (e.key) {
      case 'ArrowLeft':
        direction = [0, -1];
        break;
      case 'ArrowRight':
        direction = [0, 1];
        break;
      case 'ArrowUp':
        direction = [-1, 0];
        break;
      case 'ArrowDown':
        direction = [1, 0];
        break;
        case 'a':
        direction = [0, -1];
        break;
      case 'd':
        direction = [0, 1];
        break;
      case 'w':
        direction = [-1, 0];
        break;
      case 's':
        direction = [1, 0];
        break;
      case 'Enter':
        restart();
        break;
    }
  }} />