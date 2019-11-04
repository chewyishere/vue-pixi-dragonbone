<template>
  <div id="app">
    <HelloWorld msg="vue component"/>
    <pixi-renderer>
      <pixi-container
        :x="200" :y="200"
        msg="pixi container"
      >
         <pixi-sprite :x="0" :y="0" imagePath="logo.png" msg="pixi sprite" />
         <pixi-sheep />
      </pixi-container>
    </pixi-renderer>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import PixiRenderer from './pixi/PIXIRenderer.vue'
import PixiSprite from './pixi/PIXISprite.vue'
import PixiSheep from './pixi/PIXISheep.vue'
import PixiContainer from './pixi/PIXIContainer.vue'

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
    PixiSheep,
    PixiContainer
  },

   methods: {
    // tickInfo(container, delta) {
    //   console.log(`Tick delta: ${delta}`)
    // }
  }
}

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
   position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}
</style>
