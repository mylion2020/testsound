<template>
	<view class="content">
		<view class="status-area">
			<text class="status-text">状态: {{ status }}</text>
		</view>
        
        <view class="result-area">
            <view class="result-title">识别结果:</view>
            <scroll-view scroll-y="true" class="result-box">
                <text class="result-text">{{ resultText }}</text>
                <text class="temp-text">{{ tempText }}</text>
            </scroll-view>
        </view>
		
		<view class="control-area">
            <button size="mini" @click="requestPermission" style="margin-bottom: 10px;">获取录音权限</button>
            <button size="mini" @click="initASR" style="margin-bottom: 10px; margin-left: 10px;" :disabled="isInit">初始化引擎</button>
            <view style="height: 10px;"></view>
			<button type="primary" @click="startListening" :disabled="!isInit || isListening">开始识别</button>
			<button type="warn" @click="stopListening" :disabled="!isListening">停止识别</button>
            <button @click="cancelListening" :disabled="!isListening" style="margin-top: 10px;">取消</button>
		</view>
		
		<view class="log-area">
			<view class="log-title">日志:</view>
			<scroll-view scroll-y="true" style="height: 150px; border: 1px solid #eee; padding: 5px;">
				<view v-for="(log, index) in logs" :key="index" class="log-item">
					{{ log }}
				</view>
			</scroll-view>
            <button size="mini" @click="clearLogs" style="margin-top: 5px;">清空日志</button>
		</view>
	</view>
</template>

<script>
	import { AndroidASR } from "@/uni_modules/android-asr"

	export default {
		data() {
			return {
				status: '未初始化',
                resultText: '',
                tempText: '',
				logs: [],
				asr: null,
				isListening: false,
                isInit: false
			}
		},
		onLoad() {
            this.requestPermission();
		},
        onUnload() {
            if (this.asr) {
                this.asr.destroy();
            }
        },
		methods: {
			addLog(msg) {
				const time = new Date().toLocaleTimeString();
				this.logs.unshift(`[${time}] ${msg}`);
			},
            clearLogs() {
                this.logs = [];
            },
            requestPermission() {
                // 请求录音权限
                plus.android.requestPermissions(['android.permission.RECORD_AUDIO'], (e) => {
                    if (e.deniedAlways.length > 0) {
                        this.addLog('权限被永久拒绝');
                    }
                    if (e.deniedPresent.length > 0) {
                        this.addLog('权限被拒绝');
                    }
                    if (e.granted.length > 0) {
                        this.addLog('权限已获取');
                        this.initASR();
                    }
                }, (e) => {
                    this.addLog('权限请求失败: ' + JSON.stringify(e));
                });
            },
			initASR() {
				this.addLog("正在初始化 ASR...");
				try {
					this.asr = new AndroidASR();
                    this.asr.setListener((res) => {
                        // 兼容处理：检查 res 是否已经是对象
                        let type, data;
                        if (typeof res === 'string') {
                            try {
                                const obj = JSON.parse(res);
                                type = obj.type;
                                data = obj.data;
                            } catch (e) {
                                console.error("解析回调数据失败", e);
                                return;
                            }
                        } else {
                            type = res.type;
                            // 如果后端标记了 data 是字符串，需要再次解析
                            if (res.isDataString && typeof res.data === 'string') {
                                try {
                                    data = JSON.parse(res.data);
                                } catch(e) {
                                    data = res.data;
                                }
                            } else {
                                data = res.data;
                            }
                        }

                        console.log("ASR Callback:", type, data);
                        
                        switch (type) {
                            case 'init':
                                if (data.success) {
                                    this.isInit = true;
                                    this.status = "初始化成功";
                                    this.addLog("初始化成功");
                                } else {
                                    this.status = "初始化失败";
                                    this.addLog("初始化失败: " + data.msg);
                                }
                                break;
                            case 'start':
                                this.status = "正在监听...";
                                this.isListening = true;
                                this.resultText = "";
                                this.tempText = "";
                                this.addLog("开始监听");
                                break;
                            case 'ready':
                                this.status = "准备就绪，请说话";
                                this.addLog("准备就绪");
                                break;
                            case 'begin':
                                this.status = "检测到语音";
                                this.addLog("检测到语音");
                                break;
                            case 'end':
                                this.status = "语音结束";
                                this.isListening = false;
                                this.addLog("语音结束");
                                break;
                            case 'result':
                                if (data.isFinal) {
                                    this.resultText = data.text;
                                    this.tempText = "";
                                    this.addLog("识别结果: " + data.text);
                                } else {
                                    this.tempText = data.text;
                                }
                                break;
                            case 'error':
                                this.status = "错误: " + data.msg;
                                this.isListening = false;
                                this.addLog("错误: " + data.code + " - " + data.msg);
                                break;
                            case 'volume':
                                // console.log("Volume:", data.volume);
                                break;
                        }
					});
				} catch (e) {
					console.error(e);
					this.addLog("ASR 初始化异常: " + e.message);
				}
			},
			startListening() {
				if (!this.isInit || !this.asr) {
					this.addLog("ASR 未初始化");
                    this.initASR();
					return;
				}
				this.asr.startListening();
			},
			stopListening() {
				if (this.asr) {
					this.asr.stopListening();
				}
			},
            cancelListening() {
                if (this.asr) {
                    this.asr.cancel();
                }
            }
		}
	}
</script>

<style>
	.content {
		padding: 20px;
	}
    .status-area {
        margin-bottom: 20px;
        padding: 10px;
        background-color: #f0f0f0;
        border-radius: 5px;
        text-align: center;
    }
    .status-text {
        font-size: 16px;
        font-weight: bold;
    }
    .result-area {
        margin-bottom: 20px;
        border: 1px solid #ddd;
        border-radius: 5px;
        padding: 10px;
        min-height: 100px;
    }
    .result-title {
        font-size: 14px;
        color: #666;
        margin-bottom: 5px;
    }
    .result-text {
        font-size: 18px;
        color: #333;
        display: block;
    }
    .temp-text {
        font-size: 16px;
        color: #999;
        display: block;
    }
	.control-area {
		margin-bottom: 20px;
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
