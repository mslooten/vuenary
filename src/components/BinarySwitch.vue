<script setup lang="ts">
import { NCard, NSwitch } from "naive-ui";
import { computed, ref } from "vue";

const props = defineProps<{
  checked: boolean;
  label: string;
  keySwitch: string;
}>();

const emit = defineEmits(["checkbox"]);

const checked = ref(false);

const value = computed({
  get() {
    return props.checked;
  },
  set(val) {
    checked.value = val;
  },
});

const updateCheck = (value: boolean) => {
  emit("checkbox", value);
};
</script>

<template>
  <n-card @click="updateCheck(!value)" class="card">
    <p
      style="
        text-align: center;
        font-family: monospace;
        padding: 0;
        margin: 0 0 0.5rem 0;
        font-size: 20px;
      "
    >
      {{ props.label }}
    </p>
    <n-switch :value="value" ref="switch" size="large">
      <template #checked-icon> 1 </template>
      <template #unchecked-icon> 0 </template>
    </n-switch>
    <p
      style="
        margin: 1rem 0 0 0;
        padding: 0;
        text-align: center;
        font-size: 16px;
        opacity: 0.6;
        text-transform: uppercase;
      "
    >
      {{ props.keySwitch }}
    </p>
  </n-card>
</template>
<style scoped>
.card {
  cursor: pointer;
  border: 1px solid transparent;
}

.card:hover {
  border: 1px solid rgba(0255, 255, 255, 0.3);
}
</style>
