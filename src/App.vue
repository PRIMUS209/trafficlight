<template>
  <div id="app">
    <header>
      <h1>Эмулятор светофора</h1>
    </header>
    <main>
      <div class="stoplight">
        <router-view></router-view>
        <sl-timer :counter="counter"></sl-timer>
      </div>
    </main>
  </div>
</template>

<script>
import slTimer from './components/sl-timer.vue'
import { timeSettings } from './main.js'
export default {
  name: 'app',
  components: {
    slTimer
  },
  data () {
    return {
      previousScreen: null,
      nextScreen: null,
      counter: 0,
      timeouts: []
    }
  },
  created(){
    this.initNextScreen(this.$route.path);
  },
  mounted() {
    this.$router.afterEach((to, from) => {
      this.initNextScreen(this.$route.path);
      this.timeouts.forEach(timeout => {clearTimeout(timeout)});
      this.timeouts = [];
      this.countDown();
    });
    this.countDown();
  },
  methods: {
    initNextScreen(path){
      switch (path){
        case '/':
        case '/green':
          this.counter = timeSettings.timeGreen;
          this.nextScreen = '/yellow';
          this.previousScreen = '/green';
          break;
        case '/yellow':
          this.counter = timeSettings.timeYellow;
          if (this.previousScreen == '/green'){
            this.nextScreen = '/red';} else {
            this.nextScreen = '/green';}
          break;
        case '/red':
          this.counter = timeSettings.timeRed;
          this.nextScreen = '/yellow';
          this.previousScreen = '/red';
          break;
      }
    },
    countDown(){
      if (this.counter) {
        let newTimeout = setTimeout(() => {
          --this.counter;
            this.countDown()
        }, 1000);
        this.timeouts.push(newTimeout);
        return newTimeout;
      }
      this.$router.push(this.nextScreen);
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
