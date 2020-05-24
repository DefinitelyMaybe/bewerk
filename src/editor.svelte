<script>
	import { onMount } from 'svelte';
	import Node from './node.svelte'
	import Menu from './ui/menu.svelte'
	
	let nodes = [
		{ x:225, y:180, component:Node },
		{ x:225, y:400, component:Node },
		{ x:565, y:290, component:Node }
	]
	let currentSelection = undefined

	let dragXOffSet = 0
	let dragYOffSet = 0

	let initDragMovement = true
	let moveX = 0
	let moveY = 0
	let prevX = 0
	let prevY = 0

	scrollX = 0
	scrollY = 0

	let backgroundOffsetX = 0
	let backgroundOffsetY = 0

	const bgWidth = 3508
	const bgHeight = 2480
	const bgScales = [0.67, 0.75, 0.8, 0.9, 1.0, 1.1, 1.25, 1.5, 1.75, 2.0]
	let scaleIndex = 4

	// ref to the html below
	let editor
	let BB = {left:0, top:0} // init dummy vars
	
	onMount(()=> {
		BB = editor.getBoundingClientRect()
	});

	// note: node positions may not work if the editor is not placed
	// in the top left corner (for now)
	
	// context menu for right click event
	// (idea: on import cursor change to waiting)

	function handleDragStart(event) {
		initDragMovement = true
		// check what was dragged
		if (!currentSelection) {
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
			currentSelection = event.target.id
		}
	}

	function handleMouseDown(event) {
		// de-select current node
		if (currentSelection) {
			currentSelection = undefined
		}

		// if the target has an id then its a node
		if (event.target.id) {
			currentSelection = event.target.id
		}
	}

	function handleDragOver(event) {
		event.preventDefault()
		// Later on, you might like to revisit this code so that
		// you can drag instead of scrolling.
		if (!currentSelection) {
			// calculate movement
			if (!initDragMovement) {
				moveX = event.screenX - prevX
				moveY = event.screenY - prevY
				prevX = event.screenX
				prevY = event.screenY
			} else {
				prevX = event.screenX
				prevY = event.screenY
				initDragMovement = false
			}
			// change the scroll
			// assumes that 'main' is parent
			event.path[1].scrollLeft -= moveX
			event.path[1].scrollTop -= moveY
		}
	}

	function handleDragEnd(event) {
		initDragMovement = false
		// what should happen regardless of whether the drag was successful
		// this should only happen if the node was dropped in the editor
		if (currentSelection) {
			// update both the node style and the editor array
			let y = (event.clientY - dragYOffSet + backgroundOffsetY) / bgScales[scaleIndex]
			let x = (event.clientX - dragXOffSet + backgroundOffsetX) / bgScales[scaleIndex]

			// console.log(x, y)

			// assume that the id and position match within the array
			let backgroundYOffSet = 0
			let index = parseInt(currentSelection.split('-')[1])
			let el = nodes[index]
			el.y = y
			el.x = x

			el = editor.childNodes[0].querySelector(`#${currentSelection}`)
			el.style.top = `${y}px`
			el.style.left = `${x}px`
		}
	}
	
	function handleDrop(event) {
		event.preventDefault()
	}

	function handleScroll(event) {
		backgroundOffsetX = event.target.scrollLeft
		backgroundOffsetY = event.target.scrollTop
	}

	function handleMouseWheel(event) {
		// will still fire even if overflow is hidden
		if (event.ctrlKey) {
			event.preventDefault()
			// scale the div
			//console.log(event)
			if (event.deltaY > 0) {
				// zoom out
				scaleIndex = Math.max(scaleIndex - 1, 0)
			} else {
				// zoom in
				scaleIndex = Math.min(scaleIndex + 1, bgScales.length - 1)
			}
			// could also compute new view position
		}
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
		overflow: auto;
		border: solid black 1px;
	}

	.background {
		width: 3508px;
		height: 2480px;
		background-color: #fff;
  	background-size: 40px 40px;
  	background-image:
    	linear-gradient(to right, #9ae5ff 1px, transparent 1px),
    	linear-gradient(to bottom, #9ae5ff 1px, transparent 1px);
		transform-origin: top left;
	}
</style>

<main on:dragover={handleDragOver}
	on:dragend={handleDragEnd}
	on:drop={handleDrop}
	on:dragstart={handleDragStart}
	on:scroll={handleScroll}
	on:mousedown={handleMouseDown}
	on:wheel={handleMouseWheel}
	draggable="true"
	bind:this={editor}>
	<div class="background"
		style="transform: scale({bgScales[scaleIndex]});">
		{#each nodes as {component, x, y}, i}
			<svelte:component this={component}
				selected={currentSelection==i}
				id="node-{i}"
				x={x}
				y={y}/>
		{/each}
	</div>
	<Menu></Menu>
</main>