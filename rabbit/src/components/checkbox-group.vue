<template>
	<div class="checkbox-group">
		<slot :label="value[0]"></slot>
		{{value[0]}}
	</div>
</template>

<script>
	export default{
		data(){
			return {}
		},
		props:{
			'value':{
				'type':Array,
				'default':[]
			}
		},
		methods:{
			test(e){
				console.log(e)
			}
		}
	}
</script>

<style scoped>
	
</style>