# 主题CRUD

## 主题列表

```
{
	"url": "http://{ip:port}/cms/theme/list",
	
	"http-method": "get",
	
	"request-params": {
		"page_num": "1", # 必填，必须大于0
		"rows": "20", # 必填，必须大于0，不能大于50
	},
	
	"response-data": {
        "resultMsg": "操作成功",
        "data": {
            "count": 1,
            "list": [
                {
                    "status": 1,
                    "id": "66bfc6dc508f47938ffeeb40c8e96bd4",
                    "file_name": "what are you doing",
                    "uri": "http://xb-vod.oss-cn-hangzhou.aliyuncs.com/m4a/what_are_you_doing_CN.m4a",
                    "create_time": "2017-07-07 10:31:50",
                    "name": "七月",
                    "count": 2,
                    "modify_time": "2017-07-07 10:31:52"
                }
            ]
        },
        "resultCode": 0
    }
	
}
```

---

## 主题详情

```
{
	"url": "http://{ip:port}/cms/theme/info",
	
	"http-method": "get",
	
	"request-params": {
		"id": "id" # 主题的id
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": {
            "color": "#FFE4C4",
            "name": "bbb",
            "id": "848bbaed-bb28-4682-80b2-bed5cb9a33e5",
            "audio_file": {
                "guide": "bbb",
                "id": "be909162-9a83-4ca3-b804-fe6ac9566eea",
                "cover": "bbb",
                "uri": "bbb",
                "file_name": "bbb"
            },
            "brief": "bbb",
            "bg_img": "bbb"
        }
    }
	
}
```

---

## 新增主题

