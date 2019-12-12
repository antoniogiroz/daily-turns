<template>
  <div class="flex flex-col justify-center align-center">
    <div class="text-8xl md:text-10xl flex justify-center font-mono">
      <div class="relative">
        <span :class="counterClasses">{{ totalTime | time }}</span>
      </div>
    </div>
    <div class="mt-12 flex justify-center">
      <button class="button" @click="resetTimer" :disabled="!resetButton">
        <IconReset />
      </button>
      <button class="button primary" v-if="!timer" @click="startTimer">
        <IconPlay v-if="started" class="ml-1" />
        <span v-else class="text-white uppercase font-semibold">Start</span>
      </button>
      <button class="button primary" v-if="timer" @click="stopTimer">
        <IconPause />
      </button>
      <button class="button" @click="next" :disabled="!started">
        <IconForward
          class="w-10 h-10 inline fill-current text-gray-600 hover:text-gray-400"
        />
      </button>
    </div>
  </div>
</template>

<script>
import IconPlay from './icons/IconPlay.vue';
import IconPause from './icons/IconPause.vue';
import IconForward from './icons/IconForward.vue';
import IconReset from './icons/IconReset.vue';

export default {
  components: {
    IconPlay,
    IconPause,
    IconForward,
    IconReset
  },

  props: {
    seconds: {
      type: [String, Number],
      default: 120
    }
  },

  data() {
    return {
      started: false,
      timer: null,
      totalTime: this.seconds,
      resetButton: false,
      exceeded: false
    };
  },

  computed: {
    counterClasses() {
      if (this.totalTime < 0 || this.exceeded) {
        return 'text-red-600 exceeded';
      }
      if (this.totalTime <= 5) {
        return 'text-orange-400 blink';
      }
      if (this.totalTime <= 15) {
        return 'text-orange-400';
      }
      return 'text-teal-400';
    }
  },

  methods: {
    startTimer() {
      if (!this.started) {
        this.started = true;
        this.$emit('start');
      }
      this.timer = setInterval(() => this.countdown(), 1000);
      this.resetButton = false;
    },

    stopTimer() {
      clearInterval(this.timer);
      this.timer = null;
      this.resetButton = true;
    },

    resetTimer() {
      this.exceeded = false;
      this.totalTime = this.seconds;
      clearInterval(this.timer);
      this.timer = null;
      this.resetButton = true;
    },

    next() {
      const currentTotalTime = this.exceeded
        ? +this.seconds + this.totalTime
        : this.seconds - this.totalTime;
      this.$emit('next', currentTotalTime);
      this.resetTimer();
      this.startTimer();
    },

    padTime(time) {
      return (time < 10 ? '0' : '') + time;
    },

    countdown() {
      if (!this.exceeded && this.totalTime >= 1) {
        this.totalTime--;
      } else {
        this.exceeded = true;
        this.totalTime++;
      }
    }
  }
};
</script>
