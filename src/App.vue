<template>
  <VueWinWheel ref="winWheel" :options="allOptions" :canvasWidth="canvasWidth" :canvasHeight="canvasHeight" :stopAt="stopAt" />
  <br/>
  <br/>
  <br/>
  <button @click="spinWinWheel">Random Spin</button>
  <button @click="spinWinWheelStopAt">Defined Spin</button>
<!--    <VueWinWheel ref="winWheel" />-->
<!--    <VueWinWheel />-->
</template>

<script setup>
import VueWinWheel from '../lib/VueWinWheel.vue';
import { ref, onMounted } from 'vue';

const winWheel = ref();

const spinWinWheel = () => {
  spinStartTime = Date.now()
  if(winWheel.value)
    winWheel.value.spin(winWheelInstances)
};

const spinWinWheelStopAt = () => {
  spinStartTime = Date.now()
  if(winWheel.value)
    winWheel.value.spinAndStopAt(stopAt, winWheelInstances)
};

const canvasWidth = 500;
const canvasHeight = 500;
const stopAt = 3;

let spinningAudio = new Audio('./spinning.mp3');
let winnerAudio = new Audio('./winner.mp3');

// Called when the animation has finished.
function spinStopped(indicatedSegment)
{
  playWinningSound()
  // Display different message if win/lose/bankrupt.
  if (indicatedSegment.text == 'LOOSE TURN')
    alert('Sorry but you loose a turn.');
  else if (indicatedSegment.text == 'BANKRUPT')
    alert('Oh no, you have gone BANKRUPT!');
  else
    alert("You have won " + indicatedSegment.text);
}

function eachSegmentSpin()
{
  playSpinningSound()
}

let spinStartTime;

function playSpinningSound()
{
  if(!spinStartTime){
    spinStartTime = Date.now(); // this must start when spin starts
  }
  spinningAudio.pause();
  const spinEndTime = Date.now();
  const diffInSpinTime = spinEndTime - spinStartTime
  if(options?.animation?.duration === undefined || diffInSpinTime < ((options.animation.duration * 1000)/1.4)) { // do not play this sound if animation time is exceeded
    spinningAudio.currentTime = 0;
    spinningAudio.play();
  }
}

function playWinningSound()
{
  winnerAudio.pause();
  winnerAudio.currentTime = 0;
  winnerAudio.play();
}

/*
 *
const options = { // basic_code_wheel
  'numSegments'  : 8,			// Number of segments
  'outerRadius'  : 212,		// The size of the wheel.
  'centerX'      : 217,		// Used to position on the background correctly.
  'centerY'      : 219,
  'textFontSize' : 28,		// Font size.
  'segments'     :			// Definition of all the segments.
      [
        {'fillStyle' : '#eae56f', 'text' : 'Prize 1'},
        {'fillStyle' : '#89f26e', 'text' : 'Prize 2'},
        {'fillStyle' : '#7de6ef', 'text' : 'Prize 3'},
        {'fillStyle' : '#e7706f', 'text' : 'Prize 4'},
        {'fillStyle' : '#eae56f', 'text' : 'Prize 5'},
        {'fillStyle' : '#89f26e', 'text' : 'Prize 6'},
        {'fillStyle' : '#7de6ef', 'text' : 'Prize 7'},
        {'fillStyle' : '#e7706f', 'text' : 'Prize 8'}
      ],
  'animation' :				// Definition of the animation
      {
        'type'     : 'spinToStop',
        'duration' : 5,
        'spins'    : 8,
        'callbackFinished' : spinStopped,  // Function to call when the spinning has stopped.
        'callbackSound'    : eachSegmentSpin,   // Called when the tick sound is to be played.
      }
};
*/

