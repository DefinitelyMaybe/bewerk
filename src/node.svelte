<script>
	import { scale } from 'svelte/transition'
	import RadialMenu from './ui/radialmenu.svelte';
	
	export let id = 0
	export let selected = false
	let openmenu = false

	export let x = 0
	export let y = 0
	let xOffSet = 0
	let yOffSet = 0

	function handleDragStart(event) {
		//calc offset
		xOffSet = event.offsetX
		yOffSet = event.offsetY

		event.dataTransfer.dropEffect = "move";
		// event.currentTarget.style.border = "dashed"
		event.dataTransfer.setData("text/plain", event.target.id); //event.target.outerHTML
		
		selected = true
	}
	
	function handleDragEnd(event) {
		// what should happen regardless of whether the drag was successful
		event.target.style.border = "solid"

		// this should only happen if the node was dropped in the editor
		let el = document.getElementById(id)
		
		if (el.hasAttribute("dropped")) {
			x = event.clientX - xOffSet;
			y = event.clientY - yOffSet;
			el.removeAttribute("dropped")
		}
	}

	function handleContextMenu(event) {
		event.preventDefault()
		console.log("open context menu")
		openmenu = true
	}
</script>

<style>
	div {
		position: absolute;
		width: 200px;
		border: solid;
		background-color: white;
	}

	div[selected=true] {
		border: dashed;
	}

	div:hover {
		cursor: grab;
		/* transform: scale(1.1); */
	}
</style>

<div on:dragstart={handleDragStart}
	on:dragend={handleDragEnd}
	id={id}
	style="top: {y}px; left: {x}px;"
	draggable="true"
	selected={selected?true:false}
	on:contextmenu={handleContextMenu}>
	<!-- <RadialMenu bind:open={openmenu}></RadialMenu> -->
	Node:<br>
	Position [{x}, {y}]<br>
	Offset: [{xOffSet}, {yOffSet}]<br>
	selected: {selected}<br>
	openmenu: {openmenu}<br>
</div>