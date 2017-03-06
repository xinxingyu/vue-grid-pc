# vue-grid-pc
<p>
    <img height="18" src="https://img.shields.io/badge/vue-v2.1.10-green.svg">
    <img height="18" src="https://img.shields.io/badge/npm-v0.0.1-blue.svg">
</p>

> grid component used for pc


### 展示
<p>
	<img width="600px" src="https://github.com/xinxingyu/vue-grid-pc/blob/master/static/img/grid.jpg">
<p>


## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

## DEMO

```javascript

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

```

### API
* 属性说明

|属性|说明|类型|默认值|是否必传|
|---|----|---|-----|-------|
|on-select|Grid组件单选回调|Function|默认datatime|否|
|choice-page-num|选择页码的回调|String|默认无|否|
|handle|操作按钮列表，用于每一行的操作|Object|数据格式参考`附录`|否|
|data|Grid component 的数据|Array|数据格式参考`附录`|是|
|cols|列属性|Array|数据格式参考`附录`|否|
|paging|分页数据：</br>totalNum: 100(总记录条数)</br>pageSize：20 (每页记录条数)|Object|-|是|
|theme|Grid component 的主题配色|Object|-|否|

> 附录

* handle 数据格式:
```javascript
	/**
	 * add operated button to grid component
	 * 'rule' is used to match RegExp with operate
	 */
	let handle = {
		width: 100,
		operateList: [{
			name: '处理',
			rule: '{priority}==1',
			callback: (row) => {
				console.log(row);
			}
		},{
			name: '查看',
			callback: (row) => {
				console.log(row);
			}
		},{
			name: '关闭',
			callback: (row) => {
				console.log(row);
			}
		}]
	}
```

* data 数据格式:
```javascript
	/**
	 * the data key is correspond with the cols's key
	 */
	let data = [{
		id: 1,
		ts: "2017/1/1 12:50",
		province: 'BeiJing',
		source: 'source1',
		priority: 1
	},{
		id: 2,
		ts: "2017/1/1 12:50",
		province: 'NanJing',
		source: 'source2',
		priority: 2
	}]
```

* cols 数据格式:
```javascript
	let cols = [{
		name: 'ID',
		key: 'id',
		width: 50,
	},{
		name: 'CreateTime',
		key: 'ts',
		width: 70,
		format(key, val, item){
			return (new Date(val)).toLocaleDateString();
		}
	},{
		name: 'Priority',
		key: 'priority',
		width: 50,
		format(key, val, item){
			var mok = {
				1: '低级',
				2: '高级'
			}
			return mok[item.priority]
		}
	}]
	/**
	 * name: 是显示在grid component中列头部的文字内容
	 * key: 是要显示的数据的哪个字段
	 * width: 是列的宽度
	 * format: 格式化数据
	 */
```

### 版本升级
#### 0.0.1
* 基本功能实现
