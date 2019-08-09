<template>
	<div id="app">
		<introduction @clicked="dimmerClick" v-bind="{ dimmer }">
		</introduction>
		<div id="visual">
			<visualisation v-bind="{ mode, colorScheme }"> </visualisation>
		</div>

		<div id="timeline"></div>

		<div id="menu">
			<visualToInfo> </visualToInfo>
			<infoToVisual @clicked="visualUpdate"> </infoToVisual>
		</div>
	</div>
</template>

<script lang="ts">
import Vue from 'vue'

import introduction from './components/introduction.vue'
import visualisation from './components/visualisation.vue'
import timeline from './components/timeline.vue'
import visualToInfo from './components/visualToInfo.vue'
import infoToVisual from './components/infoToVisual.vue'

export interface ColorScheme {
	[region: string]: string
}

const colorScheme: ColorScheme = {
	'North America (U.S. and Canada)': '#FF6D6F',
	'Europe': '#FF7F00',
	'East Asia': '#FFFF32',
	'Latin America and the Caribbean': '#4DAF4A',
	'Southeast Asia': '#377EB8',
	'Middle East and North Africa': '#984EA3',
	'Sub-Saharan Africa': '#F781BF',
	'South Asia': '#4F2207',
	'Russia, Central Asia, and Transcaucasia': '#999999',
	'Pacific and Oceania': '#000000',
}

export interface Mode {
	key: string
	input: string
}

interface DimmerClick {
	key: string
	input: boolean
}

const dimmer = true
const mode: Mode = {
	key: 'all',
	input: 'all',
}

export default Vue.extend({
	name: 'app',
	components: {
		introduction,
		visualisation,
		timeline,
		visualToInfo,
		infoToVisual,
	},
	data() {
		return {
			dimmer,
			mode,
			colorScheme,
		}
	},
	methods: {
		visualUpdate(data: Mode) {
			this.mode = data
		},
		dimmerClick(data: DimmerClick) {
			this.dimmer = data.input
		},
	},
})
</script>

<style>
html,
body {
	margin: 0;
	height: 100%;
}

#app {
	margin: 0;
	height: 100%;
	display: grid;
	grid-template-columns: 4fr 2fr;
	grid-template-rows: 8fr 1fr;
	grid-template-areas:
		'visual menu'
		'timeline menu';
}

#visual {
	grid-area: visual;
}

#timeline {
	grid-area: timeline;
}

#menu {
	display: grid;
	grid-template-rows: 1fr 1fr;
}
</style>
