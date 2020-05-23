<template>
	<view class="picker-select">
		<view class="picker-select__input" @click="showPopup">
			<view class="val">
				{{text || placeholder}}
			</view>
			<view class="icon">
				&gt;
			</view>
		</view>
		
		<z-popup :visible="show" @change="close">
			<view class="picker-select__header">
				<view class="cancel" @click="cancel">
					取消
				</view>
				<view class="confirm" @click="confirm">
					确认
				</view>
			</view>
			<picker-view
				class="picker-select__popup"
				:indicator-style="indicatorStyle"
				:value="valIdxs" 
				@change="bindChange">
				<picker-view-column v-for="(col, index) in columns" :key="index">
					<view 
						class="range-label" 
						v-for="(item, subIndex) in columnList[index]" 
						:key="subIndex">{{item[rangeKey] || ''}}</view>
				</picker-view-column>
		</picker-view>
		</z-popup>
	</view>
</template>

<script>
	import ZPopup from './Popup.vue'
	
	export default{
		name: 'picker-select',
		components: {
			ZPopup
		},
		props: {
			text: {
				type: String,
				default: ''
			},
			placeholder: {
				type: String,
				default: '--'
			},
			range: {
				type: Array,
				default: []
			},
			columns: {
				type: Number,
				default: 1
			},
			values: {
				type: Array,
				default: () => []
			},
			rangeKey: {
				type: String,
				default: 'label'
			},
			rangeValue: {
				type: String,
				default: 'value'
			},
		},
		data() {
			return {
				show: false,
				// picker-view-column对应的列表
				columnList: [],
				// values转换为对应picker-view-column中的索引数组
				valIdxs: [],
				originVals: [],
				indicatorStyle: `height: ${Math.round(uni.getSystemInfoSync().screenWidth/(750/100))}px;`
			}
		},
		computed: {
			
		},
		created() {
			console.log('created')
			
			this.initInfo()
		},
		methods: {
			showPopup() {
				this.initInfo()
				// 整理 picker-view 对应的数据
				console.log(this.columnList,this.valIdxs)
				this.show = true
			},
			
			close() {
				this.show = false
			},
			cancel() {
				this.hide()
			},
			confirm() {
				console.log(this._updateValByIdx())
				this.hide()
			},
			
			hide() {
				this.show = false
			},
			
			initInfo() {
				this.valIdxs = new Array(this.columns)
				this.columnList = new Array(this.columns)
				// 1. 若 values 为非数组或 values 的长度与 columns 不一致，默认全部为第一项
				if (!Array.isArray(this.values) || this.values.length !== this.columns) {
					this.valIdxs.fill(0)				
					this._updateColumnByIdx(0)
					return
				}
				
				// 2. 若 values 正常,则需要把 values 中的 value 值转为对应的 picker-view 内的 picker-view-column 的第几项（下标从 0 开始）
				this._transformValToIdx()
				
				console.log(this.columnList,this.valIdxs)
			},
			
			// picker-view 滚动时触发
			bindChange(e) {
				// 获取当前 picker-view 的索引列表
				const currentValIdxs = e.detail.value
				
				let preColumn = this.range
				for(let i = 0; i < this.columns; i++) {
					// 与 valIdxs 逐个一一比较，判断是哪一个 picker-view-column 的索引发生了变化
					// 同时更新 columnList
					let currentIdx = currentValIdxs[i]
					let originIdx = this.valIdxs[i]
					
					if (currentIdx !== originIdx) {
						// 更新 valIdx 以及当前 picker-view-column 后面的索引全部置为 0 
						this.valIdxs.splice(i, 1, currentIdx)
						if (i < this.columns) {
							this.valIdxs.fill(0, i + 1)
						}
						this._updateColumnByIdx(i)
						break
					}
				}
			},
			
			// 通过 values 转换为索引
			_transformValToIdx() {
				let preColumn = this.range
				for(let i = 0; i < this.columns; i++) {
					// 2.1 获取当前 values 中的 value 对应的 range 的第几项
					const val = this.values[i]
					let _idx = preColumn.findIndex(item => item[this.rangeValue] === val)
					
					this.columnList.splice(i, 1, preColumn)
					// 2.2 若未找到,则默认取第一项
					this.valIdxs.splice(i, 1, _idx > -1 ? _idx : 0)
					
					preColumn = preColumn[_idx].children || []
				}
			},
			
			// 通过 picker-view-column 的索引更新对应的列表 columnList[i]
			_updateColumnByIdx(index) {
				let preColumn = []
				if (index === 0) {
					preColumn = this.range
				} else {
					let _valIdx = this.valIdxs[index - 1]
					preColumn = this.columnList[index - 1][_valIdx].children || []
				}
				
				for(let i = index; i < this.columns; i++) {
					let _idx = this.valIdxs[i]
					// 更新当前 picker-view-column 的索引更新对应的列表
					this.columnList.splice(i, 1, preColumn)
					preColumn = preColumn[_idx].children || []
				}
			},
			
			// 通过 picker-view-column 的索引更新对应的val
			_updateValByIdx() {
				let result = []
				let values = []
				let labels = []
				
				for(let i = 0; i < this.columns; i++) {
					const currentCol = this.columnList[i]
					const currentObj = currentCol[this.valIdxs[i]]
					const key = currentObj[this.rangeKey]
					const value = currentObj[this.rangeValue]
					
					values.push(value)
					labels.push(key)
					
					result.push({
						[this.rangeKey]: key,
						[this.rangeValue]: value
					})
				}
				
				return {
					result,
					values,
					labels
				}
			}
		}
	}
</script>

<style lang="scss" scope>
	.picker-select{
		&__input{
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding: 6rpx 12rpx;
		}
		
		&__header{
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding: 6rpx 12rpx;
			.cancel{
				padding: 6rpx 12rpx;
			}
			.confirm{
				padding: 6rpx 12rpx;
				color: skyblue;
			}
		}
		&__popup{
			width: 100%;
			height: 400rpx;
			.range-label{
				display: flex;
				align-items: center;
				justify-content: center;
			}
		}
	}
</style>
