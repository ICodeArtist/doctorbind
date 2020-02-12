<template>
	<gracePage :customHeader="false">
		<view slot="gBody" class="grace-flex-v1" id="gBody">
			<view class="SegmentedControlIn">
				<graceSegmentedControl :items="cates" :current="current" @change="change"></graceSegmentedControl>
			</view>
			<scroll-view scroll-y class="grace-list grace-margin-top" :style="{height:scrollHeight+'px'}">
				<view class="grace-list-items grace-body" 
				v-for="(item, index) in logs" :key="index" >
					<view class="grace-list-body grace-border-b">
						<view class="grace-list-title">
							<text class="grace-list-title-text">{{item[0]}}</text>
							<text class="grace-list-title-desc">￥{{item[1]}}</text>
						</view>
						<view class="grace-list-body-desc">{{item[2]}}</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</gracePage>
</template>
<script>
import gracePage from "../../graceUI/components/gracePage.vue";
import graceSegmentedControl from '../../graceUI/components/graceSegmentedControl.vue';
var graceRequest = require("../../graceUI/jsTools/request.js");
export default {
    data() {
    	return {
			cates : ["未提现", "已提现"],
			current : 0,
			scrollHeight : 500,
			logs : []
		}
    },
	onShow() {
		const _this = this
		if (!uni.getStorageSync('doctorid')) {
			uni.redirectTo({
				url: '../index/index'
			});
		}else{
			
		}
	},
	onReady:function(){
		setTimeout(()=>{
			uni.createSelectorQuery().select('#gBody').fields(
				{size: true}, (res) => {
					this.scrollHeight = res.height - uni.upx2px(180);
				}
			).exec();
		},1000);
	},
	methods:{
		change : function(e){
			this.current = e;
		},
		getlogs(){
			
		}
	},
	components:{gracePage,graceSegmentedControl}
}
</script>
<style>
.SegmentedControlIn{width:500rpx; padding:0 125rpx; margin-top:38rpx;}
</style>