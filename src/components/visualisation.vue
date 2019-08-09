<template>
	<svg
id="visualisation" :width="width" :height="height"></svg>
</template>

<script lang="ts">
import Vue, { PropType } from 'vue'
import * as d3 from 'd3'
import data from '../data'
import { Mode, ColorScheme } from '../App.vue'

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
	.force('center', d3.forceCenter(width / 2.5, height / 2.3))

export default Vue.extend({
	name: 'introduction',
	props: {
		mode: Object as PropType<Mode>,
		colorScheme: Object as PropType<ColorScheme>,
	},
	data() {
		return {
			width,
			height,
			margin,
			nodes,
			links,
			simulation,
		}
	},
	watch: {
		mode: function() {
			if (this.mode.key === 'region') {
				this.nodeColor(this.mode)
			}
		},
	},
	methods: {
		nodeColor: function(mode: Mode) {
			nodes.forEach(node => {
				if (nodeByIndex.get(node.index).region === mode.input) {
					d3.select(`#circle${node.index}`).attr(
						'fill',
						this.colorScheme[nodeByIndex.get(node.index).region],
					)
				} else {
					d3.select(`#circle${node.index}`).attr('fill', 'grey')
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
						d3.select(`#link${link.index}`).attr('stroke', 'yellow')
						associated = true
					} else {
						d3.select(`#link${link.index}`).attr('fill', 'grey')
					}
				})
				if (!associated && node.index !== i) {
					d3.select(`#circle${node.index}`).attr('fill', 'grey')
				}
			})
		},
		handleMouseOut: function() {
			nodes.forEach(node => {
				d3.select(`#circle${node.index}`).attr(
					'fill',
					this.colorScheme[nodeByIndex.get(node.index).region],
				)
			})
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

<style scoped></style>
