<script>
  export let visible = false
</script>

<style>
  ul {
    position: absolute;
    margin: 0px;
    z-index: 1;
    top: 0px;
    left: 0px;
    background-color: white;
    border: solid 2px;
  }
</style>

<ul id="menu"
  style="visibility:{visible?'visible':'hidden'};">
  <li>new</li>
  <li>edit</li>
  <li>delete</li>
  <li>load</li>
  <li>save</li>
</ul>