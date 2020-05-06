<script>
	let position = { x: 0, y: 0 };
	let offSet = {x:0, y:0}

	function handleDragStart(event) {
		//calc offset
		console.log(event.currentTarget.style)
		event.dataTransfer.dropEffect = "move";
		event.currentTarget.style.border = "dashed"
		event.dataTransfer.setData("text/plain", event.target.id)
		//const img = new Image();
		//img.src = 'example.png'
		//event.dataTransfer.setDragImage(img, xOffset, yOffset)
	}
	
	function handleDragEnd(event) {
		// calc end position if valid?
		position.x = event.clientX;
		position.y = event.clientY;
		event.target.style.border = "solid"
	}
</script>

<style>
	div {
		/*top: {@javascript m.x}*/
		position: relative;
		top: 100px;
		left: 100px;
		width: 200px;
		border: solid;
		background-color: white;
	}
</style>

<div on:dragstart={handleDragStart} on:dragend={handleDragEnd} draggable="true">
	Node:<br>
	Position [{position.x}, {position.y}]<br>
	Offset: [{offSet.x}, {offSet.y}]<br>
</div>