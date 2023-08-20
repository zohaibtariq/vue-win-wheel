<script setup lang="ts">

import { onMounted, ref } from 'vue';
import { winwheel } from "./winwheel";

export interface Props {
  options?: any,
  canvasWidth?: number,
  canvasHeight?: number,
  stopAt?: number
}
const props = withDefaults(defineProps<Props>(), {
  options: {
    canvasId: 'canvas',
    centerX: 250,         // X position of the center of the wheel. The default of these are null which means will be placed in center of the canvas.
    centerY: 250,         // Y position of the wheel center. If left null at time of construct the center of the canvas is used.
    outerRadius: 246,         // The radius of the outside of the wheel. If left null it will be set to the radius from the center of the canvas to its shortest side.
    rotationAngle: 0,            // The angle of rotation of the wheel - 0 is 12 o'clock position.
    textFontFamily: 'Arial',      // Segment text font, you should use web safe fonts.
    textFontWeight: 'bold',       // Font weight.
    textFillStyle: 'black',      // This is basically the text colour.
    textStrokeStyle: null,         // Basically the line colour for segment text, only looks good for large text so off by default.
    textLineWidth: 1,            // Width of the lines around the text. Even though this defaults to 1, a line is only drawn if textStrokeStyle specified.
    fillStyle: 'silver',     // The segment background colour.
    strokeStyle: 'black',      // Segment line colour. Again segment lines only drawn if this is specified.
    clearTheCanvas: true,         // When set to true the canvas will be cleared before the wheel is drawn.
    imageOverlay: false,        // If set to true in image drawing mode the outline of the segments will be displayed over the image. Does nothing in code drawMode.
    drawText: true,         // By default the text of the segments is rendered in code drawMode and not in image drawMode.
    pointerAngle: 0,            // Location of the pointer that indicates the prize when wheel has stopped. Default is 0 so the (corrected) 12 o'clock position.
    wheelImage: null,         // Must be set to image data in order to use image to draw the wheel - drawMode must also be 'image'.
    imageDirection: 'N',          // Used when drawMode is segmentImage. Default is north, can also be (E)ast, (S)outh, (W)est.
    responsive: false,        // If set to true the wheel will resize when the window first loads and also onResize.
    scaleFactor: 1,            // Set by the responsive function. Used in many calculations to scale the wheel.
    textFontSize: 12,
    innerRadius: 30,
    lineWidth: 4,
    textAlignment: 'center',
    textDirection: 'normal',
    textOrientation: 'horizontal',
    textMargin: 1,
    drawMode: 'code',
    imageSrc: null,
    numSegments: 4,
    segments: [
      { fillStyle: '#eae56f', text: 'Prize One' },
      { fillStyle: '#89f26e', text: 'Prize Two' },
      { fillStyle: '#7de6ef', text: 'Prize Three' },
      { fillStyle: '#e7706f', text: 'Prize Four' }
    ],
    animation: {
      type: 'spinToStop',
      duration: 3,
      spins: 3,
      callbackSound: () => console.log('callbackSound'),
      callbackFinished: () => console.log('callbackFinished')
    }
  },
  canvasWidth: 500,
  canvasHeight: 500,
  stopAt: 1
})

let winwheelObj = <any>ref({});
let innerWinwheelObj = <any>ref({});
let inputStopAtRef = <any>ref(props.stopAt);

function initSpin(callback: ((wheel: any, innerWheel: any) => void) | null = null){
  const options = {
    ...props?.options,
    centerX: (props.canvasWidth || 500) / 2,
    centerY: (props.canvasHeight || 500) / 2,
  };
  if(props?.options?.innerWheelOptions) {
    innerWinwheelObj.value = new winwheel(props.options?.innerWheelOptions);
  }
  winwheelObj.value = new winwheel(options);
  if(callback)
    callback(winwheelObj.value, innerWinwheelObj.value)
  // drawTriangle();
  if(props?.options?.drawMode === 'image' && props?.options?.imageSrc != null) {
    // Create new image object in memory.
    let loadedImg = new Image();
    // Create callback to execute once the image has finished loading.
    loadedImg.onload = function()
    {
      winwheelObj.value.wheelImage = loadedImg;    // Make wheelImage equal the loaded image object.
      winwheelObj.value.draw();                    // Also call draw function to render the wheel.
    }
    // Set the image source, once complete this will trigger the onLoad callback (above).
    loadedImg.src = props?.options?.imageSrc;
  }
  else if(props?.options?.innerWheelOptions) {
    // Call draw on the outerWheel then the inner wheel to ensure that both are rendered on the canvas.
    winwheelObj.value.draw();
    innerWinwheelObj.value.draw(false);   // Pass false to stop it clearing the canvas and wiping the outer wheel.
  }
}