/*
 *
const options = { // wheel_of_fortune
  'outerRadius'     : 212,        // Set outer radius so wheel fits inside the background.
  'innerRadius'     : 75,         // Make wheel hollow so segments don't go all way to center.
  'textFontSize'    : 24,         // Set default font size for the segments.
  'textOrientation' : 'vertical', // Make text vertical so goes down from the outside of wheel.
  'textAlignment'   : 'outer',    // Align text to outside of wheel.
  'numSegments'     : 24,         // Specify number of segments.
  'segments'        :             // Define segments including colour and text.
      [                               // font size and text colour overridden on bankrupt segments.
        {'fillStyle' : '#ee1c24', 'text' : '300'},
        {'fillStyle' : '#3cb878', 'text' : '450'},
        {'fillStyle' : '#f6989d', 'text' : '600'},
        {'fillStyle' : '#00aef0', 'text' : '750'},
        {'fillStyle' : '#f26522', 'text' : '500'},
        {'fillStyle' : '#000000', 'text' : 'BANKRUPT', 'textFontSize' : 16, 'textFillStyle' : '#ffffff'},
        {'fillStyle' : '#e70697', 'text' : '3000'},
        {'fillStyle' : '#fff200', 'text' : '600'},
        {'fillStyle' : '#f6989d', 'text' : '700'},
        {'fillStyle' : '#ee1c24', 'text' : '350'},
        {'fillStyle' : '#3cb878', 'text' : '500'},
        {'fillStyle' : '#f26522', 'text' : '800'},
        {'fillStyle' : '#a186be', 'text' : '300'},
        {'fillStyle' : '#fff200', 'text' : '400'},
        {'fillStyle' : '#00aef0', 'text' : '650'},
        {'fillStyle' : '#ee1c24', 'text' : '1000'},
        {'fillStyle' : '#f6989d', 'text' : '500'},
        {'fillStyle' : '#f26522', 'text' : '400'},
        {'fillStyle' : '#3cb878', 'text' : '900'},
        {'fillStyle' : '#000000', 'text' : 'BANKRUPT', 'textFontSize' : 16, 'textFillStyle' : '#ffffff'},
        {'fillStyle' : '#a186be', 'text' : '600'},
        {'fillStyle' : '#fff200', 'text' : '700'},
        {'fillStyle' : '#00aef0', 'text' : '800'},
        {'fillStyle' : '#ffffff', 'text' : 'LOOSE TURN', 'textFontSize' : 12}
      ],
  'animation' :           // Specify the animation to use.
      {
        'type'     : 'spinToStop',
        'duration' : 3,
        'spins'    : 10,
        'callbackFinished' : spinStopped,  // Function to call when the spinning has stopped.
        'callbackSound'    : eachSegmentSpin,   // Called when the tick sound is to be played.
        'soundTrigger'     : 'pin'        // Specify pins are to trigger the sound.
      },
  'pins' :				// Turn pins on.
      {
        'number'     : 24,
        'fillStyle'  : 'silver',
        'outerRadius': 4,
      }
};
*/

/*
 *
const options = { // basic_image_wheel
  'numSegments'       : 4,        	// Specify number of segments.
  'outerRadius'       : 150,      	// Set outer radius so wheel fits inside the background.
  'drawMode'          : 'image',  	// drawMode must be set to image.
  'imageSrc'          : "./wheel-colored.png",
  'drawText'          : true,     	// Need to set this true if want code-drawn text on image wheels.
  'textFontSize'      : 24,
  'textOrientation'   : 'curved',		// Note use of curved text.
  'textDirection'     : 'reversed',	// Set other text options as desired.
  'textAlignment'     : 'outer',
  'textMargin'        : 5,
  'textFontFamily'    : 'monospace',
  'textStrokeStyle'   : 'black',
  'textLineWidth'     : 2,
  'textFillStyle'     : 'white',
  'segments'     :                // Define segments.
      [
        {'text' : 'T-55 Vampire'},
        {'text' : 'P-40 Kittyhawk'},
        {'text' : 'North American Harvard'},
        {'text' : 'L-39C Albatross'}
      ],
  'animation' :                   // Specify the animation to use.
      {
        'type'     : 'spinToStop',
        'duration' : 5,     		// Duration in seconds.
        'spins'    : 8,     		// Number of complete spins.
        'callbackFinished' : spinStopped
      }
};
*/

