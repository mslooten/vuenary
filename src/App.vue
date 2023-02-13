<script setup lang="ts">
import {
  darkTheme,
  NConfigProvider,
  NGradientText,
  NSpace,
  useOsTheme,
} from "naive-ui";
import { computed, onMounted, ref, watch } from "vue";
import BinarySwitch from "./components/BinarySwitch.vue";

const osThemeRef = useOsTheme();
const theme = computed(() => (osThemeRef.value === "dark" ? darkTheme : null));

type binaryNumbers = 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1;
const keys = ["a", "s", "d", "f", "h", "j", "k", "l"];

const binary: binaryNumbers[] = [128, 64, 32, 16, 8, 4, 2, 1];

const answer = ref([false, false, false, false, false, false, false, false]);
const currentValue = ref(0);
const points = ref(0);
const question = ref(Math.floor(Math.random() * 256));

const wrapper = ref<HTMLElement | null>(null);

const focusWrapper = () => {
  wrapper.value?.focus();
};

onMounted(() => wrapper.value?.focus());

const reset = () => {
  question.value = Math.floor(Math.random() * 256);
  points.value++;
  answer.value = [false, false, false, false, false, false, false, false];
  currentValue.value = 0;
};

const updateAnswer = (index: number, value: boolean) => {
  answer.value.splice(index, 1, value);
  currentValue.value = answer.value.reduce(
    (acc: number, curr: boolean, index: number) => {
      if (curr) {
        return acc + binary[index];
      } else {
        return acc + 0;
      }
    },
    0
  );
};

watch(currentValue, () => {
  if (currentValue.value === question.value) {
    reset();
  }
});

const toggleSwitch = (key: any) => {
  const index = keys.indexOf(key.key);
  if (index > -1) {
    updateAnswer(index, !answer.value[index]);
  }
};
</script>
<template>
  <n-config-provider :theme="theme">
    <div
      class="wrapper"
      @keypress="toggleSwitch"
      ref="wrapper"
      tabindex="0"
      @focusout="focusWrapper"
    >
      <h1>
        <n-gradient-text :size="48" type="success"> Vuenary </n-gradient-text>
      </h1>
      <h2>Create {{ question }} in binary</h2>
      <p>You have {{ points }} points</p>
      <br />

      <n-space :size="16" :align="'center'">
        <BinarySwitch
          v-for="(binaryNr, index) in binary"
          :key="binaryNr.toString()"
          :checked="answer[index]"
          :label="binaryNr.toString()"
          :keySwitch="keys[index]"
          @checkbox="(value) => updateAnswer(index, value)"
        />
      </n-space></div
  ></n-config-provider>
</template>
<style>
.wrapper {
  height: 100%;
  min-height: 600px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.wrapper:focus {
  outline: none;
  border: none;
  box-shadow: none;
}

.switches {
  display: flex;
}

.n-base-close {
  z-index: 999;
}
</style>
