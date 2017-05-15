<template>
	<div class="checkbox" :class="disabled&&'disabled'">
		<span class="picker" :class="!disabled&&value&&'selected'" @click="onIsCheckChange"></span>
		<span class="checkbox-val">
			<slot v-if="!label"></slot>{{label}}
		</span>
		<input :value="value">
	</div>
</template>
<script>
	export default{
		props:{
			'value':{
				'type':Boolean,
				'default':false
			},
			'disabled':{
				'type':Boolean,
				'default':false
			},
			'label':{//选项的备注或描述
				'type':String,
				'default':''
			}
		},
		methods:{
			onIsCheckChange(e){
				if(!this.disabled){
					this.$emit('input',!this.value);					
				}
			}
		}
	}

</script>
<style lang="scss" scoped>
	.checkbox{
		display: inline-flex;
		align-items: center;
		position: relative;
		.picker{
			display: inline-block;
			width:18px;
			height:18px;
			background-color: #fff;
			border:1px solid #0190fe;
			border-radius:2px;
			margin-right: 10px;
		}
		.picker.selected{
			background-color: #20a0ff;
		}
		.picker.selected:after{
			content:'';
			display:block;
			width:5px;
			height:10px;
			align-self: center;
			border-right:2px solid #fff;
			border-bottom:2px solid #fff;
			transform:translate(6px,1px) rotate(45deg);
		}
		input{
			display: none;
		}
	}
	.disabled{
		cursor:not-allowed;
		color:#bbb;
		.picker{
			border-color:#d1dbe5;
			background-color: #eef1f6;
		}
	}

</style>