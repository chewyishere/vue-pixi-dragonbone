<template>
  <div class="pixi-renderer" ref="pixiBody">
    <!-- All child <template> elements get added in here -->
    <slot></slot>
  </div>
</template>
<script>

import Vue from 'vue';
import * as PIXI from 'pixi.js';
window.PIXI=PIXI; //Solution to use dragonbones with PIXI v5
const dragonBones = require('pixi5-dragonbones');

export default {
  data() {
    return {
      // These need to be contained in an object because providers are not reactive.
      PIXIWrapper: {
        // Expose PIXI and the created app to all descendants.
        PIXI,
        PIXIApp: null,
      },
      // Expose the event bus to all descendants so they can listen for the app-ready event.
      EventBus: new Vue()
    }
  },
  // Allows descendants to inject everything.
  provide() {
    return {
      PIXIWrapper: this.PIXIWrapper,
      EventBus: this.EventBus,
      DragonBones: dragonBones
    }
  },

  mounted() {
    const renderBody = this.$refs.pixiBody;

    // Create a new PIXI app.
    this.PIXIWrapper.PIXIApp = new PIXI.Application({
      width: 800,
      height: 1200,
      backgroundColor: 0x1099bb
      });

    renderBody.appendChild(this.PIXIWrapper.PIXIApp.view);
    this.PIXIWrapper.PIXIApp.stop();
    this.EventBus.$emit('ready');
  }
}
</script>

<style scoped>
canvas {
  width: 100%;
  height: 100%;
}
</style>
