<script>
	import Node from './node.svelte'
	// let grabbing = false
	let nodes = [
		{ id:1, x:200, y:200, component:Node }
	]
	let currentNode

	// catch mousewheel events (later mobile pinch) for zoom

	// create an origin and position nodes relative to the origin

	// pick up on the drag events for panning across the graph
	function handleDragStart(event) {
		console.log("editor Drag Start")
		// check what was dragged
		// grabbing = true
		if (event.target.nodeName == 'MAIN') {
			// editor
			console.log("begin pan")
			const img = new Image();
			event.dataTransfer.setDragImage(img, 0, 0)
		} else {
			// node
			// get node id and set to current node
			// then update nodes selected prop
		}
	}

	//on click of empty space de-select current node

	// context menu for right click event
	// on import cursor change to waiting?

	function handleDragOver (event) {
		event.preventDefault()
	}

	function handleDragEnd(event) {
		// grabbing = false
	}
	
	function handleDrop (event) {
		event.preventDefault()
		// the dataTransfered is the what got dropped
		let id = event.dataTransfer.getData("text/plain")
		
		// now we need to tell that element that it was a valid drop
		// set one of its attributes
		if (id) {
			// how does the dom actually know which element to use
			document.getElementById(id).setAttribute("dropped", true)	
		}
	}
</script>

<style>
	main {
		width: 800px;
		height: 600px;
		clip-path: inset(0px 0px 0px 0px);
		background-color: #fff;
  	background-size: 40px 40px;
  	background-image:
    	linear-gradient(to right, #9ae5ff 1px, transparent 1px),
    	linear-gradient(to bottom, #9ae5ff 1px, transparent 1px);
	}
</style>

<main on:dragover={handleDragOver}
	on:dragend={handleDragEnd}
	on:drop={handleDrop}
	on:dragstart={handleDragStart}
	draggable="true">
	{#each nodes as {component, x, y, id}, i}
		<svelte:component this={component} id={id} x={x} y={y}/>
	{/each}
</main>