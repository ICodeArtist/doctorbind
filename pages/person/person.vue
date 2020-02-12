<template>
	<gracePage :customHeader="false">
		<view slot="gBody" class="grace-body">
			<view class="grace-list">
				<view class="grace-list-items">
					<view class="grace-list-body">
						<view class="grace-list-title">
							<text class="grace-list-title-text">{{name}}</text>
						</view>
						<view class="grace-list-body-desc">{{dtitle}}</view>
					</view>
				</view>
			</view>
			<view class="ucenter-line"></view>
			<view class="grace-list grace-margin-top">
				<navigator url="../cashaccount/cashaccount" class="grace-list-items">
					<text class="grace-list-icon grace-icons icon-menu grace-blue"></text>
					<view class="grace-list-body grace-border-b">
						<view class="grace-list-title">
							<text class="grace-list-title-text">提现账号</text>
						</view>
					</view>
					<text class="grace-list-arrow-right grace-icons icon-arrow-right"></text>
				</navigator>
				<navigator url="../income/income" class="grace-list-items">
					<text class="grace-list-icon grace-icons icon-wallet grace-yellow"></text>
					<view class="grace-list-body grace-border-b">
						<view class="grace-list-title">
							<text class="grace-list-title-text">我的收入</text>
						</view>
					</view>
					<text class="grace-list-arrow-right grace-icons icon-arrow-right"></text>
				</navigator>
				<navigator url="../answer/answer?type=1" class="grace-list-items">
					<text class="grace-list-icon grace-icons icon-kf3 grace-red"></text>
					<view class="grace-list-body grace-border-b">
						<view class="grace-list-title">
							<text class="grace-list-title-text">客服</text>
						</view>
					</view>
					<text class="grace-list-arrow-right grace-icons icon-arrow-right"></text>
				</navigator>
			</view>
		</view>
	</gracePage>
</template>
<script>
import gracePage from "../../graceUI/components/gracePage.vue";
var graceRequest = require("../../graceUI/jsTools/request.js");
export default {
	data() {
		return {
			name:'',
			dtitle:''
		}
	},
	onShow() {
		const _this = this
		if (!uni.getStorageSync('doctorid')) {
			uni.redirectTo({
				url: '../index/index'
			});
		}else{
			uni.showLoading({});
			graceRequest.get(
				'https://askapp.cloudhos.net/back/doctordetail',
				{doctorid:uni.getStorageSync('doctorid')},
				function(res){
					if(res.code == '0'){
						_this.name = res.data.name;
						_this.dtitle = res.data.dtitle;
						uni.hideLoading();
					}else{
						uni.showToast({
						    title: res.msg,
							icon:'none',
							mask:true
						});
						uni.removeStorageSync('doctorid');
						uni.redirectTo({
							url: '../index/index'
						});
					}
				}
			)
		}
	},
	methods:{},
	components:{
		gracePage
	}
}
</script>
<style>
.ucenter-face{width:100rpx !important; height:100rpx !important;}
.ucenter-face-image{width:100rpx !important; height:100rpx !important;}
.ucenter-line{height:12rpx; background-color:#F4F5F6; margin:16rpx 0;}
</style>