```
{
	"url": "http://{ip:port}/cms/theme/add",
	
	"http-method": "post",
	
	"request-params": {
        "name":"aaa",
        "brief":"aaa",
        "bg_img":"aaa", 
        "color":"#FFE4C4", 
        "audio_file":{
            "file_name":"aaa",
            "size":"111",
            "uri":"aaa",
            "duration":"111",
            "guide":"aaa", 
            "cover":"aaa"
        }
    },
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 修改主题

```
{
	"url": "http://{ip:port}/cms/theme/update",
	
	"http-method": "post",
	
	"request-params": {
        "id":"a835c017-c2c5-4544-9b1d-ab71b214c064",
        "name":"bbb",
        "brief":"bbb",
        "bg_img":"bbb", 
        "color":"#FFE4C4", 
        "audio_file":{
            "id":"604722eb-b3ae-4295-94b0-a9fa54d18716",
            "file_name":"bbb",
            "size":"222",
            "uri":"bbb",
            "duration":"222",
            "guide":"bbb", 
            "cover":"bbb"
        }
    },
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 删除主题

```
{
	"url": "http://{ip:port}/cms/theme/remove",
	
	"http-method": "post",
	
	"request-params": {
        "id":"xxx", # id
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 撤消删除主题

```
{
	"url": "http://{ip:port}/cms/theme/undo/remove",
	
	"http-method": "post",
	
	"request-params": {
        "id":"xxx", # id
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 上架主题

```
{
	"url": "http://{ip:port}/cms/theme/up",
	
	"http-method": "post",
	
	"request-params": {
        "id":"xxx", # id
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 下架主题

```
{
	"url": "http://{ip:port}/cms/theme/down",
	
	"http-method": "post",
	
	"request-params": {
        "id":"xxx", # id
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 关联子主题列表页面

```
{
	"url": "http://{ip:port}/cms/theme/set/list",
	
	"http-method": "get",
	
	"request-params": {
        "id":"xxx", # id
	},
	
	"response-data": {
        "resultMsg": "操作成功",
        "resultCode": 0,
        "data": {
            "alreadly_set": [
                {
                    "title": "我有友情要出租",
                    "id": "09f1c809-afeb-42e2-a942-d09fe89ef387"
                },
                {
                    "title": "Jolly, Happy Ganders",
                    "id": "0dd35817-762f-4f69-a8af-62f1f79cbc30"
                }
            ],
            "can_set": [
                {
                    "title": "qwer",
                    "id": "qwreqewrqdfasafasfsafd"
                }
            ]
        }
    }
	
}
```

---

## 关联子主题提交

```
{
	"url": "http://{ip:port}/cms/theme/set/go",
	
	"http-method": "post",
	
	"request-params": {
        "id":"xxx", # id
        "res_id":['id0', 'id1', 'id2']
	},
	
	"response-data": {
        "resultMsg": "操作成功",
        "data": null,
        "resultCode": 0
    }
	
}
```

---

## 子主题列表

```
{
	"url": "http://{ip:port}/cms/theme/child/list",
	
	"http-method": "get",
	
	"request-params": {
        "page_num": "1", # 必填，必须大于0
		"rows": "20", # 必填，必须大于0，不能大于50
	},
	
	"response-data": {
        "resultMsg": "操作成功",
        "resultCode": 0,
        "data": {
            "count": 1,
            "list": [
                {
                    "create_time": "2017-06-30 15:29:15",
                    "rel": true,
                    "audio_uri": "http://xb-vod.oss-cn-hangzhou.aliyuncs.com/m4a/what_are_you_doing_CN.m4a",
                    "modify_time": "2017-06-30 16:15:53",
                    "book": "如何读",
                    "video_uri": "http://xb-vod.oss-cn-hangzhou.aliyuncs.com/m4a/what_are_you_doing_EN.m4a",
                    "name": "八月"
                }
            ]
        }
    }
	
}
```

---

## 新增子主题

```
{
	"url": "http://{ip:port}/cms/theme/child/add",
	
	"http-method": "post",
	
	"request-params": {
        "name":"111", # 主题名称
        "brief":"111", # 简介
        "bg_img":"111", # 背景图片
        "audio_file":{ # 音频信息
            "file_name":"111.mp3",
            "size":"111",
            "uri":"111",
            "duration":"111",
            "cover":"导读图片",
            "guide":"导读文章"
        },
        "video_file":{ # 视频信息
            "file_name":"111",
            "size":"111",
            "uri":"111",
            "duration":"111",
            "cover":"视频封面图"
        },
        "book_file":{
            "orid":"oridoridorid",
            "file_name":"xxx",
            "cover":"图片地址"
        }
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 修改子主题

```
{
	"url": "http://{ip:port}/cms/theme/child/update",
	
	"http-method": "post",
	
	"request-params": {
        "id":"id"
        "name":"111", # 主题名称
        "brief":"111", # 简介
        "bg_img":"111", # 背景图片
        "audio_file":{ # 音频信息
            "id":"id",
            "file_name":"111.mp3",
            "size":"111",
            "uri":"111",
            "duration":"111",
            "cover":"导读图片",
            "guide":"导读文章"
        },
        "video_file":{ # 视频信息
            "id":"id",
            "file_name":"111",
            "size":"111",
            "uri":"111",
            "duration":"111",
            "cover":"视频封面图"
        },
        "book_file":{
            "orid":"oridoridorid",
            "file_name":"xxx",
            "cover":"图片地址"
        }
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 删除子主题

```
{
	"url": "http://{ip:port}/cms/theme/child/remove",
	
	"http-method": "post",
	
	"request-params": {
        "id":"xxx", # id
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 撤消删除子主题

```
{
	"url": "http://{ip:port}/cms/theme/child/undo/remove",
	
	"http-method": "post",
	
	"request-params": {
        "id":"xxx", # id
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 撤消删除子主题

```
{
	"url": "http://{ip:port}/cms/theme/undo/remove",
	
	"http-method": "post",
	
	"request-params": {
        "id":"xxx", # id
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": null
    }
	
}
```

---

## 子主题详情

```
{
	"url": "http://{ip:port}/cms/theme/child/info",
	
	"http-method": "get",
	
	"request-params": {
        "id":"xxx", # id
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": {
            "id": "3a2f760a-407a-4c22-9351-7a34ec8f8bdb",
            "bg_img": null,
            "brief": null,
            "icon": null,
            "name": "Chi Chi Bird"
        }
    }
	
}
```

---

## 子主题关联绘本

```
{
	"url": "http://{ip:port}/cms/theme/child/books",
	
	"http-method": "get",
	
	"request-params": {
        "orid":"xxx", # id
        "name":"xxx", # id
	},
	
	"response-data": {
        "resultCode": 0,
        "data": [
            {
                "id": "086c0a99e60341daa10fbf6f24c61743",
                "file_name": "What Funny Shapes!"
            },
            {
                "id": "116d10a7357f4184b48999ba49de2f55",
                "file_name": "最快乐的人"
            },
            {
                "id": "12aca7b5cee44e18a0e924332aa20f38",
                "file_name": "如何读"
            },
            {
                "id": "1642de2fb9b34ab697a6d5bc6718cee1",
                "file_name": "Baby Bear s Birthday"
            }
        ],
        "resultMsg": "操作成功"
    }
	
}
```

---

## 子主题关联主题的列表接口

```
{
	"url": "http://{ip:port}/cms/theme/child/p",
	
	"http-method": "get",
	
	"request-params": {
        "id":"xxx", # id
	},
	
	"response-data": {
        "resultCode": 0,
        "resultMsg": "操作成功",
        "data": [
            {
                "name": "七月"
            }
        ]
    }
	
}
```