const options = { // one_image_per_segment
  'numSegments'       : 8,                // Specify number of segments.
  'outerRadius'       : 200,              // Set outer radius so wheel fits inside the background.
  'drawText'          : true,             // Code drawn text can be used with segment images.
  'textFontSize'      : 16,				// Set text options as desired.
  'textOrientation'   : 'curved',
  'textAlignment'     : 'inner',
  'textMargin'        : 90,
  'textFontFamily'    : 'monospace',
  'textStrokeStyle'   : 'black',
  'textLineWidth'     : 3,
  'textFillStyle'     : 'white',
  'drawMode'          : 'segmentImage',    // Must be segmentImage to draw wheel using one image per segemnt.
  'segments'          :                    // Define segments including image and text.
      [
        {'image' : './tom.png',  'text' : 'Jane'},
        {'image' : './tom.png',   'text' : 'Tom'},
        {'image' : './tom.png',  'text' : 'Mary'},
        {'image' : './tom.png',  'text' : 'Alex'},
        {'image' : './tom.png', 'text' : 'Sarah'},
        {'image' : './tom.png', 'text' : 'Bruce'},
        {'image' : './tom.png',  'text' : 'Rose'},
        {'image' : './tom.png', 'text' : 'Steve'}
      ],
  'animation' :           // Specify the animation to use.
      {
        'type'     : 'spinToStop',
        'duration' : 5,
        'spins'    : 8,
        'callbackFinished' : spinStopped
      }
}

/*
 *
const options = { // hollow_wheel
  'numSegments'   : 16,   // Specify number of segments.
  'outerRadius'   : 212,  // Set radius to so wheel fits the background.
  'innerRadius'   : 120,  // Set inner radius to make wheel hollow.
  'textFontSize'  : 16,   // Set font size accordingly.
  'textMargin'    : 0,    // Take out default margin.
  'segments'      :       // Define segments including colour and text.
      [
        {'fillStyle' : '#eae56f', 'text' : 'Prize 1'},
        {'fillStyle' : '#89f26e', 'text' : 'Prize 2'},
        {'fillStyle' : '#7de6ef', 'text' : 'Prize 3'},
        {'fillStyle' : '#e7706f', 'text' : 'Prize 4'},
        {'fillStyle' : '#eae56f', 'text' : 'Prize 5'},
        {'fillStyle' : '#89f26e', 'text' : 'Prize 6'},
        {'fillStyle' : '#7de6ef', 'text' : 'Prize 7'},
        {'fillStyle' : '#e7706f', 'text' : 'Prize 8'},
        {'fillStyle' : '#eae56f', 'text' : 'Prize 9'},
        {'fillStyle' : '#89f26e', 'text' : 'Prize 10'},
        {'fillStyle' : '#7de6ef', 'text' : 'Prize 11'},
        {'fillStyle' : '#e7706f', 'text' : 'Prize 12'},
        {'fillStyle' : '#eae56f', 'text' : 'Prize 13'},
        {'fillStyle' : '#89f26e', 'text' : 'Prize 14'},
        {'fillStyle' : '#7de6ef', 'text' : 'Prize 15'},
        {'fillStyle' : '#e7706f', 'text' : 'Prize 16'}
      ],
  'animation' :           // Define spin to stop animation.
      {
        'type'     : 'spinToStop',
        'duration' : 5,
        'spins'    : 8,
        'callbackFinished' : spinStopped
      }
};
*/

