  [toc]

##### 预约练车设置
* Domain: dev-http://app2.chaojijiaolian.cn.chelun.com     online-http://app2.chaojijiaolian.cn
* API：/audio/deleteSystem
* author: Ada
* 请求方式：GET/POST   
* 参数： 

||参数名||类型||是否必填||说明||
||_token||string||是||登录token||
||appversion||string||是||app版本||
||systemIds||string||是||要删除的所有系统id，通过","(英文状态)拼接的字符串||
返回
```
//成功
{
    "code": 0,
    "msg": "success"
}
```
##### 同驾校考场列表
* Domain: dev-http://app2.chaojijiaolian.cn.chelun.com     online-http://app2.chaojijiaolian.cn
* API：/audio/sameSchoolSystemList
* author: Ada
* 请求方式：GET/POST   
* 参数： 

||参数名||类型||是否必填||说明||
||_token||string||是||登录token||
||appversion||string||是||app版本||
||pageIndex||int||否||分页开始位置，默认1||
||pageSize||int||否||每页数据条数，默认10条||
返回
```
//成功
{
	code: 0,
	msg: "成功",
	result: {
		lastpage: false,
		list: [{
				coachName: "番茄炒蛋",
				siteId: "0bad029555c53316",
				siteName: "全国通用",
				size: "5.39M",
				systemid: 97929931380969470,
				name: "基础版",
			},
			{
				coachName: "番茄炒蛋",
				siteId: "da621fcf62b67927",
				siteName: "桃花岭科目三考场",
				size: "0.6M",
				systemid: 305218971804098560,
				name: "南京多伦",
			}
		],
		schoolName: "车轮互联网驾校"
	}
}
```
##### 同驾校考场下载
* Domain: dev-http://dev.cjjl.chelun.com     online-http://cjjl.chelun.com
* API：/Audio/Download
* author: Eric
* 请求方式：GET/POST   
* 参数： 

||参数名||类型||是否必填||说明||
||_token||string||是||登录token||
||appversion||string||是||app版本||
||siteId||string||是||语音id||
返回
```
{
    "code": 0,
    "msg": "成功",
    "result": {
        "list": [
            {
                "vid": "29419",
                "title": "上坡起步",
                "content": "上坡起步和定点停车",
                "icon": "http://t4.58cdn.com.cn/brandads/jxedt/back/n_v1bkuyfvouhjbvnll6lbwq_4efbf870af0a0e2d.png",
                "type": "2",
                "indexNum": "0",
                "defaultTitle": "上坡起步",
                "defaultContent": "上坡起步和定点停车",
                "updateTime": "2018-04-20 16:48:16",
                "cid": "10949",
                "source": "0",
                "projectid": null,
                "hide": "0",
                "systemid": "346537134478774272",
                "reserve1": "0",
                "md5": "",
                "flag": "0",
                "temp_vid": "0",
                "category": "0"
            },]
	}
}
```
##### 指令图标列表
* Domain: dev-http://app2.chaojijiaolian.cn.chelun.com     online-http://app2.chaojijiaolian.cn
* API：/audio/iconList
* author: Ada
* 请求方式：GET/POST   
* 参数： 

||参数名||类型||是否必填||说明||
||_token||string||是||登录token||
||appversion||string||是||app版本||
||type||int||否||图标类型： 2科二语音图标  32科三语音图标 34 科三灯光指令图标||
||pageIndex||int||否||分页开始位置，默认1||
||pageSize||int||否||每页数据条数，默认10条||
返回
```
//成功
{
	code: 0,
	msg: "成功",
	result: {
		lastpage: false,
		list: [{
				createTime: 1522821867,
				icon: "http://cjjl-wx.img.cdn.chaojijiaolian.cn/strartupPic_16b2b793-0c1e-462d-83b5-2d2823de0c86.png",
				id: 2,
				indexNum: 1,
				remark: "测试",
				sortIndex: 0,
				status: 1,
				title: "远光灯",
				type: 32,
				updateTime: 1523154212,
			},
			{
				createTime: 1522821889,
				icon: "http://cjjl-wx.img.cdn.chaojijiaolian.cn/strartupPic_df74d45d-d57d-4785-be11-1372176686a1.png",
				id: 3,
				indexNum: 1,
				remark: "测试啊",
				sortIndex: 0,
				status: 1,
				title: "近光灯",
				type: 2,
				updateTime: 1522821889,
			}
		]
	}
}
```
##### 最近模拟线路
* Domain: dev-http://app2.chaojijiaolian.cn.chelun.com     online-http://app2.chaojijiaolian.cn
* API：/audioline/recently/list
* author: Ada
* 请求方式：GET/POST   
* 参数： 

||参数名||类型||是否必填||说明||
||_token||string||是||登录token||
||appversion||string||是||app版本||
||pageIndex||int||否||分页开始位置 默认1||
||pageSize||int||否||分页每页数据量 默认10条||
返回
```
{
	code: 0,
	msg: "成功",
	result: {
		lastpage: false,
		routeList: [{
				acceptCount: 0,
				acceptCountStr: "使用0次",
				lineLength: 0,
				lineLengthStr: "0km",
				nodeCount: 4,
				subject: 2,
				title: "[分享]范冰冰呢",
				vid: 964,
			},
			{
				acceptCount: 2,
				acceptCountStr: "使用2次",
				lineLength: 0,
				lineLengthStr: "0km",
				nodeCount: 2,
				subject: 2,
				title: "[分享]更高好拜拜",
				vid: 986,
			}
		]
	}
}
```
##### 附近路线
* Domain: dev-http://app2.chaojijiaolian.cn.chelun.com     online-http://app2.chaojijiaolian.cn
* API：/audioline/near/list/v2
* author: Ada
* 请求方式：GET/POST   
* 参数： 

||参数名||类型||是否必填||说明||
||_token||string||是||登录token||
||appversion||string||是||app版本||
||subject||int||是||科目，2或者3||
||pageindex||int||否||分页开始位置 默认1||
||pagesize||int||否||分页每页数据量 默认10条||

返回
```
{
    "code": 0,
    "msg": "成功",
    "result": {
        "lastpage": true, //是否是最后一页
        "linelist": [
            {
                "vid": 893,
                "coachname": "测试年",
                "schoolname": "爱乐驾校二部",
                "face": "http://picture.eclicks.cn/g1/l/2017/03/20/0bd0550e3b4d6db5_640_640.jpg",
                "linename": "和的活动和",
                "nodeCount": 3, //路线节点数量
                "lineLengthStr": "292.4km", //路线总长度
                "acceptCountStr":  "1人使用过" //路线被使用次数,
                "distance": "离我19.1km"
            }
        ]
    }
}
```
##### 新增或编辑指令（新增参数）
* Domain: dev-http://app2.chaojijiaolian.cn.chelun.com     online-http://app2.chaojijiaolian.cn
* API：/audio/add 、 /audio/update 
* author: Ada
* 请求方式：GET/POST   
* 参数： 

||参数名||类型||是否必填||说明||
||icon||string||否||图标链接||
||iconId||int||否||图标id||
新增以上两个参数
返回
```
{
    "code": 0,
    "msg": "成功"
}
```
##### 线索咨询小红点接口
https://www.tapd.cn/20037381/markdown_wikis/view/#1120037381001003765@toc7