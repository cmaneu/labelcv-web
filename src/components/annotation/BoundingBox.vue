<template>
  <div
      :id="`bounding-box-${id}`"
      :class="{
        'bounding-box': true,
        'bounding-box-selected': selected,}"
      :style="{
        left: `${x - 2}px`, top: `${y - 5}px`,
        width: `${width + 4}px`, height: `${height + 9}px`, }"
      @click="onMouseClick">
    <div class="bounding-box-header" @click="onMouseClick"></div>
    <div
        class="bounding-box-content"
        :style="{
          width: `${width}px`,
          height: `${height}px`, }"
        @click="onMouseClick"></div>
  </div>
</template>

<script lang="ts">
import Vue, { VNode } from 'vue';
import Component from 'vue-class-component';
import { getModule } from 'vuex-module-decorators'

import AnnotationStore from '@/store/store.annotation';

import Box from '@/models/geometry/box';
import Size from '@/models/geometry/size';

@Component({
  props:['id'],
})
export default class BoundingBox extends Vue {

  /** */
  private readonly state: AnnotationStore = getModule(AnnotationStore);

  /** */
  private hovered: boolean = false;

  get box(): Box {
    const id = this.$props.id;
    // TODO: boundaries control.
    return this.state.boxes[id];
  }
  
  get reverseRatio(): Size {
    return {
      width: 1 / this.state.imageRatio.width,
      height: 1 / this.state.imageRatio.height,      
    };
  }

  get selected(): boolean {
    return this.state.selectedBox == this.$props.id;
  }

  get x(): number {
    return Math.round(this.reverseRatio.width * this.box.x);
  }

  get y(): number {
    return Math.round(this.reverseRatio.height * this.box.y);
  }

  get width(): number {
    return Math.round(this.reverseRatio.width * this.box.width);
  }

  get height(): number {
    return Math.round(this.reverseRatio.height * this.box.height);
  }

  private onMouseClick(event: MouseEvent): void {
    if (this.$el == event.target) {
      this.state.select(this.$props.id);
    }
  }

}
</script>

<style scoped>
.bounding-box {
  position: absolute;
  cursor: pointer;
}

.bounding-box-header {
  height: 5px;
  width: 100%;
  background: greenyellow;
}

.bounding-box-content {
  border: 2px solid greenyellow;
}

.bounding-box-selected .bounding-box-header {
  background: rgb(105, 40, 13);
}

.bounding-box-selected .bounding-box-content {
  border: 2px solid rgb(105, 40, 13);
}
</style>