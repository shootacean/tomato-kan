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
      <v-container>
        <v-layout align-center column>
          <p>
            {{ remainMinutes }}:{{ remainSeconds }}
          </p>
          <v-btn flat icon color="blue lighten-2"
                 @click="startTimer(pomodoroTime)"
                 v-if="!isCounting">
            <v-icon>play_arrow</v-icon>
          </v-btn>
          <v-btn flat icon color="red lighten-2"
                 @click="stopTimer"
                 v-if="isCounting">
            <v-icon>stop</v-icon>
          </v-btn>
        </v-layout>
      </v-container>
    </v-progress-circular>
  </v-container>
</template>

<script lang="ts">
  import {Component, Vue} from 'vue-property-decorator';

  @Component({
    components: {},
  })
  export default class Pomodoro extends Vue {
    private isCounting: boolean = true;
    private interval: number = 0;
    private value: number = 0;
    private remainTime: number = 0;
    private pomodoroTime: number = 60 * 25;

    private beforeDestroy() {
      clearInterval(this.interval);
    }

    private mounted() {
      this.startTimer(this.pomodoroTime);
    }

    get remainMinutes(): string {
      return Math.floor(this.remainTime / 60).toString().padStart(2, '0');
    }

    get remainSeconds(): string {
      return (this.remainTime % 60).toString().padStart(2, '0');
    }

    private startTimer(seconds: number) {
      clearInterval(this.interval);
      this.remainTime = seconds - 1;
      this.value = 0;
      this.interval = setInterval(() => {
        this.value += (100 / seconds);
        this.remainTime -= 1;
        if (this.value >= 100) {
          this.remainTime = seconds;
          return (this.value = 0);
        }
      }, 1000);
      this.isCounting = true;
    }

    private stopTimer(seconds: number) {
      clearInterval(this.interval);
      this.remainTime = this.pomodoroTime;
      this.isCounting = false;
    }
  }
</script>

<style lang="stylus">
  .v-progress-circular__info
    font-size: 4em
</style>