/*
 *
const innerWheelOptions = { // two_part_wheel
  'numSegments' : 4,
  'outerRadius' : 110,        // Set the outer radius to make the wheel smaller than the outer wheel.
  'segments': [
    {'fillStyle' : '#eae56f', 'text' : 'Inner 1'},
    {'fillStyle' : '#89f26e', 'text' : 'Inner 2'},
    {'fillStyle' : '#7de6ef', 'text' : 'Inner 3'},
    {'fillStyle' : '#e7706f', 'text' : 'Inner 4'}
  ]
};

const outerWheelOptions = {
  'numSegments': 8,
  'textMargin' : 0,
  'outerRadius' : 210,
  'innerRadius' : 110,    // Set inner radius to the size of the inner wheel since the inner part of the wheel
  'segments': [           //   is being drawn by the inner wheel we don't need to draw there.
    {'fillStyle' : '#8C8A42', 'text' : 'Outer 1'},
    {'fillStyle' : '#F2F0A8', 'text' : 'Outer 2'},
    {'fillStyle' : '#519142', 'text' : 'Outer 3'},
    {'fillStyle' : '#B7F7A8', 'text' : 'Outer 4'},
    {'fillStyle' : '#4B898F', 'text' : 'Outer 5'},
    {'fillStyle' : '#B1EFF5', 'text' : 'Outer 6'},
    {'fillStyle' : '#8A4342', 'text' : 'Outer 7'},
    {'fillStyle' : '#F0A9A8', 'text' : 'Outer 8'}
  ],
  'animation':
      {
        'type': 'spinToStop',                     // Define animation more or less as normal, except for the callbackAfter().
        'duration': 5,
        'spins': 5,
        'easing': 'Power3.easeOut',
        'callbackAfter' : drawInnerWheel,     // Call back after each frame of the animation a function we can draw the inner wheel from.
        'callbackFinished': alertPrize
      }
};

// This function is called after the outer wheel has drawn during the animation.
function drawInnerWheel()
{
  // Update the rotationAngle of the innerWheel to match that of the outer wheel - this is a big part of what
  // links them to appear as one 2-part wheel. Call the draw function passing false so the outer wheel is not wiped.
  innerWinWheelInstance.rotationAngle = outerWinWheelInstance.rotationAngle;
  innerWinWheelInstance.draw(false);
}

// Called when the animation has finished.
function alertPrize()
{
  // The indicated segments from the 2 wheels.
  let winningInnerSegment = innerWinWheelInstance.getIndicatedSegment();
  let winningOuterSegment = outerWinWheelInstance.getIndicatedSegment();

  // Alert the combination of prizes won.
  alert('You won ' + winningInnerSegment.text + ', ' + winningOuterSegment.text);

  // Set things so power and spin button can be clicked again.
  // wheelSpinning = false;

  // Remove all colours from the power level indicators.
  // document.getElementById('pw1').className = "";
  // document.getElementById('pw2').className = "";
  // document.getElementById('pw3').className = "";
}

const options = {
  ...outerWheelOptions,
  innerWheelOptions
};
*/

let outerWinWheelInstance;
let innerWinWheelInstance;

const winWheelInstances = (outerWheelInstance, innerWheelInstance) => {
  if(outerWheelInstance) {
    outerWinWheelInstance = outerWheelInstance
    // Call draw on the outerWheel then the inner wheel to ensure that both are rendered on the canvas.
    // outerWheelInstance.draw();
  }
  if(innerWheelInstance) {
    innerWinWheelInstance = innerWheelInstance
    // innerWheelInstance.draw(false);   // Pass false to stop it clearing the canvas and wiping the outer wheel.
  }
  if(outerWheelInstance && innerWheelInstance) { // now tackled with drawInnerWheel
    // Update the rotationAngle of the innerWheel to match that of the outer wheel - this is a big part of what
    // links them to appear as one 2-part wheel. Call the draw function passing false so the outer wheel is not wiped.
    innerWheelInstance.rotationAngle = outerWheelInstance.rotationAngle;
    if(innerWheelInstance.draw)
      innerWheelInstance.draw(false);
  }
}

const allOptions = {
  ...options,
  outerRadius: (canvasWidth / 2) - (options.lineWidth || 1)
};

onMounted(() => {
  allOptions.outerRadius = (canvasWidth / 2) - (options.lineWidth || 1);
});

</script>

<style scoped>
</style>