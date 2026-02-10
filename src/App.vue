<script setup lang="ts">
import { UseDraggable as Draggable } from "@vueuse/components";
import { isClient, useDraggable } from "@vueuse/core";
import { ref, shallowRef } from "vue";

const el = ref<HTMLElement | null>(null);
const handle = ref<HTMLElement | null>(null);

const innerWidth = isClient ? window.innerWidth : 200;

const disabled = shallowRef(false);

const { x, y, style } = useDraggable(el, {
  initialValue: { x: innerWidth / 4.2, y: 80 },
  preventDefault: true,
  disabled,
});
</script>

<template>
  <div>
    <div>
      <label>
        <input type="checkbox" v-model="disabled" />
        <span>Disabled drag and drop</span>
      </label>
    </div>

    <!-- useDraggable composable -->
    <div ref="el" :style="style" class="draggable-box">
      Drag me! I am at {{ x }}, {{ y }}
    </div>

    <Draggable
      class="draggable-box"
      v-slot="{ x, y }"
      :initial-value="{ x: innerWidth / 3.9, y: 150 }"
      prevent-default
      storage-key="vueuse-draggable-pos"
      storage-type="session"
      :disabled="disabled"
    >
      Renderless component
      <div class="hint">Position persisted in sessionStorage</div>
      <div>{{ Math.round(x) }}, {{ Math.round(y) }}</div>
    </Draggable>

    <Draggable
      class="draggable-box"
      v-slot="{ x, y }"
      :initial-value="{ x: innerWidth / 3.6, y: 240 }"
      :prevent-default="true"
      :handle="handle"
      :disabled="disabled"
    >
      <div ref="handle" class="handle">👋 Drag here!</div>
      <div class="hint">Handle that triggers the drag event</div>
      <div>{{ Math.round(x) }}, {{ Math.round(y) }}</div>
    </Draggable>

    <Draggable
      class="draggable-box"
      v-slot="{ x, y }"
      :initial-value="{ x: innerWidth / 3.3, y: 330 }"
      prevent-default
      :disabled="disabled"
      :capture="false"
    >
      Not Use Captured Element
      <div class="no-drag" @pointerdown.stop>Dragging here will not work</div>
      <div>{{ Math.round(x) }}, {{ Math.round(y) }}</div>
    </Draggable>
  </div>
</template>

<style lang="scss">
.draggable-box {
  position: fixed;
  padding: 12px 16px;
  background: #fff;
  border-radius: 10px;
  border: 1px solid rgba(0, 0, 0, 0.08);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.12);
  cursor: grab;
  user-select: none;
  transition:
    box-shadow 0.2s ease,
    transform 0.15s ease;

  &:hover {
    box-shadow: 0 12px 28px rgba(0, 0, 0, 0.18);
  }

  &:active {
    cursor: grabbing;
    transform: scale(1.02);
  }

  .hint {
    margin-top: 4px;
    font-size: 12px;
    color: #888;
  }

  .handle {
    cursor: grab;
    font-weight: 600;
  }

  .no-drag {
    margin-top: 6px;
    font-size: 12px;
    color: #c0392b;
    cursor: default;
  }
}
</style>
