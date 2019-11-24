<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <input type="text" v-model="parentInput">
    <Child :value="parentInput" editable="ture" minHeight="200px"/>
  
  </div>
</template>

<script>
import Child from './Child'
export default {
  name: 'Parent',
  components: {
    Child
  },
  props: {
    msg: String
  },
   data() {
      return {
       parentInput:'d'
        }
   }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
