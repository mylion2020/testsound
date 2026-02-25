# android-utils

#### 插件测试使用方法
1. 选择试用，绑定要试用的项目appid，

2. 选择后下载到对应的本地项目，

3. 按照文档 -》把插件引入项目（即 	import {showToast,showToastButton,androidDialog,showColorToast,screenShotEnableState,AndroidTTSVoice} from "@/uni_modules/android-utils"
 需要先引入），

4. 发布-》云打包-》选择制作基座-》打包等基座制作完成 

5. 运行 -》 运行到手机或模拟器-》运行到Androidapp基座-》选择使用自定义基座运行-》选择手机-》运行

6. 若之前手机安装过基座需要先卸载之前的基座




### 语音对象
#### AndroidTTSVoice（Android ios 鸿蒙）
### uniapp
~~~

import {showToast,showToastButton,androidDialog,showColorToast,screenShotEnableState,AndroidTTSVoice} from "@/uni_modules/android-utils"

tts=new AndroidTTSVoice(function(state){
	console.log(state)
	if(state){
		
	}
});
// 需要初始化完成
tts.listenerVoiceState(function(b){
	// 0 开始  1 完成  -1 错误
			console.log(b)
})
tts.speak("语音测试");
~~~


### uniappx
~~~

import {showToast,showToastButton,androidDialog,showColorToast,screenShotEnableState,AndroidTTSVoice} from "@/uni_modules/android-utils"

tts=new AndroidTTSVoice(function(state:boolean){
	if(state){
		
	}
});

tts.listenerVoiceState(function(b:number){
	// 0 开始  1 完成  -1 错误
})

// 需要初始化完成
tts.speak("语音测试")
~~~


### 仅安卓
```
	import {showToast,showToastButton,androidDialog,showColorToast,screenShotEnableState,AndroidTTSVoice} from "@/uni_modules/android-utils"
	 
	
	showToast("test");
	
	showToastButton("test");
	
	androidDialog("标题","消息","确定",function(){
			showToast("单击确定")
	},"取消",function(){
		showToast("单击取消")
		return true;
	})	
	
	showColorToast("这是一个安卓原生吐司","#ff0000")
	
	
	screenShotEnableState(true);// 禁用截屏
	screenShotEnableState(false);// 启用截屏
	
	
```

## 对象方法
### AndroidTTSVoice 构造方法 
参数1 function  方法  function 参数1 boolean 

#### 播放
#### speak
参数1 string 播放内容

#### 设置模式(仅安卓)
#### setMode
参数1 number  0 暂停后输出， 1  播放结束后播放





#### 获取可用语音名称(仅安卓)
#### getVoiceNames
retrn string[]

#### 设置语音名称(仅安卓)
#### setVoiceName
参数1 string 语音名称


#### 设置语速
#### setSpeed
参数1 number 0-1



#### 停止
#### stop



#### 是否正在播放(仅安卓)
#### isSpeaking
return  boolean 是否正在播放




### 监听播放状态 (仅安卓)
#### listenerVoiceState
参数1 function 参数1 number  0 开始  1 完成  -1 错误

### 打赏
感谢您使用此插件，如果你觉得本插件，解决了你的问题，赠人玫瑰，手留余香。




### 开发文档
[UTS 语法](https://uniapp.dcloud.net.cn/tutorial/syntax-uts.html)
[UTS API插件](https://uniapp.dcloud.net.cn/plugin/uts-plugin.html)
[UTS 组件插件](https://uniapp.dcloud.net.cn/plugin/uts-component.html)
[Hello UTS](https://gitcode.net/dcloud/hello-uts)