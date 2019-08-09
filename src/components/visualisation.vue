<template>
	<div>
		<div id="menu">
			<img
src="../menu.png" width="50" v-on:click="fadeIn" />
		</div>
		<svg
id="visualisation" :width="width" :height="height"></svg>
	</div>
</template>

<script lang="ts">
import Vue, { PropType } from 'vue'
import * as d3 from 'd3'
import data from '../data'
import { Update, ColorScheme } from '../App.vue'

const width = 850
const height = 625
const radius = 6
const margin = { top: 20, right: 20, bottom: 20, left: 20 }

interface SimNode extends d3.SimulationNodeDatum {
	id: string
	group: number
	r: number
}

interface SimLink extends d3.SimulationLinkDatum<SimNode> {
	value: number
	d: number
	s: number
}

interface Node {
	x: number
	y: number
	fx: number | null
	fy: number | null
}

//const drag = (simulation: d3.Simulation<SimNode, SimLink>) => {
//	//todo fix types//

//	function dragstarted(d: any) {
//		if (!d3.event.active) simulation.alphaTarget(0.3).restart()
//		d.fx = d.x
//		d.fy = d.y
//	}//

//	function dragged(d: any) {
//		d.fx = d3.event.x
//		d.fy = d3.event.y
//	}//

//	function dragended(d: any) {
//		if (!d3.event.active) simulation.alphaTarget(0)
//		d.fx = null
//		d.fy = null
//	}//

//	return d3
//		.drag()
//		.on('start', dragstarted)
//		.on('drag', dragged)
//		.on('end', dragended)
//}

const nodes = data.nodes.map(d => Object.create(d))

const nodeById = d3.map()
const nodeByIndex = d3.map()

data.nodes.forEach((node, i) => {
	nodeById.set(node.id, i)
	nodeByIndex.set(i.toString(), node)
})

const links = data.links.map((link, i) => {
	const linkObject = {
		index: i,
		source: nodes[nodeById.get(link.source)],
		target: nodes[nodeById.get(link.target)],
	}
	return linkObject
})

data.links.forEach(link => {
	link.source = nodeById.get(link.source)
	link.target = nodeById.get(link.target)
})

const simulation: d3.Simulation<SimNode, SimLink> = d3
	.forceSimulation<SimNode, SimLink>(nodes)
	.force('link', d3.forceLink(links))
	.force('charge', d3.forceManyBody().strength(-12))
	.force('center', d3.forceCenter(width / 2.15, height / 2.15))

// todo fix types

interface State {
	regions: {
		[region: string]: any
	}
	reasons: {
		[reason: string]: any
	}
	time: {
		[step: string]: any
	}
}

const state: State = {
	regions: {
		all: true,
	},
	reasons: {
		all: true,
	},
	time: {
		step: 0,
	},
}

