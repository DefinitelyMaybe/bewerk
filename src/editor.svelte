<script>
	import Node from './node.svelte'
	
	let nodes = [
		{ x:250, y:250, component:Node },
		{ x:250, y:300, component:Node },
		{ x:250, y:350, component:Node }
	]
	let currentNode = undefined

	let dragXOffSet = 0
	let dragYOffSet = 0
	let backgroundXOffSet = 0
	let backgroundYOffSet = 0

	// note: node positions may not work if the editor is not placed
	// in the top left corner (for now)

	// catch mousewheel events (later mobile pinch) for zoom

	// create an origin and position nodes relative to the origin

	// pick up on the drag events for panning across the graph
	function handleDragStart(event) {
		// check what was dragged
		if (!currentNode) {
			// editor
			const img = new Image();
			event.dataTransfer.setDragImage(img, 0, 0)
		} else {
			// node
			//calc offset
			dragXOffSet = event.offsetX
			dragYOffSet = event.offsetY

			event.dataTransfer.dropEffect = "move";

			// get node id and set to current node
			currentNode = event.target.id
		}
	}

	function handleMouseDown(event) {
		// de-select current node
		if (currentNode) {
			currentNode = undefined
		}

		// if the target has an id then its a node
		if (event.target.id) {
			currentNode = event.target.id
		}
	}

	// context menu for right click event
	// on import cursor change to waiting?

	function handleDragOver (event) {
		event.preventDefault()
	}

	function handleDragEnd(event) {
		// what should happen regardless of whether the drag was successful
		// this should only happen if the node was dropped in the editor
		if (currentNode) {
			// update both the node style and the editor array
			let y = event.clientY - dragYOffSet
			let x = event.clientX - dragXOffSet

			// assume that the id and position match within the arSay = 0
			let backgroundYOffSet = 0
			let el = nodes[currentNode]
			el.y = y
			el.x = x

			el = document.getElementById(currentNode)
			el.style.top = `${y}px`
			el.style.left = `${x}px`
		}
	}
	
	function handleDrop (event) {
		event.preventDefault()
	}

</script>

<style>
	* {
		box-sizing: border-box;
	}

	main {
		position: relative;
		width: 800px;
		height: 600px;
		clip-path: inset(0px 0px 0px 0px);
		background-color: #fff;
  	background-size: 40px 40px;
  	background-image:
    	linear-gradient(to right, #9ae5ff 1px, transparent 1px),
    	linear-gradient(to bottom, #9ae5ff 1px, transparent 1px);
		overflow: hidden;
		border: solid black 1px;
	}

	.background {
		width: 3508px;
		height: 2480px;
	}
</style>

<main on:dragover={handleDragOver}
	on:dragend={handleDragEnd}
	on:drop={handleDrop}
	on:dragstart={handleDragStart}
	on:mousedown={handleMouseDown}
	draggable="true">
	<div class="background">
		{#each nodes as {component, x, y}, i}
			<svelte:component this={component}
				selected={currentNode==i}
				id={i}
				x={x}
				y={y}/>
		{/each}
	</div>
</main>