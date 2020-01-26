<template>
	<gracePage :customHeader="false">
		<view slot="gBody" class="grace-body">
			<swiper class="grace-swiper swiper1" autoplay="true" indicator-color="rgba(255, 255, 255, 1)" indicator-active-color="#3688FF"
			 style="height:276rpx;margin-top: 30rpx;" interval="3000">
				<swiper-item>
					<image src='https://askyisheng.oss-cn-hangzhou.aliyuncs.com/sw1.png'></image>
				</swiper-item>
			</swiper>
			<form @submit="formSubmit" class="grace-form" style="margin-top:25px;">
				<view class="grace-form-item grace-border-b">
					<text class="grace-form-label">手机号</text>
					<view class="grace-form-body">
						<input type="text" class="grace-form-input" name="phone" placeholder="请输入手机号"></input>
					</view>
				</view>
				<view class="grace-form-item grace-border-b">
					<text class="grace-form-label">密码</text>
					<view class="grace-form-body">
						<input
						type="password" class="grace-form-input" name="password" placeholder="请输入密码" />
					</view>
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
	var graceChecker = require("../../graceUI/jsTools/graceChecker.js");
	var graceRequest = require("../../graceUI/jsTools/request.js");
	export default {
		data() {
			return {
				openid:"",
				pwdShow : false
			}
		},
		components: {
				gracePage
		},
		onShow() {
			// uni.setStorageSync('doctorid',1)//测试的时候用
			if (!uni.getStorageSync('doctorid')) {
				let appid = "wx9970965c3f3a5ba3"; //为测试号id
				let code = getUrlParam("code"); //是否存在code
				let _this = this;
				let local = window.location.href;
				if (code == null || code === "") {
					//不存在就打开上面的地址进行授权
					window.location.href = `https://open.weixin.qq.com/connect/oauth2/authorize?appid=${appid}&redirect_uri=${encodeURIComponent(local)}&response_type=code&scope=snsapi_userinfo&state=1#wechat_redirect`;
				} else {
					//存在则通过code传向后台调用接口返回微信的个人信息
					graceRequest.get(
						'https://askapp.cloudhos.net/wx/getWXinfo',
						{code:code},
						function(res){
							if(res.code == '0'){
								_this.openid = res.data.openid
							}
						}
					);
				}
			}else{
				uni.redirectTo({
					url: '../person/person'
				});
			}
		},
		methods:{ 
			loginsuccess: function(e) {
				uni.setStorageSync('doctorid',e.id)
				uni.redirectTo({
					url: '../person/person'
				});
			},
			formSubmit : function(e){
				const _this = this
				//定义表单规则
				var rule = [
					{name:"phone", checkType : "phoneno", checkRule:"",  errorMsg:"请填写正确的手机号"}
				];
				//进行表单检查
				var formData = e.detail.value;
				var checkRes = graceChecker.check(formData, rule);
				if(checkRes){
					if(this.openid){
						formData.openid = this.openid
						graceRequest.post(
							'https://askapp.cloudhos.net/wechat/doctorbind',
							formData,
							'form',
							{},
							function(res){
								if(res.code == '0'){
									_this.loginsuccess(res.data)
								}else{
									uni.showToast({
										title: res.msg,
										icon: 'none',
										mask: true
									});
								}
							}
						);
					}else{
						uni.showToast({
						    title: '获取用户信息失败，请刷新重试',
							icon:'none',
							mask:true
						});
					}
				}else{
					uni.showToast({ title: graceChecker.error, mask:true, icon: "none" });
				}
			}
		}
	}
	// 判断公众号截取code
	const getUrlParam = (name) => {
		let reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
		let r = window.location.search.substr(1).match(reg);
		if (r != null) {
			return unescape(r[2]);
		}
		return null;
	}
</script>
<style>
	.swiper1 image {
		width: 690rpx;
		height: 276rpx;
	}

	.swiper1 swiper-item {
		border-radius: 10rpx;
	}
</style>
