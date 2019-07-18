<template>
  <div id="app">
    <h1>E Signature</h1>
    <div class="canvas-wrapper">
      <canvas
        ref="my-canvas"
        @mousedown="drawStart('mouse', $event)"
        @mouseup="drawing = false"
        @touchstart="drawStart('touch', $event)"
        @touchend="drawing = false"
        @mousemove="draw('mouse', $event)"
        @touchmove="draw('touch', $event)"
      ></canvas>
    </div>
    <div class="btn-wrapper">
      <a :href="imageUrl" download="signature.png" @click="saveCanvas">
        <button class="btn">Save</button>
      </a>
      <button class="btn" @click="clearCanvas">Clear</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',

  data() {
    return {
      ctx: null,
      drawing: false,
      lastPos: null,
      imageUrl: null
    }
  },

  mounted() {
    this.ctx = this.$refs['my-canvas'].getContext('2d')
    this.$refs['my-canvas'].width = this.$refs['my-canvas'].parentElement.clientWidth
    this.$refs['my-canvas'].height = this.$refs['my-canvas'].parentElement.clientHeight
  },

  methods: {
    getClickPosition(evt) {
      const rect = this.$refs['my-canvas'].getBoundingClientRect()
      return {
        x: evt.clientX - rect.left,
        y: evt.clientY - rect.top
      }
    },

    drawStart(type, evt) {
      if (type === 'touch') {
        evt = evt.touches[0]
      }

      this.drawing = true
      this.lastPos = this.getClickPosition(evt)
    },

    draw(type, evt) {
      if (type === 'touch') {
        evt = evt.touches[0]
      }

      const ctx = this.ctx
      const { x, y } = this.getClickPosition(evt)

      if (this.drawing) {
        ctx.moveTo(this.lastPos.x, this.lastPos.y)
        ctx.lineTo(x, y)
        ctx.stroke()
        this.lastPos = { x, y }
      }
    },

    clearCanvas() {
      const canvas = this.$refs['my-canvas']
      this.ctx.clearRect(0, 0, canvas.width, canvas.height)
      this.ctx.beginPath()
    },

    saveCanvas() {
      this.imageUrl = this.$refs['my-canvas'].toDataURL('image/png').replace('image/png', 'image/octet-stream')
    }
  }
}
</script>

<style>
html,
body {
  margin: 0 5px;
  padding: 0;
  height: 100%;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.canvas-wrapper {
  width: 100%;
  height: 200px;
  margin: 20px 0;
  border: 1px dashed lightblue;
}

.button {
  background-color: #4caf50; /* Green */
  border: none;
  color: white;
  text-align: center;
  text-decoration: none;
  display: inline-block;
}

.btn-wrapper {
  width: 50%;
  display: flex;
  justify-content: space-between;
}

.btn {
  box-shadow: 0px 10px 14px -7px #276873;
  background: linear-gradient(to bottom, #599bb3 5%, #408c99 100%);
  filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#599bb3', endColorstr='#408c99',GradientType=0);
  background-color: #599bb3;
  border-radius: 8px;
  display: inline-block;
  cursor: pointer;
  color: #ffffff;
  font-family: Arial;
  font-size: 12px;
  font-weight: bold;
  padding: 10px 20px;
  text-decoration: none;
  text-shadow: 0px 1px 0px #3d768a;
}

.btn:hover {
  background: linear-gradient(to bottom, #408c99 5%, #599bb3 100%);
  filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#408c99', endColorstr='#599bb3',GradientType=0);
  background-color: #408c99;
}

.btn:active {
  position: relative;
  top: 1px;
}
</style>
