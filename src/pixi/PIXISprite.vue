<template>
  <div class="pixi-sprite">
  </div>
</template>


<script>

export default {
  inject: ['EventBus', 'PIXIWrapper','DragonBones'],
  props: ['x', 'y', 'msg','imagePath'],

  data() {
    return {
      sprite: null,
      animationNames: [],
      target: null,
      armatureDisplay: null,
      scale: 0.3
    }
  },
    created() {
    this.sprite = this.PIXIWrapper.PIXI.Sprite.from(this.imagePath);
    this.sprite.anchor.set(0.5);
    this.sprite.interactive = true;

    this.EventBus.$on('ready', () => {

    this.sprite.x = this.PIXIWrapper.PIXIApp.screen.width / 2;
    this.sprite.y = this.PIXIWrapper.PIXIApp.screen.height / 2;
    this.PIXIWrapper.PIXIApp.stage.addChild(this.sprite);  

    this.SetupDragonBone();

    });
},

  methods: {
    SetupDragonBone(){
      console.log('setup dragon bones');
      this.target = new this.PIXIWrapper.PIXI.Point();

      this.animationNames = [
          'PARAM_ANGLE_X',
          'PARAM_ANGLE_Y',
          'PARAM_ANGLE_Z',
          'PARAM_EYE_BALL_X',
          'PARAM_EYE_BALL_Y',
          'PARAM_BODY_X',
          'PARAM_BODY_Y',
          'PARAM_BODY_Z',
          'PARAM_BODY_ANGLE_X',
          'PARAM_BODY_ANGLE_Y',
          'PARAM_BODY_ANGLE_Z',
          'PARAM_BREATH',
      ];

      this.PIXIWrapper.PIXI.Loader.shared
      .add('skeleton', 'eyetracking/shizuku_ske.json')
      .add('texture_png_0', 'eyetracking/texture_00.png')
      .add('texture_png_1', 'eyetracking/texture_01.png')
      .add('texture_png_2', 'eyetracking/texture_02.png')
      .add('texture_png_3', 'eyetracking/texture_03.png')
      .load(this.onAssetsLoaded);
    },

    onAssetsLoaded(loader, res){
      console.log('on assets load')
      const factory = this.DragonBones.PixiFactory.factory;

      factory.parseDragonBonesData(res.skeleton.data, 'shizuku');
      factory.updateTextureAtlases([res.texture_png_0.texture, res.texture_png_1.texture, res.texture_png_2.texture, res.texture_png_3.texture], 'shizuku');
      this.armatureDisplay = factory.buildArmatureDisplay('shizuku', 'shizuku');
      this.armatureDisplay.animation.play('idle_00');
      this.armatureDisplay.x = 400.0;
      this.armatureDisplay.y = 500.0;
      this.armatureDisplay.scale.set(this.scale, this.scale);
      this.PIXIWrapper.PIXIApp.stage.addChild(this.armatureDisplay);

      this.PIXIWrapper.PIXIApp.stage.interactive = true;
      this.PIXIWrapper.PIXIApp.stage.on('touchmove', this.touchHandler);
      this.PIXIWrapper.PIXIApp.stage.on('mousemove', this.touchHandler);


      this.PIXIWrapper.PIXI.Ticker.shared.add(this.enterFrameHandler);
      this.EventBus.$emit('girlLoaded');
    
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
                    case 'PARAM_ANGLE_X':
                    case 'PARAM_EYE_BALL_X':
                        p = (pX + 1.0) * 0.5;
                        break;

                    case 'PARAM_ANGLE_Y':
                    case 'PARAM_EYE_BALL_Y':
                        p = (pY + 1.0) * 0.5;
                        break;

                    // case 'PARAM_ANGLE_Z':
                    //     p = (-pX * pY + 1.0) * 0.5;
                    //     break;

                    // case 'PARAM_BODY_X':
                    // case 'PARAM_BODY_ANGLE_X':
                    //     p = (pX + 1.0) * 0.5;
                    //     break;

                    // case 'PARAM_BODY_Y':
                    // case 'PARAM_BODY_ANGLE_Y':
                    //     p = (-pX * pY + 1.0) * 0.5;
                    //     break;

                    // case 'PARAM_BODY_Z':
                    // case 'PARAM_BODY_ANGLE_Z':
                    //     p = (-pX * pY + 1.0) * 0.5;
                    //     break;

                    case 'PARAM_BREATH':
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