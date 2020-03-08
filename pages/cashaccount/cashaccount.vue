<template>
	<gracePage :customHeader="false">
		<view slot="gBody" class="grace-body">
		<form @submit="formSubmit" class="grace-form grace-margin-top">
			<view class="grace-form-item grace-border-b">
				<text class="grace-form-label">银行名称</text>
				<view class="grace-form-body">
					<input type="text" class="grace-form-input" name="blankname" :value="blankname"></input>
				</view>
			</view>
			<view class="grace-form-item grace-border-b">
				<text class="grace-form-label">所属支行</text>
				<view class="grace-form-body">
					<input type="text" class="grace-form-input" name="blankpart" :value="blankpart"></input>
				</view>
			</view>
			<view class="grace-form-item grace-border-b">
				<text class="grace-form-label">卡号</text>
				<view class="grace-form-body">
					<input type="text" class="grace-form-input" name="blankcard" :value="blankcard"></input>
				</view>
			</view>
			<view class="grace-form-item grace-border-b">
				<graceCheckBtn :checked="val" :parameter="[]" @change="checkedChange" :size="46">
					<text class="grace-text">
						<a href="https://askapp.cloudhos.net/page/xy/yhsy.html">我同意并遵守结算规则协议</a>
					</text>
				</graceCheckBtn>
			</view>
			<view style="padding:22rpx 0;">
				<button class="grace-button" style="line-height:80rpx;" formType="submit" type="primary">提交</button>
			</view>
		</form>
		</view>
	</gracePage>
</template>
<script>
import gracePage from "../../graceUI/components/gracePage.vue";
import graceCheckBtn from "../../graceUI/components/graceCheckBtn.vue";
var graceRequest = require("../../graceUI/jsTools/request.js");
export default {
    data() {
        return {
			blankname: '',
			blankpart: '',
			blankcard: '',
			val: false
        }
    },
	onShow() {
		if (!uni.getStorageSync('doctorid')) {
			uni.redirectTo({
				url: '../index/index'
			});
		}else{
			this.getAccount()
		}
	},
    methods:{
		// 表单提交及验证
		formSubmit : function(e){
			const _this = this
			if(!this.val){
				uni.showToast({
					title: '请同意并遵守结算规则协议',
					icon: 'none',
					mask: true
				});
				return false;
			}
			var formData = e.detail.value;
			formData.doctorid = uni.getStorageSync('doctorid');
			graceRequest.post(
				'https://askapp.cloudhos.net/dmoney/saveaccount',
				formData,
				'form',
				{},
				function(res){
					if(res.code == '0'){
						_this.getAccount()
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
		checkedChange(e) {
			this.val = e[0];
		},
		getAccount(){
			const _this = this
			uni.showLoading({});
			graceRequest.get(
				'https://askapp.cloudhos.net/dmoney/dcashaccount',
				{doctorid:uni.getStorageSync('doctorid')},
				function(res){
					if(res.code == '0'){
						_this.blankname = res.data.blankname;
						_this.blankpart = res.data.blankpart;
						_this.blankcard = res.data.blankcard;
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
	components: {
		gracePage,graceCheckBtn
	}
}
</script>
<style>
.grace-border-b{border-color:#F8F8F8;}
</style>