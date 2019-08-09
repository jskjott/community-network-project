<template>
	<div class="container">
		<div>
			<h3>Countries & Regions:</h3>
			<div
				class="buttonContainer"
				v-for="{ region, background, text } in colorScheme"
			>
				<button
					v-on:click="sendInputToParent('region', region)"
					v-bind:style="{ backgroundColor: background, color: text }"
				>
					{{ region }}
				</button>
			</div>
		</div>
		<br />
		<br />
		<br />
		<br />
		<br />
		<br />
		<div>
			<h3>Reasons for Connection:</h3>
			<div class="buttonContainer" v-for="{ activity } in activities">
				<button v-on:click="sendInputToParent('activity', activity)">
					{{ activity }}
				</button>
			</div>
		</div>
	</div>
</template>

<script lang="ts">
import Vue from 'vue'

const colorScheme = [
	{
		region: 'North America (U.S. and Canada)',
		background: '#FF6D6F',
		text: 'black',
	},
	{ region: 'Europe', background: '#FF7F00', text: 'black' },
	{ region: 'East Asia', background: '#FFFF32', text: 'black' },
	{
		region: 'Latin America and the Caribbean',
		background: '#4DAF4A',
		text: 'black',
	},
	{ region: 'Southeast Asia', background: '#377EB8', text: 'black' },
	{
		region: 'Middle East and North Africa',
		background: '#984EA3',
		text: 'black',
	},
	{ region: 'Sub-Saharan Africa', background: '#F781BF', text: 'black' },
	{ region: 'South Asia', background: '#4F2207', text: 'white' },
	{
		region: 'Russia, Central Asia, and Transcaucasia',
		background: '#999999',
		text: 'black',
	},
	{ region: 'Pacific and Oceania', background: '#000000', text: 'white' },
]

const activities = [
	{ activity: 'physical distance' },
	{ activity: 'share food' },
	{ activity: 'chilling buddies' },
	{ activity: 'academic' },
	{ activity: 'cultural' },
	{ activity: 'interests' },
	{ activity: 'shared experience' },
	{ activity: 'religion' },
	{ activity: 'randomness' },
]

export default Vue.extend({
	name: 'introduction',
	data() {
		return {
			colorScheme,
			activities,
		}
	},
	methods: {
		sendInputToParent(key: string, input: string) {
			this.$emit('clicked', { key, input })
		},
	},
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
button {
	border-radius: 5px;
	float: left;
	padding: 3px 5px;
	margin: 2px 5px;
}

.buttonContainer {
	width: 100%;
}

.container {
}
</style>
