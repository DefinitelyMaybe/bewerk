<script>
	function handleDragOver (event) {
		event.preventDefault()
		event.dataTransfer.dropEffect = "move"
	}
	
	function handleDrop (event) {
		event.preventDefault()
		// the dataTransfered is the what got dropped
		let id = event.dataTransfer.getData("text/plain")
		
		// now we need to tell that element that it was a valid drop
		// set one of its attributes
		document.getElementById(id).setAttribute("dropped", true)
	}
</script>

<style>
	main {
		width: 800px;
		height: 600px;
		background-color: #fff;
  	background-size: 40px 40px;
  	background-image:
    	linear-gradient(to right, #9ae5ff 1px, transparent 1px),
    	linear-gradient(to bottom, #9ae5ff 1px, transparent 1px);
	}
</style>

<p>
	The Editor
</p>
<main on:dragover={handleDragOver} on:drop={handleDrop}>
	<slot></slot>
</main>