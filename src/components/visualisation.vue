<template>
	<div id="container">
		<svg
id="visualisation" :width="width" :height="height"></svg>
	</div>
</template>

<script lang="ts">
import Vue, { PropType } from 'vue'
import * as d3 from 'd3'
import data from '../data'
import { Update, ColorScheme } from '../App.vue'

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
	index?: number
	links?: {
		[nodeIndex: number]: {
			[time: number]: number
		}
	}
}

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

// todo fix types

interface State {
	regions: {
		[region: string]: any
	}
	reasons: {
		[reason: string]: any
	}
	time: {
		step: number
	}
}

interface LinkElement {
	index: number
	source: {
		index: number
	}
	target: {
		index: number
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
		step: 1,
	},
}

let simulation: d3.Simulation<SimNode, SimLink> | null = null

export default Vue.extend({
	name: 'introduction',
	props: {
		update: Object as PropType<Update>,
		colorScheme: Object as PropType<ColorScheme>,
		width: Number,
		height: Number,
	},
	data() {
		return {
			state,
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

			if (update.key === 'time') {
				const input =
					typeof update.input === 'string'
						? parseInt(update.input)
						: update.input
				this.state.time.step = input
			} else if (state[update.key]) {
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
				const { all } = this.state.regions
				const sourceSelected = Object.prototype.hasOwnProperty.call(
					this.state.regions,
					nodeByIndex.get(link.source).region,
				)
				const targetSelected = Object.prototype.hasOwnProperty.call(
					this.state.regions,
					nodeByIndex.get(link.target).region,
				)

				if (
					link.time === state.time.step &&
					((sourceSelected && targetSelected) || all)
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
					d3.select(`#line${i}`)
						.style('stroke-width', active ? 5 : 1)
						.style('stroke', active ? 'yellow' : 'black')
				} else {
					d3.select(`#line${i}`)
						.style('stroke-width', 0)
						.style('stroke', 'black')
				}
			})
		},
		handleMouseOver: function(d: Node, i: number) {
			const activeLinks: number[] = []

			links.forEach((link, index) => {
				if (data.links[index].time === this.state.time.step) {
					d3.select(`#line${link.index}`)
						.style('stroke-width', 1)
						.style('stroke', 'black')
				}
			})

			nodes.forEach(node => {
				if (
					d.links !== undefined &&
					d.links[node.index] &&
					d.links[node.index][this.state.time.step] !== undefined
				) {
					d3.select(`#circle${node.index}`).attr(
						'fill',
						this.colorScheme[nodeByIndex.get(node.index).region],
					)
					activeLinks.push(d.links[node.index][this.state.time.step])
				} else if (node.index !== i) {
					d3.select(`#circle${node.index}`).attr('fill', 'grey')
				}
			}),
				activeLinks.forEach(link => {
					d3.select(`#line${link}`)
						.style('stroke-width', 5)
						.style('stroke', 'yellow')
				})
		},
		handleMouseOut: function() {
			this.draw()
		},
	},
	mounted: function() {
		const simulation: d3.Simulation<SimNode, SimLink> = d3
			.forceSimulation<SimNode, SimLink>(nodes)
			.force('link', d3.forceLink(links))
			.force('charge', d3.forceManyBody().strength(-12))
			.force(
				'center',
				d3.forceCenter(this.width / 2.15, this.height / 2.15),
			)
		this.simulation = simulation

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

		this.colorNodes()
		this.colorLinks()

		simulation.on('tick', () => {
			link.attr('x1', d => d.source.x)
				.attr('y1', d => d.source.y)
				.attr('x2', d => d.target.x)
				.attr('y2', d => d.target.y)

			node.attr(
				'cx',
				d =>
					(d.x = Math.max(
						radius,
						Math.min(this.width - radius, d.x),
					)),
			).attr(
				'cy',
				d =>
					(d.y = Math.max(
						radius,
						Math.min(this.height - radius, d.y),
					)),
			)
			node.attr('cx', d => d.x).attr('cy', d => d.y)
		})

		svg.node()

		nodes.forEach((node: Node) => {
			node.links = {}
			links.forEach((link: LinkElement, linkIndex: number) => {
				const elements = [link.source.index, link.target.index]
				if (node.index !== undefined && elements.includes(node.index)) {
					let targetNode
					if (link.source.index === node.index) {
						targetNode = link.target.index
					} else if (link.target.index === node.index) {
						targetNode = link.source.index
					}

					const { time } = data.links[linkIndex]

					let links: Node['links']

					if (node.links !== undefined) {
						links = node.links
					}

					if (
						targetNode !== undefined &&
						links![targetNode] !== undefined
					) {
						node!.links![targetNode][time] = linkIndex
					} else if (
						node.links !== undefined &&
						targetNode !== undefined
					) {
						node!.links[targetNode] = {
							[time]: linkIndex,
						}
					}
				}
			})
		})
	},
})
</script>

<style scoped>
svg {
	margin: 2rem;
}
</style>
