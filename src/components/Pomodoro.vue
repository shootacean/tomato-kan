<template>
  <v-container>
    <v-layout align-center column>
      <v-progress-circular
              :rotate="-90"
              :size="400"
              :width="20"
              :value="progressPercent"
              :button="true"
              :color="pomodoroColor"
      >
        <v-container>
          <v-layout align-center column>
            <p>{{ remainMinutes }}:{{ remainSeconds }}</p>
            <p>
              <v-layout align-center>
                <v-icon small color="red"
                        v-for="(_, i) in todayPomodoros"
                >brightness_1
                </v-icon>
              </v-layout>
            </p>
            <v-btn flat icon color="red lighten-2"
                   @click="startFocusTimer"
                   v-if="!isCounting">
              <v-icon x-large>play_arrow</v-icon>
            </v-btn>
            <v-btn flat icon color="red lighten-2"
                   @click="stopTimer"
                   v-if="isCounting">
              <v-icon x-large>stop</v-icon>
            </v-btn>
          </v-layout>
        </v-container>
      </v-progress-circular>
      <v-layout align-center column>
        <v-btn flat icon color="black lighten-2"
               @click="incrementInterrupt">
          <v-icon x-large>stop</v-icon>
        </v-btn>
        <p>
          <v-layout align-center>
            <v-icon small color="black"
                    v-for="(_, i) in interruptCount"
            >brightness_1
            </v-icon>
          </v-layout>
        </p>
      </v-layout>
    </v-layout>
  </v-container>
</template>

<script lang="ts">
  import {Component, Vue} from 'vue-property-decorator';
  import push from 'push.js';

  type PomodoroColor = '#ED4726' | '#9fed52';

  @Component({
    components: {},
  })
  export default class Pomodoro extends Vue {
    private isCounting: boolean = false;
    private isFocus: boolean = true;
    private interval: number = 0;
    private progressPercent: number = 0;
    private pomodoroColor: PomodoroColor = '#ED4726';
    private todayPomodoros: number = 0;
    private pomodoroTime: number = 3; // 60 * 25;
    private shortBreakTime: number = 2; // 60 * 5;
    private longBreakTime: number = 5; // 60 * 15;
    private remainTime: number = this.pomodoroTime;
    private interruptCount: number = 0;

    private beforeDestroy() {
      clearInterval(this.interval);
    }

    /**
     * 残り時間(分)
     */
    get remainMinutes(): string {
      return Math.floor(this.remainTime / 60).toString().padStart(2, '0');
    }

    /**
     * 残り時間(秒)
     */
    get remainSeconds(): string {
      return (this.remainTime % 60).toString().padStart(2, '0');
    }

    private startTimer(seconds: number): void {
      clearInterval(this.interval);
      this.progressPercent = 0;
      this.remainTime = seconds;
      // todo: refactoring
      this.interval = setInterval(() => {
        this.progressPercent += (100 / seconds);
        this.remainTime--;
        if (this.progressPercent >= 100) {
          // タイマー終了時
          this.remainTime = seconds;
          if (this.isFocus) {
            // Next is break
            this.isFocus = false;
            this.pushNotification('Take a break!');
            this.todayPomodoros++;
            if (this.todayPomodoros % 4 === 0) {
              this.startBreakTimer(true);
            } else {
              this.startBreakTimer(false);
            }
          } else {
            // Next is focus
            this.isFocus = true;
            this.pushNotification('Just focus!');
            this.switchMode(true);
            this.stopTimer();
          }
          return (this.progressPercent = 0);
        }
      }, 1000);
      this.isCounting = true;
    }

    private stopTimer(): void {
      clearInterval(this.interval);
      this.isCounting = false;
      this.isFocus = true;
      this.switchMode(true);
      this.progressPercent = 0;
      this.remainTime = this.pomodoroTime;
    }

    private startFocusTimer(): void {
      this.switchMode(true);
      this.startTimer(this.pomodoroTime);
    }

    private startBreakTimer(isLong: boolean): void {
      this.switchMode(false);
      const time = isLong ? this.longBreakTime : this.shortBreakTime;
      this.startTimer(time);
    }

    private switchMode(isFocus: boolean): void {
      this.pomodoroColor = isFocus ? '#ED4726' : '#9fed52';
    }

    private pushNotification(msg: string) {
      push.create(msg, {
        timeout: 5000,
      });
    }

    private incrementInterrupt(): void {
      this.interruptCount++;
    }
  }
</script>

<style lang="stylus">
  .v-progress-circular__info
    font-size: 4em
</style>
