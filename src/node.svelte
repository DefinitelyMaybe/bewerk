<script>
	export let selected = false
	export let id = undefined
	export let x = 0
	export let y = 0
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
		transform: scale(1.1);
	}

	div:hover {
		cursor: grab;
		border: dashed;
	}
</style>

<div id={id}
	selected={selected}
	draggable="true"
	style="top:{y}px;left:{x}px;">
	Placeholder Node
</div>