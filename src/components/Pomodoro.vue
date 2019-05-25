<template>
  <v-container>
    <h2>Pomodoro</h2>
    <h3>Count ten</h3>
    <v-progress-circular
            :rotate="360"
            :size="400"
            :width="20"
            :value="value"
            :button="true"
            color="#ED4726"
    >
      {{ remainMinutes }}:{{ remainSeconds }}
    </v-progress-circular>
  </v-container>
</template>

<script lang="ts">
  import {Component, Vue} from 'vue-property-decorator';

  @Component({
    components: {},
  })
  export default class Pomodoro extends Vue {
    private interval: number = 0;
    private value: number = 0;
    private remainTime: number = 0;

    private beforeDestroy() {
      clearInterval(this.interval);
    }

    private mounted() {
      this.setTimer(60 * 25);
    }

    get remainMinutes(): number {
      return Math.floor(this.remainTime / 60);
    }

    get remainSeconds(): number {
      return this.remainTime % 60;
    }

    private setTimer(seconds: number) {
      this.remainTime = seconds - 1;
      this.interval = setInterval(() => {
        this.value += (100 / seconds);
        this.remainTime -= 1;
        if (this.value >= 100) {
          this.remainTime = seconds;
          return (this.value = 0);
        }
      }, 1000);
    }
  }
</script>

<style lang="stylus">
  .v-progress-circular__info
    font-size: 4em
</style>
