<template>
	<div id="app">
		<introduction
			id="dimmerContainer"
			@clicked="dimmerClick"
			v-bind="{ dimmer }"
		>
		</introduction>
		<div id="visual">
			<visualisation
				v-bind="{ update, colorScheme, width, height }"
				@clicked="dimmerClick"
			>
			</visualisation>
		</div>

		<div id="timeline"></div>

		<div id="menuContainer">
			<div id="menuBar">
				<visualToInfo> </visualToInfo>
				<infoToVisual @clicked="visualUpdate"> </infoToVisual>
			</div>
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
	'all': 'white',
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

export interface Update {
	key: 'regions' | 'reasons' | 'time'
	input: string | number
}

interface DimmerClick {
	key: string
	input: string
}

const dimmer = 'introduction'
const update: Update = {
	key: 'regions',
	input: 'all',
}

const svgWidthRatio = 0.57
const svgHeigthRatio = 0.9
const width = document.body.clientWidth * svgWidthRatio
const height = window.innerHeight * svgHeigthRatio

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
			update,
			colorScheme,
			width,
			height,
		}
	},
	methods: {
		visualUpdate(data: Update) {
			this.update = data
		},
		dimmerClick(data: DimmerClick) {
			this.dimmer = data.input
		},
	},
})
</script>

<style>
@font-face {
	font-family: 'titillium_webbold';
	src: url('assets/titilliumweb-bold-webfont.woff2') format('woff2'),
		url('assets/titilliumweb-bold-webfont.woff') format('woff');
	font-weight: normal;
	font-style: normal;
}

@font-face {
	font-family: 'open_sansregular';
	src: url('assets/opensans-regular-webfont.woff2') format('woff2'),
		url('assets/opensans-regular-webfont.woff') format('woff');
	font-weight: normal;
	font-style: normal;
}

html,
body {
	margin: 0;
	height: 100%;
}

ul,
p {
	font-family: 'open_sansregular';
	margin: 0;
}

h3,
h2,
h1 {
	font-family: 'titillium_webbold';
}

h1 {
	font-size: 1.8rem;
	padding-right: 1rem;
}

ul {
	line-height: 1.5;
}

#app {
	margin: 0;
	height: 100%;
	display: grid;
	grid-template-columns: 2fr 1fr;
	grid-template-areas: 'visual menu';
}

#visual {
	grid-area: visual;
}

#timeline {
	grid-area: timeline;
}

#menuBar {
	display: grid;
	grid-template-rows: 1fr 6fr;
	min-width: 230px;
	background-color: rgba(255, 255, 255, 0.5);
	margin-right: 2rem;
}

#menuContainer {
	position: absolute;
	top: 0;
	right: 0;
	z-index: 0;
}

#dimmerContainer {
	z-index: 1;
	position: absolute;
}
</style>
