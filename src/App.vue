<script setup lang="ts">
import {
  darkTheme,
  NConfigProvider,
  NGradientText,
  useOsTheme,
} from "naive-ui";
import { computed, onMounted, ref, watch } from "vue";
import Auth from "./components/Auth.vue";
import SwitchPanel from "./components/SwitchPanel.vue";
import { supabase } from "./supabase";

const session = ref();

onMounted(async () => {
  await supabase.auth.getSession().then(({ data }) => {
    session.value = data.session;
  });

  supabase.auth.onAuthStateChange((_, _session) => {
    session.value = _session;
  });

  getScore();
});

// Make sure Naive UI switches darkmode
const osThemeRef = useOsTheme();
const theme = computed(() => (osThemeRef.value === "dark" ? darkTheme : null));

// Set up the refs
const currentValue = ref(0);
const displayValue = ref("00000000");
const points = ref(0);
const question = ref(1);

// Methods
const newBinary = (): void => {
  question.value = Math.floor(Math.random() * 254) + 1;
};

const newRound = () => {
  newBinary();
  points.value++;
  updateScore(points.value);
  currentValue.value = 0;
};

async function getScore() {
  try {
    const { user } = session.value;
    const { data, error } = await supabase
      .from("highscores")
      .select("score")
      .eq("user_id", user?.id);
    if (error) throw error;
    points.value = parseInt(data[0].score) || 0;
  } catch (err) {
    console.log(err);
  }
  if (points.value > 0) {
    newBinary();
  }
}

async function updateScore(score: number) {
  try {
    const { user } = session.value;

    const updates = {
      user_id: user.id,
      score: score,
    };

    let { error } = await supabase.from("highscores").upsert(updates);

    if (error) throw error;
  } catch (err) {
    console.log(err);
  }
}

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
    <div class="wrapper" v-if="session" :session="session">
      <p>Welcome {{ session?.user?.email }}</p>
      <h1>
        <n-gradient-text :size="48" type="success"> Vuenary </n-gradient-text>
      </h1>
      <h2>Create {{ question }} in binary</h2>
      <p>You have {{ points }} points</p>
      <br />

      <p style="font-family: monospace; font-size: 20px">
        {{ displayValue }}
      </p>
      <SwitchPanel @answerUpdate="updateAnswer" :question="question" />
    </div>

    <Auth v-else
  /></n-config-provider>
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
