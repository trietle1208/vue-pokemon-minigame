<template>
  <main-srceen
    v-if="statusMatch === 'default'"
    @onStart="onHandleBeforeStart($event)"
  />
  <interact-srceen
    v-if="statusMatch === 'match'"
    :cardsContext="settings.cardsContext"
    @onFinish="onGetResult"
  />
  <result-srceen
    :timer="timer"
    v-if="statusMatch === 'result'"
    @onStartAgain="statusMatch = 'default'"
  />
</template>

<script>
import MainSrceen from "./components/MainSrceen.vue";
import InteractSrceen from "./components/InteractSrceen.vue";
import ResultSrceen from "./components/ResultSrceen.vue";
import { shuffled } from "./ultils/array";
export default {
  name: "App",
  components: {
    MainSrceen,
    InteractSrceen,
    ResultSrceen,
  },
  data() {
    return {
      settings: {
        totalBlock: 0,
        cardsContext: [],
        startedAt: null,
      },
      statusMatch: "default",
      timer: 0,
    };
  },
  methods: {
    onHandleBeforeStart(config) {
      console.log("Running Handle Before Start...", config);
      this.settings.totalBlock = config.totalBlock;

      const firstCards = Array.from(
        { length: this.settings.totalBlock / 2 },
        (_, i) => i + 1
      );
      const secondCards = [...firstCards]; //clone array in ES6
      const cards = [...firstCards, ...secondCards];
      this.settings.cardsContext = shuffled(
        shuffled(shuffled(shuffled(cards)))
      );
      this.settings.startedAt = new Date().getTime();
      this.statusMatch = "match";
    },
    onGetResult() {
      this.timer = new Date().getTime() - this.settings.startedAt;
      console.log(this.timer);
      this.statusMatch = "result";
    },
  },
};
</script>
