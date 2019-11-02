<template>
  <div id="app">
     <HelloWorld msg="Welcome to Your Vue.js App"/>
    <pixi-renderer  :w="pixiWidth" :h="pixiHeight">
      <pixi-Container
        :x="pixiWidth/2" :y="pixiHeight/2"
        @tick="tickInfo" @pointerdown="scaleObject"
      >
        <pixi-Sprite  :x="0" :y="0" imagePath="logo.png"/>
      </pixi-container>
    </pixi-renderer>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import PixiRenderer from './pixi/PixiRenderer.vue'
import PixiSprite from './pixi/PixiSprite.vue'
import PixiContainer from './pixi/PixiContainer.vue'

export default {
  data() {
    return {
      pixiWidth: 800,
      pixiHeight: 600
    }
  },

  components: {
    HelloWorld,
    PixiRenderer,
    PixiSprite,
    PixiContainer
  },

  methods: {
    scaleObject(container) {
      container.scale.x *= 1.25;
      container.scale.y *= 1.25;
    },

    tickInfo(container, delta) {
      console.log(`Tick delta: ${delta}`)
    }
  }
}
</script>

<style>
#app {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}
</style>
