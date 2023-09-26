<template>
  <MyInterface/>
</template>

<script>
import MyInterface from './components/Interface.vue'

export default {
  name: 'App',
  components: {
        MyInterface
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#pic {
  width: 100px;
  height: 100px;
  float: left;
  margin-left: 8px;
}
</style>
