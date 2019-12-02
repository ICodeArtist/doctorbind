<template>
	<view class="grace-margin">
		<view class="grace-title grace-margin-top"></view>
		<!-- swiper 的高度 = (外层宽度 690rpx ) * (图片的 宽高比例 图片高度/ 图片宽度) -->
		<!-- 本例 图片宽度 : 500px 高度 : 200px -->
		<!-- 计算得出 swiper 的高度 = 690 * 200 / 500 = 276rpx-->
		<swiper class="grace-swiper swiper1" autoplay="true" indicator-color="rgba(255, 255, 255, 1)" indicator-active-color="#3688FF"
		 style="height:276rpx" interval="3000">
			<swiper-item>
				<image src='https://askyisheng.oss-cn-hangzhou.aliyuncs.com/sw1.png'></image>
			</swiper-item>
		</swiper>
		<form @submit="formSubmit" class="grace-form" style="margin-top:25px;">
		    <view class="grace-items grace-noborder">
				<view class="grace-label">手机号</view>
				<input type="text" class="input" name="phone" placeholder="请输入手机号" />
			</view>
			<view class="grace-items">
				<view class="grace-label">密码</view>
					<input 
					:type="pwdShow ? 'text' : 'password'" class="input" name="password" placeholder="请输入密码" 
					:disabled="pwdShow ? true : false"
					/>
				<view class="grace-form-funs grace-icons icon-eye" :style="{color:pwdShow?'#3688FF':''}" @tap="pwdShow = !pwdShow"></view>
			</view>
			<view style="padding:22rpx 0;">
				<button formType="submit" type="primary" style="width:100%;">提交</button>
			</view>
		</form>
	</view>
</template>
<script>
	var graceChecker = require("../../graceUI/jsTools/graceChecker.js");
	var graceRequest = require("../../graceUI/jsTools/request.js");
	export default {
		data() {
			return {
				openid:"",
				pwdShow : false
			}
		},
		onShow() {
			let appid = "wx236bc250708f2a2f"; //为测试号id
			let code = getUrlParam("code"); //是否存在code
			let _this = this;
			console.log(code);
			let local = window.location.href;
			if (code == null || code === "") {
				//不存在就打开上面的地址进行授权
				window.location.href = `https://open.weixin.qq.com/connect/oauth2/authorize?appid=${appid}&redirect_uri=${encodeURIComponent(local)}&response_type=code&scope=snsapi_userinfo&state=1#wechat_redirect`;
			} else {
				//存在则通过code传向后台调用接口返回微信的个人信息
				graceRequest.get(
					'https://askapp.wxori.top/wx/getWXinfo',
					{code:code},
					function(res){
						if(res.code == '0'){
							_this.openid = res.data.openid
						}
					}
				);
			}
		},
		methods:{ 
			formSubmit : function(e){
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
						console.log(formData);
						graceRequest.post(
							'https://askapp.wxori.top/wechat/doctorbind',
							formData,
							'form',
							{},
							function(res){
								uni.showToast({
								    title: res.msg,
									icon:'none',
									mask:true
								});
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
