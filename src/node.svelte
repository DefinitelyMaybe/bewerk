<script>
	export let selected = false
	export let id = undefined
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
	draggable="true">
	Placeholder Node
</div>