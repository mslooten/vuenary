<script setup lang="ts">
import { NSpace } from "naive-ui";
import { onMounted, ref, watch } from "vue";
import BinarySwitch from "./BinarySwitch.vue";

const props = defineProps<{ question: number }>();

const emit = defineEmits<{
  (e: "answerUpdate", value: number): void;
}>();

// CONSTANTS
const BINARY = [128, 64, 32, 16, 8, 4, 2, 1];
const KEYS = ["a", "s", "d", "f", "h", "j", "k", "l"];

// REFS
const switchState = ref([
  false,
  false,
  false,
  false,
  false,
  false,
  false,
  false,
]);
const currentValue = ref(0);

// Events
const answerUpdate = (index: number, value: boolean) => {
  switchState.value.splice(index, 1, value);
  currentValue.value = switchState.value.reduce(
    (acc: number, curr: boolean, index: number) => {
      if (curr) {
        return acc + BINARY[index];
      } else {
        return acc + 0;
      }
    },
    0
  );
  emit("answerUpdate", currentValue.value);
};

const keyPressed = (event: any) => {
  const value = event.key;

  const index = KEYS.indexOf(value);
  if (index > -1) {
    answerUpdate(index, !switchState.value[index]);
  }
};

// Force focus for the keyboard events
const switches = ref<HTMLElement | null>(null);

onMounted(() => forceFocus());

const forceFocus = () => {
  switches.value?.focus();
};

watch(
  () => props.question,
  () => {
    switchState.value = [
      false,
      false,
      false,
      false,
      false,
      false,
      false,
      false,
    ];
  }
);
</script>
<template>
  <div
    tabindex="-1"
    @keypress="keyPressed"
    ref="switches"
    class="switches"
    @focusout="forceFocus"
  >
    <n-space :size="12" align="center" justify="center">
      <BinarySwitch
        v-for="(binaryNr, index) in BINARY"
        :key="binaryNr.toString()"
        :checked="switchState[index]"
        :label="binaryNr.toString()"
        :keySwitch="KEYS[index]"
        @checkbox="(value) => answerUpdate(index, value)"
      />
    </n-space>
  </div>
</template>

<style scoped>
.switches:focus {
  outline: none;
  border: none;
  box-shadow: none;
}
</style>
