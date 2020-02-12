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
							<text class="grace-list-title-text">{{item.content}}</text>
							<text class="grace-list-title-desc">￥{{item.amount}}</text>
						</view>
						<view class="grace-list-title">
							<text class="grace-list-title-text" style="margin-top: 10px;">{{item.create_time}}</text>
							<text class="grace-list-title-desc" v-if="current == '0'">
								<button type="primary" class="grace-button" @tap="docash(item.id)">提现</button>
							</text>
						</view>
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
			cates : ["未提现","提现中","已提现"],
			current : 0,
			scrollHeight : 500,
			logs : []
		}
    },
	onShow() {
		if (!uni.getStorageSync('doctorid')) {
			uni.redirectTo({
				url: '../index/index'
			});
		}else{
			this.getlogs()
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
		change(e){
			this.current = e
			this.getlogs()
		},
		docash(logid){
			const _this = this
			graceRequest.post(
				'https://askapp.cloudhos.net/dmoney/docash',
				{
					logid : logid
				},
				'form',
				{},
				function(res){
					if(res.code == '0'){
						_this.getlogs()
						uni.showToast({
							title: '成功',
							icon: 'success',
							mask: true
						});
					}else{
						uni.showToast({
							title: res.msg,
							icon: 'none',
							mask: true
						});
					}
				}
			);
		},
		getlogs(){
			const _this = this
			uni.showLoading({});
			const data = {}
			data.doctorid = uni.getStorageSync('doctorid')
			data.type = this.current
			graceRequest.get(
				'https://askapp.cloudhos.net/dmoney/getincomes',
				data,
				function(res){
					if(res.code == '0'){
						_this.logs = res.data;
						uni.hideLoading();
					}else{
						uni.showToast({
						    title: res.msg,
							icon:'none',
							mask:true
						});
					}
				}
			)
		}
	},
	components:{gracePage,graceSegmentedControl}
}
</script>
<style>
.SegmentedControlIn{width:500rpx; padding:0 125rpx; margin-top:38rpx;}
</style>