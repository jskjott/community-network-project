<template>
	<div class="container">
		<input
			type="range"
			min="0"
			max="8"
			value="0"
			class="slider"
			id="timeline"
		/>
	</div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
	name: 'introduction',
	props: {},
	methods: {},
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.container {
	width: 800px;
}

#timeline {
	width: 750px;
}
</style>
