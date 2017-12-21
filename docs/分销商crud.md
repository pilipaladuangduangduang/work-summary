# 分销商CRUD

## 分销商列表

```
{
	"url": "http://{ip:port}/cms/dist/list",
	
	"http-method": "get",
	
	"request-params": {
		"nickname": "nickname", # 选填
		"page_num": "1", # 必填，其他必须大于0
		"rows": "20", # 必填，其他必须大于0，不能大于50
	},
	
	"response-data": {
		"data": {
			"list": [
				{
					"create_user": "admin",
					"remark": "18888888888",
					"nickname": "测试001",
					"password": "dff0012b25bfdd7fc7524e52732e03aa",
					"create_time": "2017-06-17 01:39:38",
					"invite_code": "497eca21-19a7-4839-b80a-37bc1ecf7d27",
					"modify_time": "2017-06-17 01:39:38",
					"modify_user": "admin",
					"mdn": "18888888888",
					"delete_flag": 0,
					"id": "7a9d6938-bebe-4896-bb04-3f12a380c1ee"
				}
			],
			"count": 1
		},
		"resultCode": 0,
		"resultMsg": "操作成功"
	}
	
}
```

---

## 根据ID获取分销商信息

```
{
	"url": "http://{ip:port}/cms/dist/info",
	
	"http-method": "get",
	
	"request-params": {
		
	},
	
	"response-data": {
		"data": {
			"create_user": "admin",
			"remark": "18888888888",
			"nickname": "测试001",
			"password": "dff0012b25bfdd7fc7524e52732e03aa",
			"create_time": "2017-06-17 01:39:38",
			"invite_code": "497eca21-19a7-4839-b80a-37bc1ecf7d27",
			"modify_time": "2017-06-17 01:39:38",
			"modify_user": "admin",
			"mdn": "18888888888",
			"delete_flag": 0,
			"id": "7a9d6938-bebe-4896-bb04-3f12a380c1ee"
		},
		"resultCode": 0,
		"resultMsg": "操作成功"
	}
	
}
```

---

## 分销商修改自己的密码

```
{
	"url": "http://{ip:port}/cms/dist/pwd",
	
	"http-method": "post",
	
	"request-params": {
		"id": "xxx", # 必填
		"old_password": "xxx", # 必填
		"new_password": "xxx", # 必填
	},
	
	# 修改密码成功后，需要重新跳转到登录界面
	"response-data": {
		"data": {
			"mdn": "18888888888",
			"password": "654321"
		},
		"resultCode": 99,
		"resultMsg": "未登录或登录失效，返回登录页面"
	}
	
}
```

---

## 新增分销商

```
{
	"url": "http://{ip:port}/cms/dist/add",
	
	"http-method": "post",
	
	"request-params": {
		"nickname": "xxx", # 必填
		"mdn": "xxx", # 必填
		"remark": "xxx", # 必填
		"bank_card_no": "xxx", # 选填
		"bank_card_owner": "xxx", # 选填
		"bank_card_branch": "xxx", # 选填
	},
	
	"response-data": {
		"data": null,
		"resultCode": 0,
		"resultMsg": "操作成功"
	}
	
}
```

---

### 修改分销商信息

```
{
	"url": "http://{ip:port}/cms/dist/update",
	
	"http-method": "post",
	
	"request-params": {
		"id": "xxx", # 必填
		"nickname": "xxx", # 必填
		"remark": "xxx", # 必填
		"bank_card_no": "xxx", # 选填
		"bank_card_owner": "xxx", # 选填
		"bank_card_branch": "xxx", # 选填
	},
	
	"response-data": {
		"data": null,
		"resultCode": 0,
		"resultMsg": "操作成功"
	}
	
}
```

---

### 删除分销商

```
{
	"url": "http://{ip:port}/cms/dist/remove",
	
	"http-method": "post",
	
	"request-params": {
		"id": "xxx", # 必填
	},
	
	"response-data": {
		"data": null,
		"resultCode": 0,
		"resultMsg": "操作成功"
	}
	
}
```

---

### 撤消删除分销商

```
{
	"url": "http://{ip:port}/cms/dist/undo/remove",
	
	"http-method": "post",
	
	"request-params": {
		"id": "xxx", # 必填
	},
	
	"response-data": {
		"data": null,
		"resultCode": 0,
		"resultMsg": "操作成功"
	}
	
}
```