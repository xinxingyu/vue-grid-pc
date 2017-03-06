<template>
  <div id="app">
	<Grid
		v-on:on-select="onSelect"
		:choice-page-num="choicePageNum"
		:handle="gridData.handle"
		:data="gridData.data"
		:cols="gridData.cols"
		:paging="gridData.paging"
		:theme="{
			headBorderColor: '#e7e7e7',
			borderColor: '#e7e7e7',
			headBgColor: '#f8f8f8',
			fontColor: '#888',
			opacity: 1,
			choosedColor: '#f8f8f8'
		}"
		>
	</Grid>
  </div>
</template>

<script>
import Grid from 'components/'
// import Grid from '../lib/components/'

export default {
	components: {
		Grid,
	},
	data () {
		return {
			gridData: {
				data: [],
				cols: [],
				paging: {
					totalNum: 100,
					pageSize: 20
				}
			},
		}
	},
	created(){
		var _this = this

		/**
		 * the cols setting for grid component
		 */
		this.gridData.cols = [{
			name: '任务ID',
			key: 'id',
			width: 50,
		},{
			name: '创建时间',
			key: 'ts',
			width: 70,
			format(key, val, item){
				return (new Date(val)).toLocaleDateString();
			}
		},{
			name: '优先级',
			key: 'priority',
			width: 50,
			format(key, val, item){
				var mok = {
					1: '低级',
					2: '高级'
				}
				return mok[item.priority]
			}
		},{
			name: '任务来源',
			key: 'source',
			width: 70
		},{
			name: '行政区划',
			key: 'province',
			width: 70,
		}]

		/**
		 * add operated button to grid component
		 * 'rule' is used to match RegExp with operate
		 */
		this.gridData.handle = {
			width: 100,
			operateList: [{
				name: '处理',
				rule: '{priority}==1',
				callback: (row) => {
					//回调处理
					console.log(row);
				}
			},{
				name: '查看',
				callback: (row) => {
					//回调处理
					console.log(row);
				}
			},{
				name: '关闭',
				callback: (row) => {
					//回调处理
					console.log(row);
				}
			}]
		}
	},
	mounted(){
		this.gridData.data = [{
			id: 1,
			ts: "2017/1/1 12:50",
			province: '北京市',
			source: '来源1',
			priority: 1
		},{
			id: 2,
			ts: "2017/1/1 12:50",
			province: '北京市',
			source: '来源2',
			priority: 2
		},{
			id: 3,
			ts: "2017/1/1 12:50",
			province: '北京市',
			source: '来源3',
			priority: 1
		},{
			id: 4,
			ts: "2017/1/1 12:50",
			province: '北京市',
			source: '来源4',
			priority: 2
		}]
	},
	methods: {
		/**
		 * Grid组件单选回调
		 */
		onSelect(res){
			// console.log(res);
		},
		/**
		 * 选择页码的回调
		 */
		choicePageNum(pagenum, paging){
			console.log(pagenum);
		},
	}
}
</script>

<style lang="less">
#app {
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	color: #2c3e50;
	font-size: 14px;
	box-sizing: border-box;
	.lable{
		display: inline-block;
		width: 100px;
	}
	.calendar{
		display: inline-block;
		width: 200px;
		margin-bottom: 20px;
	}
}
</style>
