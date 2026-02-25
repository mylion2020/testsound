<template>
	<view class="content">
		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
        <button type="primary" @click="goToTestTTS" style="margin-top: 20px;">TTS 测试</button>
        <button type="primary" @click="goToTestASR" style="margin-top: 20px;">ASR 测试</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onLoad() {

		},
		methods: {
            goToTestTTS() {
                uni.navigateTo({
                    url: '/pages/test-tts/test-tts'
                });
            },
            goToTestASR() {
                uni.navigateTo({
                    url: '/pages/test-asr/test-asr'
                });
            }
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
