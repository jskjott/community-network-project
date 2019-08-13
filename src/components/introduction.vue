<template>
	<div id="container">
		<div id="menu">
			<div v-if="!menu">
				<img
					style="cursor: pointer"
					src="../menu.png"
					width="50"
					v-on:click="menu = true"
				/>
			</div>
			<div v-if="menu" v-on:mouseleave="menu = false">
				<div v-on:click="fadeIn('introduction')">
					<h3>Introduction</h3>
				</div>
				<div v-on:click="fadeIn('about')"><h3>About</h3></div>
			</div>
		</div>
		<transition name="fade">
			<div id="dimmer" v-if="dimmer === 'introduction'">
				<section>
					<div></div>
					<div><h1>Community Network Project</h1></div>
				</section>
				<section>
					<div id="title">
						<span style="font-size: 20px">
							<p>Introduction</p>
						</span>
					</div>
					<div>
						<p>
							Community Network Project is initiated by Xiaofan,
							and now joined with Eliana Sullivan and Nícolas Gort
							to empirically study how Class 2019 is connected and
							how these connections have evolved. We want to
							explore big questions such as what does our
							community look like? How closely tied we actually
							are?
						</p>
					</div>
				</section>
				<section>
					<div id="title">
						<span style="font-size: 20px">
							<p>Graph Reading</p></span
						>
					</div>
					<div>
						<p>
							Force-directed graph drawing is a class of graph
							layout algorithms that calculate the positions of
							each node by simulating an attractive force between
							each pair of linked nodes, as well as a repulsive
							force between the nodes. Typically, the attractive
							force acts like a spring between the nodes,
							calculated using Hooke’s law. On the other hand, two
							nodes are pushed away from each other using
							Coulomb’s law. It is a commonly implemented graph
							drawing algorithm because of its flexibility and
							intuitiveness, as it requires no special knowledge
							of graph theory.
						</p>
					</div>
				</section>
				<div id="buttonContainer">
					<button v-on:click="fadeIntro">
						<h1 style="padding-right: 0">Enter</h1>
					</button>
				</div>
				<div id="cross" v-on:click="fadeIntro">
					<img src="../cross.png" width="40px" />
				</div>
			</div>
			<div id="dimmer" v-if="dimmer === 'about'">
				<section>
					<div></div>
					<div><h1>Community Network Project</h1></div>
				</section>
				<section>
					<div id="title">
						<span style="font-size: 20px"> <p>About</p> </span>
					</div>
					<div>
						f
						<p>
							The Community Network Project was initiated by
							Xiaofan, Eliana Sullivan, and Nícolas Gort.
						</p>
					</div>
				</section>
				<div id="cross" v-on:click="fadeIntro">
					<img src="../cross.png" width="40px" />
				</div>
			</div>
		</transition>
	</div>
</template>

<script lang="ts">
import Vue from 'vue'

const menu = false

export default Vue.extend({
	name: 'introduction',
	props: ['dimmer'],
	data() {
		return {
			menu,
		}
	},
	methods: {
		fadeIntro: function() {
			this.$emit('clicked', { key: 'dimmer', input: false })
		},
		fadeIn: function(element: string) {
			this.$emit('clicked', { key: 'dimmer', input: element })
		},
	},
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#container {
	position: absolute;
}

#dimmer {
	height: 100%;
	width: 100%;
	background-color: rgba(245, 245, 220, 0.9);
	padding-bottom: 2.5rem;
	position: fixed;
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
	padding: 2.5rem 5rem 0rem 5rem;
}

button {
	grid-column-start: 2;
	grid-column-end: 3;
	padding: 0.25rem 2rem 0.5rem 2rem;
	border-radius: 10px;
	stroke: 1rem;
	border-width: medium;
	border-color: black;
	background-color: rgba(245, 245, 220, 0.9);
	cursor: pointer;
}

.fade-enter-active,
.fade-leave-active {
	transition: opacity 0.5s;
}

.fade-enter,
.fade-leave-to
/* .fade-leave-active in <2.1.8 */

 {
	opacity: 0;
}

#cross {
	position: absolute;
	top: 0;
	right: 0;
	padding: 2rem 5rem 2rem 2rem;
	cursor: pointer;
}

#menu {
	position: absolute;
	margin: 2rem;
	padding: 0 2rem 2rem 0rem;
}

h3 {
	margin: 0 0 1rem 0;
}

h3:hover {
	cursor: pointer;
}
</style>
