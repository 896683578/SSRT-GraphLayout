
<script>
import dagreD3 from 'dagre-d3'
import * as d3 from 'd3'
import graphlibDot from 'graphlib-dot'
// import dotParser from 'dotparser'
export default {
  name: 'HelloWorld',
  data () {
    return {
      gg: '111111'
    }
  },
  methods: {
    txtbtn () {
      document.getElementById('txt').click()
    },
    loadTextFromFile (e) {
      const file = e.target.files[0]
      let name = file.name.split('.').splice(-1).toString()
      if (name !== 'gv') {
        this.$message.success('文件类型错误,请重新选择文件')
        return
      }
      const reader = new FileReader()
      if (typeof FileReader === 'undefined') {
        alert('您的浏览器不支持FileReader接口')
      }
      reader.onload = (e) => this.$emit('load', this.dealNum(e.target.result))
      reader.readAsText(file, 'utf-8')
    },
    dealNum (item) {
      this.gg = item
      document.getElementById('dot').value = item
      console.log(item)
      this.draw()
    },
    draw () {
      d3.select('g').selectAll('*').remove()
      console.log('draw')
      var g = graphlibDot.read(document.getElementById('dot').value)
      // var g = dotParser(document.getElementById('dot').value)
      console.log(g)
      var renderer = dagreD3.render()
      d3.select('svg g').call(renderer, g)
      var svg = document.querySelector('#graphContainer')
      var bbox = svg.getBBox()
      svg.style.width = bbox.width + 40.0 + 'px'
      svg.style.height = bbox.height + 40.0 + 'px'
      console.log('drew')
    },
    draw_bezier () {
      d3.select('g').selectAll('*').remove()
      console.log('draw')
      var g = graphlibDot.read(document.getElementById('dot').value)
      var render = new dagreD3.render()
      render.createEdgePaths(createEdgePaths)
      d3.select('svg g').call(render, g)
      var svg = document.querySelector('#graphContainer')
      var svgGroup = svg.append('g')
      var bbox = svg.getBBox()
      svg.style.width = bbox.width + 40.0 + 'px'
      svg.style.height = bbox.height + 40.0 + 'px'
      function createEdgePaths (selection, g, arrows) {
        selection.selectAll('g.edgePath')
          .data(g.edges())
          .enter()
          .append('path')
          .attr('d', function (e) {
            return calcPoints(g, e)
          })
      }
      function createEdgeLabels (selection, g) {

      }
      function calcPoints (g, e) {
        var source = g.node(e.v)
        var target = g.node(e.w)
        var x0 = source.x + source.width / 2
        var x1 = target.x - target.width / 2
        var y0 = source.y
        var y1 = target.y
        return linker(x0, y0, x1, y1)
      }
      function linker (x0, y0, x1, y1) {
        var dx = x1 - x0
        var k = dx / 3

        var path = d3.path()
        path.moveTo(x0, y0)
        path.bezierCurveTo(x1 - k, y0, x0, y1, x1 - k, y1)
        path.lineTo(x1, y1)

        return path.toString()
      }
    }
  }
}

</script>

<template>
  <html>
    <body>
      <svg id="graphContainer">
        <g />
      </svg>
      <textarea id="dot" rows="8">
      digraph {
      a->b
      }
      </textarea>
      <el-button @click="txtbtn">选择gv文件</el-button>
      <el-button @click="draw">普通</el-button>
      <el-button @click="draw_bezier">贝塞尔</el-button>
      <input type="file" @change="loadTextFromFile" id="txt" style="display:none" />
    </body>
  </html>
</template>

<style>
path {
  stroke: #333;
  stroke-width: 1.5px;
  fill: none;
}
rect {
  fill: none;
  stroke:black;
  stroke-width: 1px;
}
body {
  font-family: Verdana, Geneva, sans-serif;
  padding-left: 2em;
}
svg {
  overflow: hidden;
  margin: 1em;
}
.node rect {
  stroke: #333;
  stroke-width: 2.5px;
  fill: white;
}

.edgeLabel rect {
  fill: white;
}
.edgePath {
  stroke: #333;
  stroke-width: 1.5px;
  fill: none;
}
</style>
