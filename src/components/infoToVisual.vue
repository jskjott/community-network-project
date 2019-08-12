<template>
	<div class="container">
		<div>
			<h3>Countries & Regions:</h3>
			<div
				class="buttonContainer"
				v-for="{ region, background, text } in colorScheme"
			>
				<button
					class="filter"
					v-on:click="sendInputToParent('regions', region)"
					v-bind:style="{ backgroundColor: background, color: text }"
				>
					<p>{{ region }}</p>
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
				<button
					v-on:click="handler(activity)"
					:id="activity"
					class="reasons"
				>
					<p>{{ activity }}</p>
				</button>
			</div>
		</div>
		<br />
		<br />
		<br />
		<br />
		<div>
			<h3>Community Age</h3>
			<div class="slidecontainer">
				<input
					type="range"
					min="0"
					max="4"
					value="0"
					class="slider"
					id="timeSlider"
					v-bind:value="time"
					v-on:input="e => this.update(e.target.value)"
				/>
				<!-- watch icon: Watch by https://thenounproject.com/Flatart/ -->
				<button id="animation" v-on:click="toggle">
					<img src="../watch.png" width="20px" />
				</button>
			</div>
		</div>
	</div>
</template>

<script lang="ts">
import Vue from 'vue'

const colorScheme = [
	{
		region: 'all',
		background: 'white',
		text: 'black',
	},
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
	{ activity: 'none' },
	{ activity: 'physical proximity' },
	{ activity: 'food sharing' },
	{ activity: 'chilling together' },
	{ activity: 'academic studies' },
	{ activity: 'shared culture' },
	{ activity: 'interests' },
	{ activity: 'shared experiences' },
	{ activity: 'religion' },
	{ activity: 'randomness' },
]

const time = 1
const animation = false

interface Input {
	isTrusted: boolean
	type: 'input'
}

export default Vue.extend({
	name: 'introduction',
	data() {
		return {
			colorScheme,
			activities,
			time,
			animation,
		}
	},
	methods: {
		sendInputToParent(key: string, input: string | number) {
			this.$emit('clicked', { key, input })
		},
		changeButtonClass(id: string) {
			const reasons = document.getElementsByClassName('reasons')
			for (const reason of reasons) {
				if (reason.id === id) {
					if (reason.classList.contains('active')) {
						reason.classList.remove('active')
					} else {
						reason.classList.add('active')
					}
				} else {
					reason.classList.remove('active')
				}
			}
		},
		handler(activity: string) {
			this.sendInputToParent('reasons', activity)
			this.changeButtonClass(activity)
		},
		update(input: string) {
			this.time = parseInt(input)
		},
		animate() {
			if (this.animation) {
				this.time = (this.time + 1) % 5
				setTimeout(() => {
					this.animate()
				}, 1000)
			}
		},
		toggle() {
			this.animation = this.animation ? false : true
		},
	},
	watch: {
		time: function() {
			this.sendInputToParent('time', this.time)
		},
		animation: function() {
			if (this.animation === true) {
				this.animate()
			}
		},
	},
})
</script>

<style scoped>
button {
	border-radius: 5px;
	padding: 3px 5px;
	margin: 2px 5px;
}

.buttonContainer {
	width: 100%;
}

.container {
}

.active {
	background-color: silver;
}

#timeSlider {
	width: 80%;
	margin-right: 0.5rem;
}

.filter,
.reasons {
	float: left;
}

#animation {
}
</style>
