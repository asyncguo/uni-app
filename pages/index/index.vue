<template>
	<view class="container">
		<!-- <z-popup></z-popup> -->
		<picker-select
			text="北京/北京/海淀区"
			:range="range3"
			:columns="3"
			:values='values3'></picker-select>
		<picker-select
			text="北京"
			:range="range3"
			:columns="1"
			:values='range3'></picker-select>
	</view>
</template>

<script>
	import PickerSelect from './components/PickerSelect.vue'
	
	export default {
		components: {
			PickerSelect
		},
		data() {
			return {
				values3: ['hb', 'hd', 'wx'],
				range3: [
					{
						label: '北京',
						value: 'bj',
						children: [
							{
								label: '北京',
								value: 'bj',
								children: [
									{ label: '海淀', value: 'hd' },
									{ label: '朝阳', value: 'cy' },
								]
							}
						]
					},
					{
						label: '河北',
						value: 'hb',
						children: [
							{
								label: '石家庄',
								value: 'sjz',
								children: [
									{ label: '新华区', value: 'xhq' },
									{ label: '桥西区', value: 'qxq' },
								]
							},
							{
								label: '邯郸',
								value: 'hd',
								children: [
									{ label: '魏县', value: 'wx' },
									{ label: '邯山区', value: 'hsq' },
								]
							}
						]
					},
					{
						label: '浙江',
						value: 'zj',
						children: [
							{
								label: '杭州',
								value: 'hz',
								children: [
									{ label: '西湖区', value: 'xhq' },
									{ label: '拱墅区', value: 'gsq' },
								]
							},
							{
								label: '宁波',
								value: 'nb',
								children: [
									{ label: '余姚市', value: 'yys' },
									{ label: '江北区', value: 'jbq' },
								]
							}
						]
					}
				]
			}
		},
		methods: {

		}
	}
</script>

<style>
	.container {
		width: 100%;
		height: 1000px;
		line-height: 24px;
	}
</style>
