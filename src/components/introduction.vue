<template>
	<transition name="fade">
		<div id="dimmer" v-if="dimmer">
			<section>
				<div></div>
				<div><h1>Community Network Project</h1></div>
			</section>
			<section>
				<div id="title">
					<span style="font-size: 20px">Introduction</span>
				</div>
				<div>
					Community Network Project is initiated by Xiaofan, and now
					joined with Eliana Sullivan and Nícolas Gort to empirically
					study how Class 2019 is connected and how these connections
					have evolved. We want to explore big questions such as what
					does our community look like? How closely tied we actually
					are?
				</div>
			</section>
			<section>
				<div id="title">
					<span style="font-size: 20px">Graph Reading</span>
				</div>
				<div>
					Force-directed graph drawing is a class of graph layout
					algorithms that calculate the positions of each node by
					simulating an attractive force between each pair of linked
					nodes, as well as a repulsive force between the nodes.
					Typically, the attractive force acts like a spring between
					the nodes, calculated using Hooke’s law. On the other hand,
					two nodes are pushed away from each other using Coulomb’s
					law. It is a commonly implemented graph drawing algorithm
					because of its flexibility and intuitiveness, as it requires
					no special knowledge of graph theory.
				</div>
			</section>
			<div id="buttonContainer">
				<button v-on:click="fadeIntro">Enter</button>
			</div>
		</div>
	</transition>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
	name: 'introduction',
	props: ['dimmer'],
	methods: {
		fadeIntro: function() {
			this.$emit('clicked', { key: 'dimmer', input: false })
		},
	},
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#dimmer {
	height: 100%;
	width: 100%;
	background-color: rgba(245, 245, 220, 0.9);
	padding-bottom: 2.5rem;
	position: fixed;
}

#container {
	height: 100%;
	width: 100%;
	padding-bottom: 2.5rem;
}

section {
	display: grid;
	grid-template-columns: 1fr 5fr;
	opacity: 1 !important;
	padding: 0rem 5rem 2.5rem 5rem;
	overflow-y: auto;
}

#buttonContainer {
	display: grid;
	grid-template-columns: 3fr 1fr 3fr;
	opacity: 1 !important;
	padding: 0rem 5rem 2.5rem 5rem;
}

button {
	grid-column-start: 2;
	grid-column-end: 3;
}

.fade-enter-active,
.fade-leave-active {
	transition: opacity 2s;
}

.fade-enter,
.fade-leave-to
/* .fade-leave-active in <2.1.8 */

 {
	opacity: 0;
}
</style>
