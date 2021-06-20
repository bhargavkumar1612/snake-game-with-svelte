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
			console.log('>>')
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

</script>

<div class="game-container">
	<div class="snake-box">
		<h1>Snake game</h1>
		{#each grid as row}
			<div class='row'>
				{#each row as cell}
					<div class={cell=='empty'? 'empty': (cell=='snake'? 'snake': (cell=='food'? 'food' : 'snake-head' ))}></div>
				{/each}
			</div>
		{/each}
	</div>
	<div class='score-card'>
		<h1>Score {score}</h1>
		{#if dead}
		<h2 class='dead'>You Lost!!</h2>
		<h2 class='info'>Hit ENTER to restart.</h2>
		{/if}
	</div>
</div>

<style>
	.game-container{
		display: flex;
		justify-content: center;
		width: 100%;
		height: 90vh;
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
	.empty{
		height: 20px;
		width: 20px;
		border: 1px solid black;
		margin: .5px;
	}
	.snake{
		height: 20px;
		width: 20px;
		border: 1px solid black;
		margin: .5px;
		background-color: orangered;
	}
	.food{
		height: 20px;
		width: 20px;
		border: 1px solid black;
		margin: .5px;
		background-color: yellow;
	}
	.snake-head{
		height: 20px;
		width: 20px;
		border: 1px solid black;
		margin: .5px;
		background-color: rgb(192, 53, 3);
	}
	.score-card{
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		max-width: 30%;
	}
	.dead{
		color: red;
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