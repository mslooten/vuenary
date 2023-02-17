<script setup lang="ts">
import {
  darkTheme,
  NConfigProvider,
  NGradientText,
  useOsTheme,
} from "naive-ui";
import { computed, ref, watch } from "vue";
import SwitchPanel from "./components/SwitchPanel.vue";

// Make sure Naive UI switches darkmode
const osThemeRef = useOsTheme();
const theme = computed(() => (osThemeRef.value === "dark" ? darkTheme : null));

// Set up the refs
const currentValue = ref(0);
const displayValue = ref("00000000");
const points = ref(0);
const question = ref(1);

// Methods
const newBinary = (complexity: number, randomize: boolean): void => {
  const random = Math.floor(Math.random() * 255) + 1;
  const inBinary = random.toString(2);
  if (
    complexity >= 6 ||
    checkComplexity(inBinary) === complexity ||
    randomize
  ) {
    question.value = random;
  } else {
    return newBinary(complexity, randomize);
  }
};

const checkComplexity = (binaryArr: string): number => {
  const length = binaryArr.split("").filter((val) => val === "1").length; // between 0 and 8
  return 7 - Math.abs(6 - length); // Between 1 and 7
};

const newRound = () => {
  newBinary(Math.round(points.value / 2) + 1, Math.random() >= 0.5);
  points.value++;
  currentValue.value = 0;
};

const updateAnswer = (value: any) => {
  currentValue.value = value;
  displayValue.value = currentValue.value.toString(2).padStart(8, "0");
};

watch(currentValue, () => {
  if (currentValue.value === question.value) {
    newRound();
  }
});
</script>
<template>
  <n-config-provider :theme="theme">
    <div class="wrapper">
      <h1>
        <n-gradient-text :size="48" type="success"> Vuenary </n-gradient-text>
      </h1>
      <h2>Create {{ question }} in binary</h2>
      <p>You have {{ points }} points</p>
      <br />

      <p style="font-family: monospace; font-size: 20px">
        {{ displayValue }}
      </p>
      <SwitchPanel @answerUpdate="updateAnswer" :question="question" /></div
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
</style>
