<script>
	let id = 0

	let x = 0
	let y = 0
	let xOffSet = 0
	let yOffSet = 0

	function handleDragStart(event) {
		//calc offset
		xOffSet = event.offsetX
		yOffSet = event.offsetY
		event.dataTransfer.dropEffect = "move";
		event.currentTarget.style.border = "dashed"
		event.dataTransfer.setData("text/plain", event.target.id); //event.target.outerHTML
		//const img = new Image();
		//img.src = 'example.png'
		//event.dataTransfer.setDragImage(img, xOffSet, yOffSet)
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
</script>

<style>
	div {
		/*top: {@javascript m.x}*/
		position: absolute;
		width: 200px;
		border: solid;
		background-color: white;
	}
</style>

<div on:dragstart={handleDragStart}
		 on:dragend={handleDragEnd}
		 id={id}
		 style="top: {y}px; left: {x}px"
		 draggable="true">
	Node:<br>
	Position [{x}, {y}]<br>
	Offset: [{xOffSet}, {yOffSet}]<br>
</div>