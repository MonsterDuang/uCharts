<template>
	<view class="china uni-bt" :style="{height: height + 'px'}">
		<view class="tips" style="top: 0" v-if="tips.length&&tips[0]">
			<view>{{tips[0]}}</view>
		</view>
		<view class="charts-box" :style="{height: height + 'px'}">
			<qiun-data-charts type="map" :canvas2d="true" canvasId="mapma" :chartData="chartsDataMap"
				tooltipFormat="tooltipFun" @getIndex="getIndex" />
		</view>
		<view class="tips" style="bottom: 0" v-if="tips.length&&tips[1]">
			<view>{{tips[1]}}</view>
		</view>
	</view>
</template>

<script>
	import mapdata from "@/utils/china.js"
	import uCharts from '@/uni_modules/ucharts/js_sdk/u-charts/config-ucharts.js'
	export default {
		data() {
			return {
				chartsDataMap: {
					series: []
				},
				tips: [],
				height: ""
			}
		},
		onLoad() {
			this.height = this.windowHeight;
			uCharts.map = {
				type: "map",
				canvasId: "mapma",
				background: "none",
				animation: false,
				color: ['#fcdfeb', '#fce2ce', '#d8ead4', '#d7e1ee'],
				padding: [0, 0, 0, 0],
				fontSize: 10,
				fontColor: "#333",
				dataLabel: true,
				extra: {
					map: {
						border: true,
						mercator: true,
						borderWidth: 1,
						borderColor: "#fff",
						fillOpacity: 1,
						activeBorderColor: "#fff",
						activeFillColor: "#FF0033",
						activeFillOpacity: 1
					},
					tooltip: {
						showBox: true,
						bgOpacity: 0.8,
						borderRadius: 5,
						borderWidth: 1,
						borderOpacity: 0.8,
						labelBgOpacity: 1,
						borderColor: '#fff',
					}
				}
			}
			uCharts.formatter.tooltipFun = (item, category, index, opts) => {
				return item.count ? `${item.name}地区患者数量：${item.count}` : `${item.name}地区暂无患者`;
			}
			this.getMapData();
		},
		onPullDownRefresh() {
			this.getMapData();
		},
		methods: {
			getMapData() {
				this.$ajax({
						url: '/app/footmark/record',
						data: {}
					},
					({
						data
					}) => {
						this.tips = [data.publicCureInfo, data.willCureInfo];
						mapdata.features.map(item => {
							data.provinceCount.map(x => {
								if (x.proviceId == item.properties.adcode) item.count = x.cnt;
								else item.count = 0;
							})
						})
						this.chartsDataMap.series = mapdata.features;
						uni.stopPullDownRefresh();
					},
					e => {
						this.chartsDataMap.series = mapdata.features;
						uni.stopPullDownRefresh();
					}
				);
			},
			getIndex(e) {
				if (e.currentIndex == -1) return;
				const {
					properties: {
						name,
						adcode
					}
				} = mapdata.features[e.currentIndex];
				// console.log(name, adcode, mapdata.features[e.currentIndex])
			}
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
	}

	.charts-box {
		width: 100%;
		height: 668upx;
	}

	.tips {
		padding: 30upx;
		position: absolute;
		view {
			padding: 20upx 30upx;
			background-color: #eee;
			font-size: 28upx;
			text-indent: 2em;
			line-height: 48upx;
		}
	}
</style>
