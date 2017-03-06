<template>
	<div class="GridComponent">
		<div class="grid-head">
			<table>
				<colgroup>
                    <col v-if="showSelect" width="30"></col>
                    <col v-for="(value, key) in cols" :width="value.width"></col>
					<col v-if="handle" :width="handle.width"></col>
                </colgroup>
                <thead :style="{backgroundColor: theme.headBgColor}">
                    <tr>
                        <td v-if="showSelect" :style="{borderColor: theme.headBorderColor}"><input type="checkbox" v-model="checkedAll" @click="selectAll" /></td>
                        <td v-for="(value, key) in cols" :style="{borderColor: theme.headBorderColor}" :title="value.name">{{value.name}}</td>
						<td v-if="handle" :style="{borderColor: theme.headBorderColor}">操作</td>
                    </tr>
                </thead>
			</table>
		</div>
		<div class="grid-body">
			<table>
				<colgroup>
                    <col v-if="showSelect" width="30"></col>
                    <col v-for="(value, key) in cols" :width="value.width"></col>
					<col v-if="handle" :width="handle.width"></col>
                </colgroup>
				<tbody>
					<template v-if="data">
						<template v-if="data.length > 0">
							<tr v-for="(row, index) in data"  :style="{borderColor: theme.borderColor}">
		                        <td v-if="showSelect" :style="{borderColor: theme.borderColor}"><input type="checkbox" v-model="selectedList" :value="row"></td>
		                        <td v-if="showIndex" :style="{borderColor: theme.borderColor}"  @click="onclick(row)">{{ index + 1}}</td>
								<td v-for="(value, key) in cols" @click="onclick(row)" v-html="format(value, row[value.key], row)" :style="{borderColor: theme.borderColor}"></td>

								<!-- add operate button by rules -->
								<td v-if="handle" :style="{borderColor: theme.borderColor}">
									<template v-for="(opt, optIndex) in handle.operateList">
										<template v-if="operateMatching(opt, row)">
											<a href="javascript:void(0)" class="link" @click="operate(row, index, opt, optIndex)">{{opt.name}}</a>
										</template>
										<template v-else>
											<a href="javascript:void(0)" class="link-disabled" contenteditable="false">{{opt.name}}</a>
										</template>
									</template>
									<!-- <a href="javascript:void(0)" class="link" v-for="(opt, optIndex) in handle.operateList" @click="operate(row, index, opt, optIndex)">{{opt.name}}</a> -->
								</td>
		                    </tr>
						</template>
						<template v-else>
	                        <tr :style="{borderColor: theme.borderColor}"><td class="noData" :colspan="handle ? cols.length+2 : cols.length+1"  :style="{borderColor: theme.borderColor}">暂无数据</td></tr>
	                        <tr :style="{borderColor: theme.borderColor}"></tr>
	                    </template>
					</template>
					<template v-else>
                        <tr :style="{borderColor: theme.borderColor}"><td class="loading" :colspan="handle ? cols.length+2 : cols.length+1"  :style="{borderColor: theme.borderColor}">数据加载中...</td></tr>
                        <tr :style="{borderColor: theme.borderColor}"></tr>
                    </template>
				</tbody>
			</table>
		</div>
		<div class="grid-paging">
			<div class="tips">
				<span>共{{totalPage}}页，{{paging.totalNum}}条记录</span>
			</div>
			<div class="operate">
				<a href="javascript:void(0)" class="prev" @click="prev" :class="{disabled: currentPage==1}" >«</a>
				<ul>
					<li v-for="page in pagingList"><a href="javascript:void(0)" :class="{active: page==currentPage}" @click="choicePage(page)">{{page}}</a></li>
				</ul>
				<a href="javascript:void(0)" class="next" @click="next" :class="{disabled: currentPage==totalPage}">»</a>
				<div class="jump">
					<input type="text" name="pagenum" class="jump-input" v-model="pagenum">
					<a href="javascript:void(0)" class="jump-btn" @click="jump">GO</a>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
	import Vue from 'vue/dist/vue.js';

	export default {
		props: {
			cols: {
				type: Array
			},
			data: {
				type: [Array, Object]
			},
			handle: {
				type: Object,
				default: {}
			},
			paging: {
				type: Object,
				default(){
					return {
						totalNum: 0,
						pageSize: 10
					}
				}
			},
			choicePageNum: {
				type: Function
			},
			/**
			 * 显示选择
			 */
			showSelect: {
				type: Boolean,
				default: true
			},
			showIndex: {
				type: Boolean,
				default: false
			},
			/** 表格主题颜色 **/
	        theme: {
	            type: Object,
	            default(){
	                return {
	                    headBgColor: '#f8f8f8',
	                    fontColor: '#888',
	                    borderColor: '#e7e7e7',
						headBorderColor: '#e7e7e7',
	                    opacity: 1,
	                    choosedColor: 'transparent'
	                }
	            }
	        }
		},
		data(){
			return {
				current: {},
	            onclick(item){
	                this.$emit('on-click',item)
	                this.current = item;
	            },
				selectedList: [],
				checkedAll: false,
				currentPage: 1,
				pagingList: [1],
				group: 15, //分页每组的个数
				pagenum: '',
			}
		},
		watch: {
			data(){
				//init grid when updata grid data
				this.initGrid()
			},
			paging: {
				handler(value){
					this.updatePaging()
				},
				deep: true
			},
	        selectedList(value){
	            if(value.length === this.data.length){
	                this.checkedAll = true;
	            }else{
	                this.checkedAll = false;
	            }
	            // 触发选择事件 返回所有选中项
	            this.$emit('on-select', value);
	        },
			currentPage(value){
				//触发回调
				this.choicePageNum && this.choicePageNum(value, this.paging)
			}
	    },
		computed: {
			totalPage(){
				return Math.ceil(this.paging.totalNum/this.paging.pageSize)
			}
		},
		created(){
			this.updatePaging()
		},
		mounted(){},
		methods: {
			updatePaging(){
				var list = []

				if(this.totalPage > this.group){
					for(let i = 0; i < this.group; i++){
						list.push(i+1)
					}
				}else{
					for(let i = 0; i < this.totalPage; i++){
						list.push(i+1)
					}
				}

				this.pagingList = list || [1]
			},
	        format(item, val, data){
	            if(item.hasOwnProperty('format')){
	                return item.format(item.key, val, data)
	            }
	            return val;
	        },
			initGrid(){
				this.selectedList = []
				this.checkedAll = []
			},
			selectAll(){
				if(this.selectedList.length == this.data.length){
                	this.selectedList = [];
	            }else{
	                this.selectedList = this.data;
	            }
			},
			/**
			 * 操作按钮按照 rule 进行正则匹配
			 */
			operateMatching(item, row){
				if(item.rule){
					var patrn = /\{[^\}]+\}/g;
					var matchs = item.rule.match(patrn); //匹配出的rules
					var rules = item.rule;
					matchs.forEach((mt, index) => {
						var key = mt
						key = key.replace('{', '')
						key = key.replace('}', '')
						rules = rules.replace(eval('/' + mt + '/g'), row[key]);
					})
					return eval(rules)
				}
				return true
			},
			/**
			 * 操作按钮 callback operate
			 */
			operate(row, rowIndex, opt, optIndex){
				this.handle.operateList[optIndex].callback && this.handle.operateList[optIndex].callback(row)
			},
			choicePage(page){
				this.currentPage = page
				this.pagingHandle()
			},
			prev(){
				if((this.currentPage-1) > 0){
					this.currentPage --
					this.pagingHandle()
				}
			},
			next(){
				if((this.currentPage+1) <= this.totalPage){
					this.currentPage ++
					this.pagingHandle()
				}
			},
			pagingHandle(){
				if(this.currentPage == this.pagingList[this.pagingList.length-1] && (this.currentPage+1)<=this.totalPage){
					//页码最后一个
					this.pagingList.shift()
					this.pagingList.push(this.currentPage+1)
				}else if(this.currentPage == this.pagingList[0] && (this.currentPage-1)>0){
					//页码第一个
					this.pagingList.unshift(this.currentPage-1)
					this.pagingList.pop()
				}
			},
			/**
			 * jump to a page
			 */
			jump(){
				if(this.pagenum && this.pagenum < this.totalPage && this.pagenum > 0){
					this.currentPage = this.pagenum
				}
			}
		}
	}
