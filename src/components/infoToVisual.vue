<template>
	<div class="container">
		<div>
			<h3>Countries & Regions:</h3>
			<div
				class="buttonContainer"
				v-for="{ region, background, text } in colorScheme"
			>
				<button
					class="regions allActive"
					:id="region"
					v-on:click="handlerRegion(region)"
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
		<br />
		<br />
		<br />
		<div>
			<h3>Reasons for Connection:</h3>
			<div
				class="buttonContainer"
				v-for="{ activity, className } in activities"
			>
				<button
					:class="className"
					:id="activity"
					v-on:click="handlerConnection(activity)"
				>
					<p>{{ activity }}</p>
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
			<h3 style="margin: 0.4rem">Community Age</h3>
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
				<button
id="animation" v-on:click="toggle" class="inactive">
					<img src="../watch.png" width="20px" />
				</button>
			</div>
			<p style="margin-left: 0.4rem; font-size: 14px">
				{{ periods[parseInt(time)] }}
			</p>
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
	{ activity: 'none', className: 'reasons active' },
	{ activity: 'physical proximity', className: 'reasons inactive' },
	{ activity: 'food sharing', className: 'reasons inactive' },
	{ activity: 'chilling together', className: 'reasons inactive' },
	{ activity: 'academic studies', className: 'reasons inactive' },
	{ activity: 'shared culture', className: 'reasons inactive' },
	{ activity: 'interests', className: 'reasons inactive' },
	{ activity: 'shared experiences', className: 'reasons inactive' },
	{ activity: 'religion', className: 'reasons inactive' },
	{ activity: 'randomness', className: 'reasons inactive' },
]

const periods = [
	'before school start',
	'1st year',
	'2nd year',
	'3rd year',
	'4th year',
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
			periods,
			time,
			animation,
		}
	},
	methods: {
		sendInputToParent(key: string, input: string | number) {
			this.$emit('clicked', { key, input })
		},
		changeButtonClass(className: string, id: string) {
			const clickedButton = document.getElementById(id)

			if (clickedButton !== null) {
				const allActive = clickedButton.classList.contains('allActive')
				const isActive = clickedButton.classList.contains('active')

				if (isActive) {
					clickedButton.classList.remove('active')
					clickedButton.classList.add('inactive')
				} else if (allActive) {
					clickedButton.classList.remove('allActive')
					clickedButton.classList.add('active')
				} else {
					clickedButton.classList.remove('inactive')
					clickedButton.classList.add('active')
				}

				const elements = document.getElementsByClassName(className)

				for (const element of elements) {
					const elementAllActive = element.classList.contains(
						'allActive',
					)
					if (element.id !== id) {
						if (className === 'reasons') {
							element.classList.remove('active')
							element.classList.add('inactive')
						} else if (id !== 'all' && elementAllActive) {
							element.classList.remove('allActive')
							element.classList.remove('active')
							element.classList.add('inactive')
						} else if (id === 'all' && allActive) {
							element.classList.remove('allActive')
							element.classList.add('inactive')
						} else if (id === 'all' && !allActive) {
							element.classList.remove('inactive')
							element.classList.add('allActive')
						}
					}
				}
			}
		},
		handlerRegion(region: string) {
			this.sendInputToParent('regions', region)
			this.changeButtonClass('regions', region)
		},
		handlerConnection(activity: string) {
			this.sendInputToParent('reasons', activity)
			this.changeButtonClass('reasons', activity)
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

			const button = document.getElementById('animation')
			console.log(button)

			if (button !== null) {
				if (this.animation) {
					button.classList.remove('inactive')
					button.classList.add('active')
				} else {
					button.classList.remove('active')
					button.classList.add('inactive')
				}
			}
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
	border-radius: 1px;
	padding: 6px 8px;
	margin: 2px 5px;
}

.buttonContainer {
	width: 100%;
	height: auto;
}

.container {
}

.allActive,
.active {
	background-color: silver;
}

.inactive {
	filter: grayscale(1);
}

#timeSlider {
	width: 80%;
	margin-right: 0.5rem;
}

.regions,
.reasons {
	float: left;
}

#animation {
}
</style>
