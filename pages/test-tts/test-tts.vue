<template>
	<view class="content">
		<view class="input-area">
			<textarea v-model="text" placeholder="请输入要朗读的文本" style="width: 100%; height: 100px; border: 1px solid #ccc; padding: 10px; box-sizing: border-box;" />
		</view>
		
		<view class="control-area">
			<view class="slider-item">
				<text>语速: {{ speed }}</text>
				<slider :value="speed" @change="onSpeedChange" min="0" max="1" step="0.1" show-value />
			</view>
			
			<view class="btn-group">
				<button type="primary" @click="speak">播放</button>
				<button type="warn" @click="stop">停止</button>
			</view>
            <view class="btn-group" style="margin-top: 10px;">
                <button size="mini" @click="checkIsSpeaking">检查是否正在播放</button>
            </view>
		</view>
		
		<view class="log-area">
			<view class="log-title">日志:</view>
			<scroll-view scroll-y="true" style="height: 200px; border: 1px solid #eee; padding: 5px;">
				<view v-for="(log, index) in logs" :key="index" class="log-item">
					{{ log }}
				</view>
			</scroll-view>
            <button size="mini" @click="clearLogs" style="margin-top: 5px;">清空日志</button>
		</view>
	</view>
</template>

<script>
	// 引入插件
	import { AndroidTTSVoice } from "@/uni_modules/android-utils"

	export default {
		data() {
			return {
				text: '语音测试，欢迎使用 Android TTS 插件',
				speed: 0.5,
				logs: [],
				tts: null,
				isInit: false
			}
		},
		onLoad() {
			this.initTTS();
		},
		methods: {
			addLog(msg) {
				const time = new Date().toLocaleTimeString();
				this.logs.unshift(`[${time}] ${msg}`);
			},
            clearLogs() {
                this.logs = [];
            },
			initTTS() {
				this.addLog("正在初始化 TTS...");
				try {
					// 初始化 TTS
                    const ttsInstance = new AndroidTTSVoice((state) => {
						console.log("TTS init state:", state);
						if (state) {
							this.isInit = true;
							this.addLog("TTS 初始化成功");
						} else {
							this.addLog("TTS 初始化失败");
						}
					});
                    this.tts = ttsInstance;
                    
                    // 设置监听器
                    this.tts.listenerVoiceState((code) => {
                        // 0 开始  1 完成  -1 错误
                        let statusText = "";
                        if (code === 0) statusText = "开始播放";
                        else if (code === 1) statusText = "播放完成";
                        else if (code === -1) statusText = "播放错误";
                        else statusText = "未知状态: " + code;
                        
                        console.log("Voice State:", code);
                        this.addLog(`播放状态: ${statusText} (${code})`);
                    });
				} catch (e) {
					console.error(e);
					this.addLog("TTS 初始化异常: " + e.message);
				}
			},
			speak() {
				if (!this.isInit || !this.tts) {
					this.addLog("TTS 未初始化或初始化失败");
                    // 尝试重新初始化
                    this.initTTS();
					return;
				}
				if (!this.text) {
					this.addLog("请输入文本");
					return;
				}
				this.addLog("开始请求播放: " + this.text);
				this.tts.speak(this.text);
			},
			stop() {
				if (this.tts) {
					this.tts.stop();
					this.addLog("停止播放");
				}
			},
			onSpeedChange(e) {
				this.speed = e.detail.value;
				if (this.tts) {
					this.tts.setSpeed(this.speed);
					this.addLog("设置语速: " + this.speed);
				}
			},
            checkIsSpeaking() {
                if (this.tts) {
                    const speaking = this.tts.isSpeaking();
                    this.addLog("正在播放: " + speaking);
                }
            }
		}
	}
</script>

<style>
	.content {
		padding: 20px;
	}
	.input-area {
		margin-bottom: 20px;
	}
	.control-area {
		margin-bottom: 20px;
	}
	.slider-item {
		margin-bottom: 15px;
	}
	.btn-group {
		display: flex;
		justify-content: space-between;
	}
	.log-area {
		margin-top: 20px;
	}
	.log-title {
		font-weight: bold;
		margin-bottom: 5px;
	}
    .log-item {
        font-size: 12px;
        margin-bottom: 2px;
        color: #333;
    }
</style>