export default Vue.extend({
	name: 'introduction',
	props: {
		update: Object as PropType<Update>,
		colorScheme: Object as PropType<ColorScheme>,
	},
	data() {
		return {
			state,
			width,
			height,
			margin,
			nodes,
			links,
			simulation,
		}
	},
	watch: {
		update: function() {
			const { state } = this
			const { update } = this

			if (state[update.key]) {
				if (
					update.input !== 'all' &&
					Object.prototype.hasOwnProperty.call(
						state[update.key],
						'all',
					)
				) {
					delete state[update.key].all
				}
				if (state[update.key][update.input] === true) {
					delete state[update.key][update.input]
				} else {
					if (update.key === 'reasons' || update.input === 'all') {
						state[update.key] = {
							[update.input]: true,
						}
					}
					state[update.key][update.input] = true
				}
			} else {
				state[update.key] = {
					[update.input]: true,
				}
			}
			this.draw()
		},
	},
	methods: {
		draw: function() {
			this.colorNodes()
			this.colorLinks()
		},
		colorNodes: function() {
			nodes.forEach(node => {
				if (
					Object.prototype.hasOwnProperty.call(
						this.state.regions,
						nodeByIndex.get(node.index).region,
					) ||
					this.state.regions['all']
				) {
					d3.select(`#circle${node.index}`).attr(
						'fill',
						this.colorScheme[nodeByIndex.get(node.index).region],
					)
				} else {
					d3.select(`#circle${node.index}`).attr('fill', 'grey')
				}
			})
		},
		colorLinks: function() {
			data.links.forEach((link, i) => {
				if (
					(Object.prototype.hasOwnProperty.call(
						this.state.regions,
						nodeByIndex.get(link.source).region,
					) &&
						Object.prototype.hasOwnProperty.call(
							this.state.regions,
							nodeByIndex.get(link.target).region,
						)) ||
					this.state.regions['all']
				) {
					let active = false
					Object.entries(link).forEach(prop => {
						if (
							prop[1] === 1 &&
							Object.prototype.hasOwnProperty.call(
								state.reasons,
								prop[0],
							)
						) {
							active = true
						}
					})
					if (active) {
						d3.select(`#line${i}`)
							.style('stroke', 'yellow')
							.style('stroke-width', 5)
					} else {
						d3.select(`#line${i}`)
							.style('stroke-width', 1)
							.style('stroke', 'black')
					}
				} else {
					d3.select(`#line${i}`)
						.style('stroke-width', 1)
						.style('stroke', 'black')
				}
			})
		},
		handleMouseOver: function(d: object, i: number) {
			const linkArray: LinkElement[] = links.map(link => {
				const linkElement = {
					index: link.index,
					targets: [link.source.index, link.target.index],
				}
				return linkElement
			})

			nodes.forEach(node => {
				let associated = false
				linkArray.forEach((link: LinkElement) => {
					if (
						link.targets.includes(node.index) &&
						link.targets.includes(i)
					) {
						d3.select(`#circle${node.index}`).attr(
							'fill',
							this.colorScheme[
								nodeByIndex.get(node.index).region
							],
						)

						d3.select(`#line${link.index}`)
							.style('stroke-width', 5)
							.style('stroke', 'yellow')
						associated = true
					} else {
						d3.select(`#line${link.index}`)
							.style('stroke-width', 1)
							.style('stroke', 'black')
					}
				})
				if (!associated && node.index !== i) {
					d3.select(`#circle${node.index}`).attr('fill', 'grey')
				}
			})
		},
		handleMouseOut: function() {
			this.draw()
		},
		fadeIn: function() {
			this.$emit('clicked', { key: 'dimmer', input: true })
		},
	},
	mounted: function() {
		const svg = d3.select('#visualisation')

		const link = svg
			.append('g')
			.attr('stroke', '#999')
			.attr('stroke-opacity', 0.6)
			.selectAll('line')
			.data(links)
			.join('line')
			.attr('stroke-width', Math.sqrt(1))
			.attr('id', d => `line${d.index}`)
			.attr('stroke-opacity', 0.5)

		const node = svg
			.append('g')
			.attr('stroke', 'black')
			.attr('stroke-width', 1)
			.selectAll('circle')
			.data(nodes)
			.join('circle')
			.attr('r', radius)
			.attr('fill', d => {
				return this.colorScheme[nodeByIndex.get(d.index).region]
			})
			.attr('id', d => `circle${d.index}`)
			//.call(drag(simulation))
			.on('mouseover', this.handleMouseOver)
			.on('mouseout', this.handleMouseOut)

		simulation.on('tick', () => {
			link.attr('x1', d => d.source.x)
				.attr('y1', d => d.source.y)
				.attr('x2', d => d.target.x)
				.attr('y2', d => d.target.y)

			node.attr(
				'cx',
				d => (d.x = Math.max(radius, Math.min(width - radius, d.x))),
			).attr(
				'cy',
				d => (d.y = Math.max(radius, Math.min(height - radius, d.y))),
			)
			node.attr('cx', d => d.x).attr('cy', d => d.y)
		})

		svg.node()
	},
})

interface LinkElement {
	index: number
	targets: number[]
}
</script>

<style scoped>
#menu {
	position: absolute;
	margin: 1.5rem;
	opacity: 0.5;
}
</style>
