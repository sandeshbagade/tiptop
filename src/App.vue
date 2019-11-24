<template>
  <div id="app">
    <Parent msg="Welcome to Your Vue.js App"/>
    <Link/>
    <Blockquote/>
    <AllFun/>

  </div>
</template>

<script>
import Parent from './components/Parent.vue'
import Link from './components/link'
import Blockquote from './components/Blockquote'

export default {
  name: 'app',
  components: {
    Parent,
    Link,
    Blockquote,
    
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
