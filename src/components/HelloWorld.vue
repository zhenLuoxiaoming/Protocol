<template>
	<div class="page_container layout_v" :style="{overflow:'hidden', height : screenH}">
		<div class="layout_h" style="width: 100%;justify-content: space-around; margin-top: 0.6rem;">
			<van-button  :class="show == 0 ? 'bt_select' : 'bt_unselect'" style="width: 48%;"  type="primary" @click="show=0">原生发给h5数据</van-button>
			<van-button  :class="show == 1 ? 'bt_select' : 'bt_unselect'" style="width: 48%;"  type="primary" @click="show=1">h5发给原生数据</van-button>
		</div>
		
		<div  v-if="show == 0"  class="layout_h" style="border: 1px solid black;width: 100%; height: 4rem; overflow: scroll;">
			 <textarea v-if="bs64s.length == 0" style="width: 100%;" readonly>{{note}}</textarea> 
			 <div v-else>
				 <span v-if="ocrInfo.length > 0">
					 ocrInfo: {{ocrInfo}}
				 </span>
				 <img v-for="i in bs64s" :src="i"/>
			 </div>
		</div>
		
		<div v-else class="layout_h" style="border: 1px solid black;width: 100%; height: 4rem; overflow: scroll;"> 
			<textarea style="width: 100%;" readonly>{{sendNote}}</textarea>
		</div>
		
		<div>测试桥接</div>
		<div>侨接状态:{{msg}}</div>
		
		<div class="bottom_container layout_v" style="padding: 0.10rem; flex-grow: 1; overflow: scroll;">
			<div class="layout_h" style="margin-top: 12px; flex-wrap: wrap; justify-content: space-between;">
		      <!-- <div v-for="itme in appMethods" class="itemButton layout_h_c" style="width: 1.1rem; height: 0.44rem; background-color: #07c160;" @click="itme.callMethod">
				  <div style="align-self: center;word-wrap: break-word; width: 100%; text-align: center; color: #fff; font-size: 14px;">
					  {{itme.title}}
				  </div>
			  </div> -->
			  <van-button v-for="itme in appMethods" style="width: 30%; word-wrap: break-word; border-radius: 0.08rem;" class="itemButton" type="primary" @click="itme.callMethod">{{itme.title}}</van-button>
			</div>
		</div>
	</div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
  	return {
		msg : '出错',
		note : '',
		bs64s : [],
		ocrInfo : '',
		show : 0,
		sendNote : '',
		screenH : '',
		h : '',
		appMethods : [
			/* app 回调h5 消息的方法
			 {
				 responseId, // 响应id
				 responseData：{ // app 响应 h5的data , 以下方法中 app 回调 h5方法基本结构都如此
					 uid: 请求唯一表示，h5发过来的消息会带
					 exception:app反给h5异常信息
					 success ： 是否成功
					 data ： { //响应数据内容
						 .... 响应的内容
					 }
				 }
			 }
			*/
			{
				/* 
				 app端收到数据格式 {method : "selectAContact", uid : '4Fb2pCNvXOLZvwxNdgYPSg=='}
				 h5接受数据格式 {{
							  "responseId" : "UL_2_1686016212903",
							  "responseData" : {
								"uid" : "4Fb2pCNvXOLZvwxNdgYPSg==",
								"exception" : "",
								"success" : true,
								"data" : {
								  "phoneNo" : "187-0822-5252",
								  "name" : "曹馨丹"
								}
							  }
							}
				ps 该方法主要接受一个联系人，观察data内的字段信息"data" : {"phoneNo" : "187-0822-5252","name" : "曹馨丹"}
				 */
				title : 'selectAContact',
				callMethod : ()=>this.normalCall({method : 'selectAContact', uid : '4Fb2pCNvXOLZvwxNdgYPSg=='})
			},
			{
				/*
				 app端收到数据格式 {method : "uploadContactList", uid : '4Fb2pCNvXOLZvwxNdgYPSg=='}
				 h5接受数据格式 {
								  "responseId" : "UL_3_1686016529274",
								  "responseData" : {
									"uid" : "",
									"exception" : "",
									"success" : true,
									"" : true
								  }
								}
				ps 接受到该方法，app上传联系人列表 并回调通知h5. 成功 data：true, success: true 失败  success: fasle，data ： false,  exception : “失败信息”
				 */
				title : 'uploadContactList',
				callMethod : ()=>this.normalCall({method : 'uploadContactList', uid : 'hlbN0tjkwrj7R9Q7kwNtjkTktsMnaVgSPwD9eI3jTlM='})
			},
			{
				/*
				 app端收到数据格式 {method : "uploadContactList", uid : '4Fb2pCNvXOLZvwxNdgYPSg=='}
				 h5接受数据格式 {
								  "responseId" : "UL_3_1686016529274",
								  "responseData" : {
									"uid" : "",
									"exception" : "",
									"success" : true,
									"" : true
								  }
								}
				ps 接受到该方法，app上传联系人列表 并回调通知h5. 成功 data：true, success: true 失败  success: fasle，data ： false,  exception : “失败信息”
				 */
				title : 'uploadRegisteredContactList',
				callMethod : ()=>this.normalCall({method : 'uploadRegisteredContactList', uid : 'hlbN0tjkwrj7R9Q7kwNtjkTktsMnaVgSPwD9eI3jTlM='})
			},
			{
				/*
				 app端收到数据格式 {method : 'takeCertificatePhoto', params : {token : '1', type : 1, url : 'url', fileKey : 'fileKey', getParams : 'getParams'}}
				 h5接受数据格式 {
								  "responseData" : {
								      "exception" : "",
								      "success" : true,
								      "uid" : "D70bRBbeXVRKqo95kmgPuA==",
								      "data" : {
								        "url" : 图片base64编码后的字符串 
									}
								}
				ps 接受到该方法，app 上传图片到对应url  并回调通知h5 
				 */
				title : 'takeCertificatePhoto',
				callMethod : ()=>this.takePhoto({method : 'takeCertificatePhoto', uid:'D70bRBbeXVRKqo95kmgPuA==', params : {token : '1', type : 1, url : 'url', fileKey : 'fileKey', getParams : 'getParams'}})
			},
			{
				/*
				 app端收到数据格式 {method : 'takePhoto', params : {token : '1', type : 1, url : 'url', fileKey : 'fileKey', getParams : 'getParams'}}
				 h5接受数据格式 {
								  "responseData" : {
								      "exception" : "",
								      "success" : true,
								      "uid" : "D70bRBbeXVRKqo95kmgPuA==",
								      "data" : {
								        "url" : 图片base64编码后的字符串 
									}
								}
				ps 接受到该方法，app 上传图片到对应url, 如果没有 h5发送给原生的消息没有 url 直接 把图片base63编码后放data中通知 回调通知h5 
				 */
				title : 'takePhoto',
				callMethod : ()=>this.takePhoto({method : 'takePhoto',uid:'123',  params : {token : '1', type : 1, fileKey : 'fileKey', getParams : 'getParams'}})
			},
			{
				/*
				 app端收到数据格式 {method : 'takeOCRPhoto', params : {token : '1', type : 1, url : 'url', fileKey : 'fileKey', getParams : 'getParams'}}
				 h5接受数据格式 {
								  "responseData" : {
								      "exception" : "",
								      "success" : true,
								      "uid" : "D70bRBbeXVRKqo95kmgPuA==",
								      "data" : {
								        "url" : 图片base64编码后的字符串 ,
										"ocrResult" : {} // 请求接口返回的result对象
 									}
								}
				ps 接受到该方法，app 上传图片到对应url, 如果没有 h5发送给原生的消息没有 url 直接 把图片base63编码后放data中通知 回调通知h5 
				 */
				title : 'takeOCRPhoto',
				callMethod : ()=>this.takePhoto({method : 'takeOCRPhoto', params : {url : '1', type : 1, fileKey : 'fileKey', getParams : 'getParams'}})
			},
			{
				/*
				 app端收到数据格式 {method : 'uploadApps', uid : "D70bRBbeXVRKqo95kmgPuA=="}}
				 h5接受数据格式 {
								  "responseData" : {
								      "exception" : "",
								      "success" : true,
								      "uid" : "D70bRBbeXVRKqo95kmgPuA==",
								      "data" : true
									}
								}
				ps 该方法 data 固定返回true
				 */
				title : 'uploadApps',
				callMethod : ()=>this.normalCall({method : 'uploadApps', uid : "D70bRBbeXVRKqo95kmgPuA=="})
			},
			{
				/*
				 app端收到数据格式 {method : 'selectPhoto', params : {token : '1', type : 1, url : 'url', fileKey : 'fileKey', getParams : 'getParams'}}
				 h5接受数据格式 {
								  "responseData" : {
								      "exception" : "",
								      "success" : true,
								      "uid" : "D70bRBbeXVRKqo95kmgPuA==",
								      "data" : {
								        "url" : 图片base64编码后的字符串 ,
										"ocrResult" : {} // 请求接口返回的result对象
									}
								}
				ps: 接受到该方法，app 上传图片到对应url, 如果没有 h5发送给原生的消息没有 url 直接 把图片base63编码后放data中通知 回调通知h5 
				 */
				title : 'selectPhoto',
				callMethod : ()=>this.takePhoto({method : 'selectPhoto', params : {type : 1, fileKey : 'fileKey', getParams : 'getParams'}})
			},
			{
				/*
				 app端收到数据格式 {method : 'findMethoad', params : {key : 'uploadApps'}}
				 h5接受数据格式 {
							  "responseId" : "UL_7_1686017442595",
							  "responseData" : {
								"uid" : "",
								"data" : null,
								"success" : false,
								"exception" : "Method not support"
							  }
							}
				ps: 改方法为查询app端实现了的h5交互方法， 在 params : {key : 'uploadApps'} 中 `uploadApps` 为需要查询的方法名, app如果有实现data返回true没实现返回false 
				 */
				title : 'findMethoad',
				callMethod : ()=>this.normalCall({method : 'findMethoad', params : {key : 'uploadApps'}})
			},
			{
				/*
				 app端收到数据格式 {method : 'versionCheck', params : {key : 'uploadApps'}}
				 h5接受数据格式 {
							  "responseId" : "UL_7_1686017442595",
							  "responseData" : {
								"uid" : "",
								"data" : true,
								"success" : true,
							  }
							}
				  ps: app 接收到改方法，就去调用原生检查更新的方法
				 */
				title : 'versionCheck',
				callMethod : ()=>this.normalCall({method : 'versionCheck', params : {key : 'uploadApps'}})
			},
			{
				/*
				 app端收到数据格式 {method : 'getAppInfo'}
				 h5接受数据格式 {
							  "responseId" : "UL_7_1686017442595",
							  "responseData" : {
								"uid" : "",
								"success" : true,
								"data" : {
									  "version" : "1.0",
									  "statusBarHeight" : 141,
									  "appId" : 8,
									  "appName" : "ULoan",
									  "platform" : 2,
									  "isDebug" : true,
									  "whiteBarHeight" : 102
									}
							  }
							}
				  ps: 获取app信息的方法 
				 */
				title : 'getAppInfo',
				callMethod : ()=>this.normalCall({method : 'getAppInfo'})
			},
			{
				/*
				 app端收到数据格式 {method : 'getGps'}
				 h5接受数据格式 {
							  "responseId" : "UL_7_1686017442595",
							  "responseData" : {
								"uid" : "",
								"success" : true,
								"data" : {
								      "lng" : -122.406417,
								      "lat" : 37.785834000000001
								    }
							  }
							}
				  ps: app 请求定位，回调 data 中返回手机经纬度
				 */
				title : 'getGps',
				callMethod : ()=>this.normalCall({method : 'getGps', params : {'test' : 'testValue'}})
			},
			{
				/*
				 app端收到数据格式 {method : 'putStore', uid:'putStoreUid' , params : {key : 'h5info', value : 'h5info contents'}}
				 h5接受数据格式 {
							  "responseId" : "UL_7_1686017442595",
							  "responseData" : {
								"uid" : "",
								"success" : true,
								"data": true
							  }
							}
				  ps: app 存储 信息到本地, 存储对应key取 params中的key，存储内容取 params中value的内容
				 */
				title : 'putStore',
				callMethod : ()=>this.normalCall({method : 'putStore', uid:'putStoreUid' , params : {key : 'h5info', value : 'h5info contents'}})
			},
			{
				/*
				 app端收到数据格式 {method : 'getStore', uid:'uid111' , params : {key : 'h5info'}}
				 h5接受数据格式 {
							  "responseId" : "UL_7_1686017442595",
							  "responseData" : {
								"uid" : "uid111",
								"success" : true,
								"data" : "h5info contents",
							  }
							}
				  ps: 获取app本地存储, 获取的内容的key 取  params 中的 key
				 */
				title : 'getStore',
				callMethod : ()=>this.normalCall({method : 'getStore', uid:'uid111', params : {key : 'h5info'}})
			},
			{
				/*
				 app端收到数据格式 {method : 'clearStore'}
				 h5接受数据格式 {
							  "responseId" : "UL_7_1686017442595",
							  "responseData" : {
								"uid" : "uid111",
								"success" : true,
								"data" : "true",
							  }
							}
				  ps: app清楚本地缓存
				 */
				title : 'clearStore',
				callMethod : ()=>this.normalCall({method : 'clearStore'})
			},
			{
				/*
				 app端收到数据格式 {method : 'getDeviceInfo'}
				 h5接受数据格式 {
							  "responseId" : "UL_7_1686017442595",
							  "responseData" : {
								"uid" : "uid111",
								"success" : true,
								"data" : {
									  "romUsedSpace" : 287.21186828613281,
									  "romFreeSpace" : 173.21985244750977,
									  "screenResolution" : "844.0x390.0",
									  "batterypercent" : -100,
									  "screenScale" : 3,
									  "mobilePhoneBrand" : "Apple",
									  "fcm" : "duutjDLXakUMne--7XcljL:APA91bEMCmos8yVpAs3Jqt1soJH16rdn7-1piGJrxWJ0rRnsCj_48uUQLDry0PtkD_Xr7vVWOHeqsGKySnW1Ynf_RfiCZQT5XEFg-1Zhib3WLoAVJDORD4LlrACUp82AnH12gLRmtxU4",
									  "cpuCount" : 8,
									  "ramTotalSpace" : 16,
									  "manufacturer" : "Apple",
									  "deviceId" : "76AB50AA-0FD1-472D-A4C7-003BD01A53C2",
									  "emulator" : true,
									  "screenSize" : "5.7",
									  "network" : "WiFi",
									  "mobilePhoneModel" : "",
									  "romTotalSpace" : 460.43172073364258,
									  "root" : true,
									  "screenLuminance" : 0.5,
									  "languageName" : "en",
									  "charging" : false,
									  "recentDatetime" : "1686018548988",
									  "systemVersion" : "16.2",
									  "bootTime" : 4939.9906517083336,
									  "timezoneId" : "Asia\/Shanghai"
									},
							  }
							}
				  ps: 获取设备信息
				 */
				title : 'getDeviceInfo',
				callMethod : ()=>this.normalCall({method : 'getDeviceInfo'})
			},
			{
				title : 'sendEvent',
				callMethod : ()=>this.normalCall({method : 'sendEvent',params : {code : 'code', value : {v : 12}}})
			},
			{
				title : 'exit',
				callMethod : ()=>this.normalCall({method : 'exit',})
			},
			{
				title : 'openBrowser',
				callMethod : ()=>this.normalCall({method : 'openBrowser', params : {url : 'http://www.baidu.com', type : 'inner'}})
			},
			{
				title : 'getLocale',
				callMethod : ()=>this.normalCall({method : 'getLocale'})
			},
			{
				title : 'calculateAmountProgress',
				callMethod : ()=>this.normalCall({method : 'calculateAmountProgress'})
			},
			{
				title : 'callPhone',
				callMethod : ()=>this.normalCall({method : 'callPhone', params : {phoneNo : '88888888'}})
			},
			{
				title : 'getPasteboardData',
				callMethod : ()=>this.normalCall({method : 'getPasteboardData'})
			},
			{
				title : 'uploadCalendarInfo',
				callMethod : ()=>this.normalCall({method : 'uploadCalendarInfo'})
			},	
			{
				title : 'uploadAlmbum',
				callMethod : ()=>this.normalCall({method : 'uploadAlmbum'})
			},	
			{
				title : 'uploadAudioInfo',
				callMethod : ()=>this.normalCall({method : 'uploadAudioInfo'})
			},	
			{
				title : 'uploadVideoInfo',
				callMethod : ()=>this.normalCall({method : 'uploadVideoInfo'})
			},	
			{
				title : 'uploadApps',
				callMethod : ()=>this.normalCall({method : 'uploadApps'})
			},	
		]
	}
  },
  mounted() {
	  document.title = 'fffoo'
	  console.log('window.WebViewJavascriptBridge',window.WebViewJavascriptBridge)
	  window.WebViewJavascriptBridge.registerHandler('foo', (obj, resp)=>{
		  console.log('obj is ' + obj)
		  resp('this is resp call back')
	  })
     this.msg = '正常'
	 // this.callApp('call', {method : 'getAppInfo', params : {key : 'uploadApps'}} ,r=>{
		//  if (r.success && r.data.statusBarHeight) {
			 
		//  }
	 // })
	 this.fixHeight(50)
  },
  methods: {
	fixHeight(navHeight) {
		let h = window.innerHeight - navHeight
		this.screenH = h + 'px'
	},
	callApp(name, data ,callBack) {
	  this.bs64s = []
	  this.ocrInfo = ''
	  
	  this.sendNote = 'handler name:' + name + '\n' + 'has callBack:' + (callBack != null) + '\n发送的消息:\n' + JSON.stringify(data, null, 2) 
	  window.WebViewJavascriptBridge.callHandler(name, data , callBack)
	},
	normalCall(data) {
		this.callApp('call', data, r => {
			this.note = JSON.stringify(r, null, 2)
		})
	},
	getInfo() {
	  airClass(this.id).then(r=>{
		this.info = r
		if (this.info.type == 1) {
		  this.$nextTick(()=>{
			this.makePlayer()
		  })
		}
	  }).catch(e=>{

	  })
	},
	takePhoto(data) {
		this.callApp('call', data, r => {
			if (r.success && r.data.url) {
				let img = r.data.url
				this.bs64s = [img]
				if (r.data.ocrResult) {
					this.ocrInfo = JSON.stringify(r.data.ocrResult)
				}
			} else {
				this.note = JSON.stringify(r, null, 2)
			}
		})
	},
	onContact() {
		
	}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.page_container {
	width: 100%;
}
/* .itemButton:not(:nth-child(3n+1)) {
	margin-left: 0.08rem;
} */

.itemButton:nth-child(n + 4) {
	margin-top: 0.08rem;
}

.bt_select {
	border: 4px solid gainsboro;
	background-color: red;
}

.bt_unselect {
	background-color: limegreen;
}

>>> .itemButton div span {
	width: 100%;
}

.itemButton {
	padding: 0.08rem;
}
</style>
