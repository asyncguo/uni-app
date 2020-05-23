<template>
	<view class="popup" :class="visible && 'show'">
		<view class="popup-mask" @tap="hidePopup"></view>
		<view class="popup-content">
			<slot/>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'ZPopup',
		props: {
			visible: {
				type: Boolean,
				default: false
			},
			maskClick: {
				type: Boolean,
				default: true
			}
		},
		data() {
			 return {
				
			 }
		},
		methods: {
			hidePopup() {
				if(!this.maskClick) return
				
				this.$emit('change', {
					show: false
				})
			}
		}
	}
</script>

<style lang="scss" scope>
	.popup{
		visibility: hidden;
		position: fixed;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		transition-duration: .3s;
		&.show{
			visibility: visible;
			.popup-mask{
				opacity: 1;
			}
			.popup-content{
				bottom: 0;
			}
		}
		&-mask{
			position: absolute;
			top: 0;
			bottom: 0;
			left: 0;
			right: 0;
			background-color: rgba(0, 0, 0, 0.4);
			opacity: 0;
			transition: opacity .3s ease-in;
		}
		&-content{
			position: absolute;
			bottom: -500px;
			left: 0;
			right: 0;
			background-color: #fff;
			transition: bottom .3s ease-in;
		}
	}
</style>
