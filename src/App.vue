<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>tomato-kan</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      </v-btn>
    </v-toolbar>

    <v-content>
      <Pomodoro/>
    </v-content>
  </v-app>
</template>

<script>
import Pomodoro from './components/Pomodoro';

export default {
  name: 'App',
  components: {
    Pomodoro,
  },
  data() {
    return {
      //
    };
  },
};
</script>