function spin(callback = null){
  initSpin(callback)
  winwheelObj.value.startAnimation()
}

function spinAndStopAt(stopAt = null, callback = null){
  initSpin(callback)
  // console.log('spinAndStopAt');
  // console.log(stopAt, inputStopAtRef.value);
  const stopAtValue = stopAt || inputStopAtRef.value;
  // console.log(stopAtValue);
  winwheelObj.value.animation.stopAngle = 360 / props.options.segments.length * parseInt(stopAtValue) - 360 / props.options.segments.length / 2 // center pin
  // // var stopAt = 360 / props.options.segments.length * prizeNumber - Math.floor(Math.random() * 60) //random location
  winwheelObj.value.startAnimation()
}

// function drawTriangle(){
//   let leftCenter = (props.canvasWidth / 2) - 50;
//   let rightCenter = (props.canvasWidth / 2) + 50;
//   let center = props.canvasWidth / 2;
//   let triangleHeight = 50;
//   let lineWidth = 2;
//   let topGap = 0;
//   let triangleTopGap = (topGap < lineWidth) ? lineWidth : topGap;
//
//   // Get the canvas context the wheel uses.
//   let ctx = winwheelObj.value.ctx;
//
//   ctx.strokeStyle = 'black';     // Set line colour.
//   ctx.fillStyle   = 'black';     // Set fill colour.
//   ctx.lineWidth   = lineWidth;
//   ctx.beginPath();              // Begin path.
//   ctx.moveTo(leftCenter, triangleTopGap);           // Move to initial position.
//   ctx.lineTo(rightCenter, triangleTopGap);           // Draw lines to make the shape.
//   ctx.lineTo(center, triangleHeight);
//   ctx.lineTo(leftCenter + (lineWidth / 2), triangleTopGap);
//   ctx.stroke();                 // Complete the path by stroking (draw lines).
//   ctx.fill();                   // Then fill.
// }

onMounted(() => {
  // console.log('props')
  // console.log({...props})
  initSpin()
});

defineExpose({
  spin,
  spinAndStopAt
});

</script>

<template>
  <div id="canvas-container" class="canvas-container d-flex align-center content-center direction-column">
<!--    <h1>Spin Wheel</h1>-->
    <canvas :height="props.canvasHeight || 500" :width="props.canvasWidth || 500" :id="props?.options?.canvasId || 'canvas'"></canvas>
    <img id="prize-pointer" class="prize-pointer" src="./basic_pointer.png" alt="prize pointer" />
    <!--    <div class="d-flex align-center content-center direction-column">-->
<!--      <input class="form input" type="number" v-model="inputStopAtRef" min="1" :max="props.options?.segments?.length || 1">-->
<!--      <button class="form button" @click="spinAndStopAt">Spin & Stop At</button>-->
<!--      <button class="form button" @click="spin">Spin It!</button>-->
<!--    </div>-->
  </div>
</template>

<style scoped>
.d-flex{
  display: flex;
}
.align-center{
  align-items: center;
}
.content-center{
  justify-content: center;
}
.direction-column{
  flex-direction: column;
}
.canvas-container{
  background-image: url('./basic_wheel_background.png');
  background-repeat: no-repeat;   /* Ensure that background does not repeat */
  background-position: center;    /* Ensure image is centred */
  width: 400px;                   /* Width and height should at least be the same as the canvas */
  height: 400px;
  position: relative;
  padding: 100px;
  background-size: 500px;
}
.prize-pointer {
  position: absolute;
  left: 262.5px;
  top: 0;
  z-index: 999;
  width: 75px;
  height: 75px;
}
/*.form.input{*/
/*  padding: 12px;*/
/*  border: 1px solid grey;*/
/*  border-radius: 8px;*/
/*  outline: none;*/
/*  width: 100px;*/
/*  margin: 5px;*/
/*}*/
/*.form.button{*/
/*  border: 1px solid grey;*/
/*  margin: 5px;*/
/*}*/
</style>
