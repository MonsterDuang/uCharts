<template>
	<view class="china uni-bt" :style="{height: height + 'px'}">
		<view class="tips" style="top: 0" v-if="tips.length&&tips[0]">
			<view>{{tips[0]}}</view>
		</view>
		<map class="map" :style="{height: height + 'px'}" scale="3" :longitude="center[0]" :latitude="center[1]"
			:markers="markers" show-location="true"></map>
		<view class="tips" style="bottom: 0" v-if="tips.length&&tips[1]">
			<view>{{tips[1]}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				tips: [],
				height: "",
				markers: [],
				center: [105, 30],
			}
		},
		onLoad() {
			this.height = this.windowHeight;
			// this.getUserLocation();
			this.getMapData();
		},
		methods: {
			getUserLocation() {
				uni.getLocation({
					type: 'wgs84',
					success: res => {
						this.center = [res.longitude, res.latitude];
					}
				});
			},
			getMapData() {
				this.$ajax({
						url: '/app/footmark/record',
						data: {}
					},
					({
						data
					}) => {
						this.tips = [data.publicCureInfo, data.willCureInfo];
					},
				);
			},
		}
	}
</script>

<style lang="scss">
	.china {
		display: flex;
		flex-direction: column;
		justify-content: center;
		background-color: #f5f5ff;
		position: relative;
		box-sizing: border-box;
	}

	.map {
		width: 100%;
	}

	.tips {
		padding: 30upx;
		position: absolute;
		z-index: 9;

		view {
			padding: 20upx 30upx;
			background-color: #c3c3c3;
			font-size: 28upx;
			text-indent: 2em;
			line-height: 48upx;
		}
	}
</style>
