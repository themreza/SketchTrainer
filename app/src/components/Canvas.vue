<script setup>
import {ref, useTemplateRef} from 'vue'
import VueDrawingCanvas from "vue-drawing-canvas";

const step = ref(0)
const eraser = ref(0)
const thickness = ref(0)
const VueCanvasDrawing = useTemplateRef('VueCanvasDrawing')

thickness.value = 1
eraser.value = false
step.value = 1

const refreshBackground = async function(increase) {
  if (increase && step.value < 8) {
    step.value += 1
  } else if (!increase && step.value > 1) {
    step.value -= 1
  }
  await VueCanvasDrawing.value.redraw();
}

const undo = async function() {
  await VueCanvasDrawing.value.undo();
}
const redo = async function() {
  await VueCanvasDrawing.value.redo();
}
const save = async function() {
  let downloadLink = document.createElement('a');
  downloadLink.setAttribute('download', 'CanvasAsImage.png');
  let canvas = document.getElementById("VueDrawingCanvas");
  let dataURL = canvas.toDataURL('image/png');
  let url = dataURL.replace(/^data:image\/png/,'data:application/octet-stream');
  downloadLink.setAttribute('href', url);
  downloadLink.click();
}
const clear = async function() {
  await VueCanvasDrawing.value.reset();
}
</script>

<template>
  <div>
    <div id="logo"><img src="@/assets/logo.png" /></div>
    <vue-drawing-canvas
        ref="VueCanvasDrawing"
        :backgroundImage="'/' + step + '.png'"
        lineCap="round"
        lineJoin="bevel"
        width="512"
        height="512"
        :eraser="eraser"
        :lineWidth="thickness"
    />
    <div id="buttons">
      <button type="button" @click.prevent="refreshBackground(false)">Previous Step</button>
      <button type="button" @click.prevent="refreshBackground(true)">Next Step</button>
      <button type="button" @click.prevent="eraser = false">Pencil</button>
      <button type="button" @click.prevent="eraser = true">Eraser</button>
      <button type="button" @click.prevent="thickness = 1">Reset Thickness</button>
      <button type="button" @click.prevent="thickness = thickness + 2">Thicker ({{thickness}})</button>
      <button type="button" @click.prevent="undo">Undo</button>
      <button type="button" @click.prevent="redo">Redo</button>
      <button type="button" @click.prevent="clear">Clear Everything</button>
      <button type="button" @click.prevent="save">Save Sketch</button>
    </div>
  </div>
</template>

<style scoped>
</style>
