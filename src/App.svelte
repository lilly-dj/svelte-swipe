
<style>
:global(body) {
	margin: 0;
	padding: 0;
	overflow: hidden;
}
.items {
	margin: 80px 0;
	max-width: 100%;
	overflow-x: hidden;
	background: green
}
.item {
	width: 100%;
	padding: 15px 40px;
	box-sizing: border-box;
	background: white;
	transform: translate3d(var(--left), 0, 0);
	display: flex;
	justify-content: flex-start;
	align-items: center;
	position: relative;
	z-index: 10;
}
.item:before {
	content: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' width='24' height='24' stroke='white' stroke-width='2' fill='none' stroke-linecap='round' stroke-linejoin='round' class='css-i6dzq1'%3E%3Cpolyline points='21 8 21 21 3 21 3 8'%3E%3C/polyline%3E%3Crect x='1' y='3' width='22' height='5'%3E%3C/rect%3E%3Cline x1='10' y1='12' x2='14' y2='12'%3E%3C/line%3E%3C/svg%3E");
	display: block;
	position: absolute;
	left: calc(var(--left) / 2 * -1);
	top: 50%;
	transform: translate(-50%, -50%);
	z-index: -10;
	color: white;
	transition: transform 150ms;
	opacity: 0;
	transition: opacity 150ms;
}
.item.showAside:before {
	opacity: 1;
}
.item:nth-child(odd) {
	background: rgb(247, 247, 255);
}
</style>

<div class="items">
	{#each items as item, i}
		<div class="item {item.position.left > 100 ? "showAside" : ""}"
		on:touchstart={(event) => startDrag(event, i)} 
		on:touchend={(event) => endDrag(event, i)}
		on:touchmove={(event) => dragMove(event, i)}
		style="--left: {item.position.left}px"
		>
			{item.data.name}: {item.touching}
		</div>
	{/each}
</div>

<script>
	let items = [];

	function startDrag(evt, index) {
		console.log(evt);

		let touch = evt.touches[0];

		items[index].touching = true;
		items[index].position = {
			...items[index].position,
			left: 0,
			leftStart: touch.clientX
		}
	}

	function dragMove(evt, index) {

		let touch = evt.touches[0];

		let calc = touch.clientX - items[index].position.leftStart;
		items[index].position.left = calc > 0 ? calc : 0;
	}

	function endDrag(evt, index) {
		items[index].touching = false;
		items[index].position.left = 0;
	}

	fetch("https://jsonplaceholder.typicode.com/users").then(d => d.json()).then(array => {
		items = array.map((obj, index) => ({
			data: obj,
			touching: false,
			position: {
				left: 0,
				top: 49 * index
			}
		}));
	});

</script>