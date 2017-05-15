<template>
  <div id="app">
  	<checkbox-group v-model="checkList">
    	<checkbox-el></checkbox-el>
  	</checkbox-group>
  </div>
</template>

<script>
import checkboxEl from './components/checkbox.vue';
import checkboxGroup from './components/checkbox-group.vue';

export default {
  name: 'app',
  data () {
    return {
      'msg': 'Welcome to Your Vue.js App',
      'checkList':['复选框 A','复选框 B']
    }
  },
  components:{
  	'checkboxEl':checkboxEl,
  	'checkboxGroup':checkboxGroup
  },
  methods:{ }
}
</script>

<style lang="scss"></style>
