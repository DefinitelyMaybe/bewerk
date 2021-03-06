<script>
	// Each element corresponds to a javascript type
	// (Number, String, Boolean, Function, Object, Symbol)
	
	// Blocks are custom elements which combine multiple elements and/or other blocks

	// All elements and blocks are created on the canvas

	// context menu for right click event
	// (idea: on import cursor change to waiting)
	// change menu depending on whats clicked

	// hamburger menu for ... info, github link, sharing, create desktop app?
	// https://www.w3schools.com/howto/howto_css_menu_icon.asp

	// mouse modifiers
	// mouse icon on drag
	// mouse icon on box-select nodes (for copy+paste nodes)

	// tabs for multiple canvas's

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

	let bgOffsetX = 0
	let bgOffsetY = 0

	const bgWidth = 3508
	const bgHeight = 2480
	const bgScales = [0.67, 0.75, 0.8, 0.9, 1.0, 1.1, 1.25, 1.5, 1.75, 2.0]
	let scaleIndex = 4

	// ref to the html below
	let editor

	let menu
	let showMenu = false

	// for later when embedding within external websites is more important
	// probably needs to take window.scroll and boundingRect into account
	// let BB = {left:0, top:0} // init dummy vars
	onMount(()=> {
		//BB = editor.getBoundingClientRect()
		menu = editor.childNodes[0].querySelector('#menu')
	});

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
		showMenu = false

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
			let y = (event.clientY - dragYOffSet + bgOffsetY) / bgScales[scaleIndex]
			let x = (event.clientX - dragXOffSet + bgOffsetX) / bgScales[scaleIndex]

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
		bgOffsetX = event.target.scrollLeft
		bgOffsetY = event.target.scrollTop
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

	function handleContextMenu(event) {
		event.preventDefault()
		showMenu = true
		menu.style.top = `${event.clientY}px`
		menu.style.left = `${event.clientX}px`
	}
</script>

<style>
	* {
		box-sizing: border-box;
	}

	main {
		position: relative;
		width: 100%;
		height: 100%;
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
	on:contextmenu={handleContextMenu}
	draggable="true"
	bind:this={editor}>
	<div class="background"
		style="transform: scale({bgScales[scaleIndex]});">
		{#each nodes as {component, x, y}, i}
			<svelte:component this={component}
				selected={currentSelection=='node-'+i}
				id="node-{i}"
				x={x}
				y={y}/>
		{/each}
	<Menu visible={showMenu}></Menu>
	</div>
</main>