<template>
  <div class="srceen">
    <div
      class="srceen__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 +
            16) *
          Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <card-flip
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="`card-${index}`"
        :imgBackFaceUrl="`images/${card}.png`"
        :card="{ index: index, value: card }"
        :cardsContext="cardsContext"
        @onFlip="checkRule($event)"
      />
    </div>
  </div>
</template>
<script>
import CardFlip from "./CardGame.vue";
export default {
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  data() {
    return {
      rules: [],
    };
  },
  components: {
    CardFlip,
  },
  methods: {
    checkRule(card) {
      if (this.rules.length === 2) return false;
      this.rules.push(card);

      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value
      ) {
        console.log("Right");
        this.$refs[`card-${this.rules[0].index}`][0].onEnable();
        this.$refs[`card-${this.rules[1].index}`][0].onEnable();
        this.rules = [];
        const disableElements = document.querySelectorAll(
          ".srceen .card.disable"
        );
        if (
          disableElements &&
          disableElements.length === this.cardsContext.length - 2
        ) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 1500);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        console.log("Wrong");
        setTimeout(() => {
          //close 2 cards
          this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard();
          this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard();
          //reset rules
          this.rules = [];
          //reset is flipping
          this.isFlipping = false;
        }, 800);
      } else return false;
    },
  },
};
</script>

<style>
.srceen {
  width: 100%;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}

.srceen__inner {
  width: calc(424px);
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>
