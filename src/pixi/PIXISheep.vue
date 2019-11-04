<template>
  <div class="pixi-sheep">
  </div>
</template>


<script>

export default {
  inject: ['EventBus', 'PIXIWrapper', 'DragonBones'],
  // x, y define the sprite's position in the parxwent.
  // imagePath is the path to the image on the server to render as the sprite.

  data() {
    return {
      sprite: null,
      target: null,
      armatureDisplay: null,
      scale: 0.5,
      animationName: []
    }
  },

  created() {
    this.EventBus.$on('girlLoaded', () => {
        this.SetupDragonBone();
    });
  },

  methods: {
    SetupDragonBone(){
      this.target = new this.PIXIWrapper.PIXI.Point();

      this.animationNames = [
        'goat_idle_anim',
        'goat_look_X',
        'goat_look_Y'
      ];

      this.PIXIWrapper.PIXI.Loader.shared
      .add('sheep_ske', 'sheep2/Sheep_Ani_ske.json')
      .add('sheep_tex_json', 'sheep2/Sheep_Ani_tex.json')
      .add('sheep_tex', 'sheep2/Sheep_Ani_tex.png')
      .load(this.onAssetsLoaded);
    },

    onAssetsLoaded(loader, res){
      const factory = this.DragonBones.PixiFactory.factory;

      factory.parseDragonBonesData(res.sheep_ske.data);
      factory.parseTextureAtlasData(res.sheep_tex_json.data, res.sheep_tex.texture);

      this.armatureDisplay = factory.buildArmatureDisplay('Armature');
      this.armatureDisplay.animation.play('goat_idle_anim');
      this.armatureDisplay.x = 400.0;
      this.armatureDisplay.y = 1000.0;
      this.armatureDisplay.scale.set(this.scale, this.scale);
      this.PIXIWrapper.PIXIApp.stage.addChild(this.armatureDisplay);

      this.PIXIWrapper.PIXIApp.stage.interactive = true;
      this.PIXIWrapper.PIXIApp.stage.on('touchmove', this.touchHandler);
      this.PIXIWrapper.PIXIApp.stage.on('mousemove', this.touchHandler);

      this.PIXIWrapper.PIXIApp.start();
      this.PIXIWrapper.PIXI.Ticker.shared.add(this.enterFrameHandler);

    },

    touchHandler(event) {
        const x = event.data.global.x;
        const y = event.data.global.y;

        this.target.x += ((x - this.PIXIWrapper.PIXIApp.stage.x - this.armatureDisplay.x) / 0.4 - this.target.x) * 0.3;
        this.target.y += ((y - this.PIXIWrapper.PIXIApp.stage.y - this.armatureDisplay.y) / this.scale - this.target.y) * 0.3;
    },

    enterFrameHandler() {
    const armature = this.armatureDisplay.armature;
    const animation = this.armatureDisplay.animation;
    const canvas = armature.armatureData.canvas;

    let p = 0.0;
    const pX = Math.max(Math.min((this.target.x - canvas.x) / (canvas.width * 0.5), 1.0), -1.0);
    const pY = -Math.max(Math.min((this.target.y - canvas.y) / (canvas.height * 0.5), 1.0), -1.0);

    
    for (const animationName of this.animationNames) {
        if (animation.hasAnimation(animationName)) {
            let animationState = animation.getState(animationName, 1);
            if (!animationState) {
                animationState = animation.fadeIn(animationName, 0.1, 1, 1, animationName);
                if (animationState) {
                    animationState.resetToPose = false;
                    animationState.stop();
                }
            }

            if (animationState) {
                switch (animationName) {
                    case 'goat_look_X':
                        p = (pX + 1.0) * 0.5;
                        break;

                    case 'goat_look_Y':
                        p = (pY + 1.0) * 0.5;
                        break;

                    case 'goat_idle_anim':
                        p = (Math.sin(armature.clock.time) + 1.0) * 0.5;
                        break;
                    
                    default:
                        break;
                }
                animationState.currentTime = p * animationState.totalTime;
       }

       }
     }
  }
}
}
</script>