</script>

<style lang="less" scoped>
	.GridComponent{
		font-size: 12px;
		.grid-head{
			font-size: 14px;
			table{
				width: 100%;
				table-layout: fixed;
				text-align: center;
				border-collapse:collapse;
				td{
		            border-width: 1px;
		            border-style: solid;
		            cursor: pointer;
		            line-height: 1.5;
		        }
				thead{
		            td{
		                padding: 18px 10px;
		                overflow: hidden;
		                white-space: nowrap;
		                text-overflow: ellipsis;
		            }
		        }

			}
		}
		.grid-body{
			table{
		        width: 100%;
		        table-layout: fixed;
		        text-align: center;
		        // border-collapse: collapse;
		        position: relative;
		        border: none;
		        td{
		            border-width: 0 1px 0 1px;
		            border-style: solid;
		            cursor: pointer;
		            line-height: 1.5;
		            overflow: hidden;
		            white-space: nowrap;
		            text-overflow: ellipsis;
		        }
		        tbody{
		            tr{
		                border-width: 1px 0 1px 0;
		                border-style: solid;
		                &:first-child{
		                    border-width: 0 0 1px 0;
		                }
		                &.on{
		                    background-color: transparent;
		                }
		                td{
		                    padding: 12px 10px;
		                    &.loading{

		                    }
		                }
		            }
					.link{
						padding: 4px 8px;
					    margin-right: 4px;
					    border-radius: 4px;
					    background: #5bc0de;
						border: 1px solid #fff;
					    color: #fff;
					    font-weight: 300;
						transition: all .3s;
						-webkit-transition: all .3s;
						-o-transition: all .3s;
						-moz-transition: all .3s;
						&:hover{
							background: #fff;
							color: #5bc0de;
							border: 1px solid #5bc0de;
						}
					}
					.link-disabled{
						cursor: no-drop;
						padding: 4px 8px;
					    margin-right: 4px;
					    border-radius: 4px;
					    background: #75a3a7;
						border: 1px solid #fff;
					    color: #fff;
					    font-weight: 300;
						transition: all .3s;
						-webkit-transition: all .3s;
						-o-transition: all .3s;
						-moz-transition: all .3s;
						&:hover{
							background: #fff;
							color: #5bc0de;
							border: 1px solid #5bc0de;
						}
					}
		        }

		    }
			.loading{
				font-size: 14px;
				color: #219f1d
			}
			.noData{
				font-size: 14px;
    			color: #219f1d
			}
		}
		.grid-paging{
			height: 40px;
			margin-top: 20px;
			padding: 4px 10px;
			background: rgba(229, 229, 229, .5);
			.tips{
				display: inline-block;
				padding: 8px 0px;
			}
			.operate{
				float: right;
				right: 0px;
				height: 100%;
				ul{
					display: inline-block;
					li{
						display: inline;
					    line-height: 14px;
						a{
							display: inline-block;
							padding: 8px 13px;
							border: 1px solid #cccccc;
							border-left-width: 0;
							&.active{
								color: #fff;
								background: #5bc0de;
							}
						}
						&:first-child{
							a{
								border-left-width: 1px;
							}
						}
					}
				}
				.prev{
					display: inline-block;
					color: #5bc0de;
					border: 1px solid #5bc0de;
					height: 100%;
					vertical-align: top;
    				padding: 9px 13px;
					line-height: 12px;

					-webkit-border-bottom-left-radius: 4px;
				    -moz-border-radius-bottomleft: 4px;
				    border-bottom-left-radius: 4px;
				    -webkit-border-top-left-radius: 4px;
				    -moz-border-radius-topleft: 4px;
				    border-top-left-radius: 4px;
				    border-left-width: 1px;
				}
				.next{
					display: inline-block;
					color: #5bc0de;
					border: 1px solid #5bc0de;
					height: 100%;
					vertical-align: top;
    				padding: 9px 13px;
					line-height: 12px;

					-webkit-border-bottom-right-radius: 4px;
				    -moz-border-radius-bottomleft: 4px;
				    border-bottom-right-radius: 4px;
				    -webkit-border-top-right-radius: 4px;
				    -moz-border-radius-topleft: 4px;
				    border-top-right-radius: 4px;
				    border-right-width: 1px;
				}
				.disabled{
					background: #d1e3e9;
				}
				.jump{
					display: inline-block;
					margin-left: 10px;
					.jump-input{
						width: 50px;
						border: 1px solid #5bc0de;
						padding: 6px 10px;
					}
					.jump-btn{
						// padding: 8px 6px;
						width: 29px;
						height: 29px;
						display: inline-block;
						vertical-align: top;
						line-height: 29px;
						text-align: center;
						border: 1px solid #5bc0de;
						color: #5bc0de;
						border-radius: 50%;
						transition: all .3s;
						-webkit-transition: all .3s;
						-o-transition: all .3s;
						-moz-transition: all .3s;
						&:hover{
							background: #5bc0de;
							color: #FFF;
						}
					}
				}
			}
		}
		/**
		 *
		 * common css
		 *
		 */
		ul {
 		    margin: 0;
			padding: 0;
 		}
		*{
			box-sizing: border-box;
		}
		a{
			color: #333;
			text-decoration: none;
		}
		a:active,
		a:hover {
		    outline: 0;
		}
		/** Remove most spacing between table cells. **/
		table {
		    border-collapse: collapse;
		    border-spacing: 0;
		}
	}
</style>
