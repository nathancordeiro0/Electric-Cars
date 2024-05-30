<template>

  <q-toolbar>
    <q-btn flat round dense size="12.5px" color="grey-8" icon="chevron_left" @click="emit('returnTab')">
      <q-tooltip>
        Voltar para seleção
      </q-tooltip>
    </q-btn>
  </q-toolbar>

  <div>
    <svg ref="svgRef"></svg>
  </div>

  <div class="flex flex-center fit">
    <q-btn class="text-white bg-primary borda-redonda" flat label="Criar grafo" @click="createGrafo"/>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import * as d3 from 'd3'

const props = defineProps(['grafoValues'])
const emit = defineEmits(['returnTab'])

const numVertices = ref(2 + props.grafoValues.points)
const nameStart = ref(props.grafoValues.originPoint)
const nameEnd = ref(props.grafoValues.destinationPoint)

const svgRef = ref(null)

const createGrafo = () => {
  const nodes = d3.range(numVertices.value).map(i => ({ id: i }))
  nodes[0].name = nameStart.value
  nodes[numVertices.value - 1].name = nameEnd.value

  const links = []
  for (let i = 0; i < numVertices.value - 1; i++) {
    links.push({ source: i, target: i + 1 })
  }

  const width = window.innerWidth
  const height = window.innerHeight

  const svg = d3.select(svgRef.value)
    .attr('width', width)
    .attr('height', height)

  const simulation = d3.forceSimulation(nodes)
    .force('link', d3.forceLink(links).id(d => d.id).distance(50)) // Reduzindo a distância
    .force('charge', d3.forceManyBody().strength(-100))
    .force('center', d3.forceCenter(width / 2, height / 2))

  const link = svg.append('g')
    .attr('class', 'links')
    .selectAll('line')
    .data(links)
    .enter().append('line')
    .attr('class', 'link')

  const node = svg.append('g')
    .attr('class', 'nodes')
    .selectAll('g')
    .data(nodes)
    .enter().append('g')
    .attr('class', 'node')

  node.append('circle')
    .attr('r', 5)

  node.append('text')
    .attr('x', 8)
    .attr('y', 3)
    .text(d => d.name || d.id)

  simulation.on('tick', () => {
    link
      .attr('x1', d => d.source.x)
      .attr('y1', d => d.source.y)
      .attr('x2', d => d.target.x)
      .attr('y2', d => d.target.y)

    node
      .attr('transform', d => `translate(${d.x},${d.y})`)
  })
}
</script>

<style>
  body, html {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
  }

  svg {
    width: 100%;
    height: 100%;
  }

  .node circle {
    fill: #69b3a2;
    stroke: #404040;
    stroke-width: 1.5px;
  }
  .node text {
    font: 12px sans-serif;
    pointer-events: none;
  }
  .link {
    stroke: #999;
    stroke-opacity: 0.6;
  }
</style>
