<script>
	import Node from './node.svelte'
	// let grabbing = false
	let nodes = [
		{ id:0, x:250, y:300, component:Node },
		{ id:1, x:250, y:250, component:Node },
		{ id:2, x:250, y:350, component:Node }
	]
	let currentNode = undefined

	let dragXOffSet = 0
	let dragYOffSet = 0

	// try out a for loop with the component i.e.
	// nodes.forEach(n => {
	// 	let x = new Node()
	// 	x.x = n.x
	// 	x.y = n.y
	// 	x.id = n.id
	// 	document.appendChild(x)
	// });

	// catch mousewheel events (later mobile pinch) for zoom

	// create an origin and position nodes relative to the origin

	// pick up on the drag events for panning across the graph
	function handleDragStart(event) {
		// check what was dragged
		// grabbing = true
		if (event.target.nodeName == 'MAIN') {
			// editor
			const img = new Image();
			event.dataTransfer.setDragImage(img, 0, 0)
		} else {
			// node
			// get node id and set to current node
			// then update nodes selected prop
			
			//calc offset
			dragXOffSet = event.offsetX
			dragYOffSet = event.offsetY

			event.dataTransfer.dropEffect = "move";
			// event.currentTarget.style.border = "dashed"
			// event.dataTransfer.setData("text/plain", event.target.id); //event.target.outerHTML
			currentNode = event.target.id
		}
	}

	//on click of empty space de-select current node
	function handleClick(event) {
		if (event.target.nodeName == 'MAIN') {
			// de-select current node
			if (currentNode) {
				document.getElementById(currentNode).setAttribute("selected", false)
			}
			currentNode = undefined
		} else {
			// target must be a node
			if (currentNode) {
				document.getElementById(currentNode).setAttribute("selected", false)
			}
			currentNode = event.target.id
			document.getElementById(currentNode).setAttribute("selected", true)
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

			// assume that the id and position match within the array
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
	on:click={handleClick}
	draggable="true">
	{#each nodes as {component, x, y}, i}
	<svelte:component this={component}
		selected={currentNode===i}
		id={i}
		x={x}
		y={y}/>
	{/each}
</main>