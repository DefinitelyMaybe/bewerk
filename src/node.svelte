<script>
	import { scale } from 'svelte/transition'
	
	export let id = 0

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
	}

	div:hover {
		cursor: grab;
		border: dashed;
	}
</style>

<div id={id}
	draggable="true">
	Node:<br>
</div>