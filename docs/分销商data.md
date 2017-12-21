# 分销商data

## 分销商日月数据（运营端）

```
{
	"url": "http://{ip:port}/cms/dist/all/data",
	
	"http-method": "get",
	
	"request-params": {
        无
	},
	
	"response-data": {
        "data": {
            "yesterday_sales_count": 0,
            "current_month_sales": 5188,
            "today_sales": 0,
            "last_month_sales_count": 0,
            "current_month_sales_count": 5,
            "last_month_sales": 0,
            "yesterday_sales": 0,
            "today_sales_count": 0
        },
        "resultCode": 0,
        "resultMsg": "操作成功"
    }
	
}
```

## 分销商日月数据（销售端）

```
{
	"url": "http://{ip:port}/cms/dist/one/data",
	
	"http-method": "get",
	
	"request-params": {
        无
	},
	
	"response-data": {
        "data": {
            "yesterday_sales_count": 0,
            "current_month_sales": 4688,
            "today_sales": 0,
            "last_month_sales_count": 0,
            "current_month_sales_count": 4,
            "last_month_sales": 0,
            "yesterday_sales": 0,
            "today_sales_count": 0
        },
        "resultCode": 0,
        "resultMsg": "操作成功"
    }
	
}
```

## 分销上明细数据（运营端）

```
{
	"url": "http://{ip:port}/cms/dist/all/list/data",
	
	"http-method": "get",
	
	"request-params": {
        "consumer": "", # 购买者，选填
        "nickname": "", # 分销商，选填
        "start_time": "", # 开始时间，选填
        "end_time": "", # 结束时间，选填
        "page_num": "1", # 第几页，必填
        "rows": "20", # 一页几条，必填
	},
	
	"response-data": {
        "data": {
            "count": 4,
            "list": [
                {
                    "pay_time": "2017-06-20 16:06:07",
                    "nick_name": "hehe",
                    "suber_name": "妈个鸡",
                    "price": 1000,
                    "goods_name": "pony school"
                },
                {
                    "pay_time": "2017-06-12 15:26:56",
                    "nick_name": "hehe",
                    "suber_name": "妈个鸡",
                    "price": 2000,
                    "goods_name": "pony school"
                },
                {
                    "pay_time": "2017-06-20 16:05:59",
                    "nick_name": "hehe",
                    "suber_name": "",
                    "price": 800,
                    "goods_name": "pony school"
                },
                {
                    "pay_time": "2017-05-01 16:06:02",
                    "nick_name": "hehe",
                    "suber_name": "妈个鸡",
                    "price": 888,
                    "goods_name": "pony school"
                }
            ]
        },
        "resultCode": 0,
        "resultMsg": "操作成功"
    }
	
}
```

## 分销上明细数据（销售端）

```
{
	"url": "http://{ip:port}/cms/dist/one/list/data",
	
	"http-method": "get",
	
	"request-params": {
        "consumer": "", # 购买者，选填
        "nickname": "", # 分销商，选填
        "start_time": "", # 开始时间，选填
        "end_time": "", # 结束时间，选填
        "page_num": "1", # 第几页，必填
        "rows": "20", # 一页几条，必填
	},
	
	"response-data": {
        "data": {
            "count": 4,
            "list": [
                {
                    "pay_time": "2017-06-20 16:06:07",
                    "nick_name": "hehe",
                    "suber_name": "妈个鸡",
                    "price": 1000,
                    "goods_name": "pony school"
                },
                {
                    "pay_time": "2017-06-12 15:26:56",
                    "nick_name": "hehe",
                    "suber_name": "妈个鸡",
                    "price": 2000,
                    "goods_name": "pony school"
                },
                {
                    "pay_time": "2017-06-20 16:05:59",
                    "nick_name": "hehe",
                    "suber_name": "",
                    "price": 800,
                    "goods_name": "pony school"
                },
                {
                    "pay_time": "2017-05-01 16:06:02",
                    "nick_name": "hehe",
                    "suber_name": "妈个鸡",
                    "price": 888,
                    "goods_name": "pony school"
                }
            ]
        },
        "resultCode": 0,
        "resultMsg": "操作成功"
    }
	
}
```

## 导出分销商Excel接口

```
{
	"url": "http://{ip:port}/cms/dist/export/data",
	
	"http-method": "get",
	
	"request-params": {
        "consumer": "", # 购买者，选填
        "nickname": "", # 分销商，选填
        "start_time": "", # 开始时间，选填
        "end_time": "", # 结束时间，选填
	},
	
	"response-data": dist_data.xls
	
}
```