# App页面新增

### 描述↓↓↓↓↓↓↓

新增App页面，目前App页面分为看、听、vip、我的四个部分

### 注意事项↓↓↓↓↓↓↓

无

### 接口地址↓↓↓↓↓↓↓

http://120.26.4.86/cms/app/add POST

### 接口开发进度↓↓↓↓↓↓↓

[OK](#开发状态字典表)

### 请求参数↓↓↓↓↓↓↓

| 名称               | 类型               | 必填               | 描述
| :----------------- | :----------------- | :----------------- | :----------------- 
| name               | string             | yes                | App页面名称
| uri                | string             | yes                | 具体的页面内容，是一个大json
| type               | string             | yes                | App页面类型：watch、listen、vip、myself、h5
| remark             | string             | no                 | 备注

### 响应结果↓↓↓↓↓↓↓

| 名称               | 类型               | 描述
| :----------------- | :----------------- | :----------------- 
| resultCode         | int                | 响应码，详细信息请点[这里](#返回码字典表)
| resultMsg          | string             | 响应信息
| data               | object             | null

---

# App页面复制

### 描述↓↓↓↓↓↓↓

复制一个App页面

### 注意事项↓↓↓↓↓↓↓

无

### 接口地址↓↓↓↓↓↓↓

http://120.26.4.86/cms/app/copy POST

### 接口开发进度↓↓↓↓↓↓↓

已完成（自测通过）

### 请求参数↓↓↓↓↓↓↓

| 名称               | 类型               | 必填               | 描述
| :----------------- | :----------------- | :----------------- | :----------------- 
| id                 | string             | yes                | App页面的唯一ID

### 响应结果↓↓↓↓↓↓↓

| 名称               | 类型               | 描述
| :----------------- | :----------------- | :----------------- 
| resultCode         | int                | 响应码，详细信息请点[这里](#返回码字典表)
| resultMsg          | string             | 响应信息
| data               | object             | null

---

# App页面修改

### 描述↓↓↓↓↓↓↓

用于修改App页面里面的信息

### 注意事项↓↓↓↓↓↓↓

 - 已发布的、定时发布中的页面是不允许修改的

### 接口地址↓↓↓↓↓↓↓

http://120.26.4.86/cms/app/update POST

### 请求参数↓↓↓↓↓↓↓

| 名称               | 类型               | 必填               | 描述
| :----------------- | :----------------- | :----------------- | :----------------- 
| id                 | string             | yes                | App页面的唯一ID
| name               | string             | yes                | App页面名称
| uri                | string             | yes                | 具体的页面内容，是一个大json
| remark             | string             | no                 | 备注

### 响应结果↓↓↓↓↓↓↓

| 名称               | 类型               | 描述
| :----------------- | :----------------- | :----------------- 
| resultCode         | int                | 响应码，详细信息请点[这里](#返回码字典表)
| resultMsg          | string             | 响应信息
| data               | object             | null

---

# App页面发布

### 描述↓↓↓↓↓↓↓

发布编辑中的App页面

### 注意事项↓↓↓↓↓↓↓

 - 只有编辑中状态的App页面才可以发布

### 接口地址↓↓↓↓↓↓↓

http://120.26.4.86/cms/app/publish POST

### 请求参数↓↓↓↓↓↓↓

| 名称               | 类型               | 必填               | 描述
| :----------------- | :----------------- | :----------------- | :----------------- 
| id                 | string             | yes                | App页面的唯一ID
| version_min        | string             | no                 | 起始版本
| version_max        | string             | no                 | 截止版本
| channel            | array              | no                 | 可选多个渠道
| publish_time       | datetime           | no                 | 定时发布的时间

### 响应结果↓↓↓↓↓↓↓

| 名称               | 类型               | 描述
| :----------------- | :----------------- | :----------------- 
| resultCode         | int                | 响应码，详细信息请点[这里](#返回码字典表)
| resultMsg          | string             | 响应信息
| data               | object             | null

---

# App页面撤销发布

### 描述↓↓↓↓↓↓↓

撤销发布App页面

### 注意事项↓↓↓↓↓↓↓

 - 只有定时发布状态的App页面才可以撤销发布

### 接口地址↓↓↓↓↓↓↓

http://120.26.4.86/cms/app/undo/publish POST

### 请求参数↓↓↓↓↓↓↓

| 名称               | 类型               | 必填               | 描述
| :----------------- | :----------------- | :----------------- | :----------------- 
| id                 | string             | yes                | App页面的唯一ID

### 响应结果↓↓↓↓↓↓↓

| 名称               | 类型               | 描述
| :----------------- | :----------------- | :----------------- 
| resultCode         | int                | 响应码，详细信息请点[这里](#返回码字典表)
| resultMsg          | string             | 响应信息
| data               | object             | null

---

# App页面删除

### 描述↓↓↓↓↓↓↓

用于删除App页面

### 注意事项↓↓↓↓↓↓↓

 - 线网环境、定时发布中的App页面是不允许删除的

### 接口地址↓↓↓↓↓↓↓

http://120.26.4.86/cms/app/rm POST

### 请求参数↓↓↓↓↓↓↓

| 名称               | 类型               | 必填               | 描述
| :----------------- | :----------------- | :----------------- | :----------------- 
| id                 | string             | yes                | App页面的唯一ID

### 响应结果↓↓↓↓↓↓↓

| 名称               | 类型               | 描述
| :----------------- | :----------------- | :----------------- 
| resultCode         | int                | 响应码，详细信息请点[这里](#返回码字典表)
| resultMsg          | string             | 响应信息
| data               | object             | null

---

# App页面撤销删除

### 描述↓↓↓↓↓↓↓

用于撤销删除App页面

### 注意事项↓↓↓↓↓↓↓

无

### 接口地址↓↓↓↓↓↓↓

http://120.26.4.86/cms/app/undo/rm POST

### 请求参数↓↓↓↓↓↓↓

| 名称               | 类型               | 必填               | 描述
| :----------------- | :----------------- | :----------------- | :----------------- 
| id                 | string             | yes                | App页面的唯一ID

### 响应结果↓↓↓↓↓↓↓

| 名称               | 类型               | 描述
| :----------------- | :----------------- | :----------------- 
| resultCode         | int                | 响应码，详细信息请点[这里](#返回码字典表)
| resultMsg          | string             | 响应信息
| data               | object             | null

---

# App页面列表

### 描述↓↓↓↓↓↓↓

展示编辑中、定时发布中、已发布的、特殊的App页面们

### 注意事项↓↓↓↓↓↓↓

无

### 接口地址↓↓↓↓↓↓↓

http://120.26.4.86/cms/app/list GET

### 请求参数↓↓↓↓↓↓↓

无

### 响应结果↓↓↓↓↓↓↓

| 名称               | 类型               | 描述
| :----------------- | :----------------- | :----------------- 
| resultCode         | int                | 响应码，详细信息请点[这里](#返回码字典表)
| resultMsg          | string             | 响应信息
| data               | object             | [HomeListModel](#homelistmodel)

---

# 返回码字典表

| 返回码             | 描述
| :----------------- | :----------------- 
| 0                  | 操作成功
| 1                  | 操作失败

# 开发状态字典表

| 状态码             | 描述
| :----------------- | :----------------- 
| OK                 | 开发完成，自测通过
| DEV                | 开发中

---

# 数据类型

描述：

XxxForm 指的是前端提交给后端的数据模型

XxxModel 指的是后端返回给前端的数据模型

### AppListModel

| 名称               | 类型               | 描述
| :----------------- | :----------------- | :----------------- 
| special            | array              | 特殊App页面：[[HomeModel](#homemodel), HomeModel...]
| waiting            | array              | 定时发布的App页面列表：[[HomeModel](#homemodel), HomeModel...]
| editing            | array              | 编辑区域的App页面列表：[[HomeModel](#homemodel), HomeModel...]
| running            | array              | 已发布的App页面列表（历史列表）：[[HomeModel](#homemodel), HomeModel...]

### HomeModel

| 名称               | 类型               | 描述
| :----------------- | :----------------- | :----------------- 
| id                 | string             | App页面的唯一ID
| name               | string             | App页面名称
| color              | string             | 文字颜色
| style              | string             | 展示样式
| cover              | string             | 展示图片
| uri                | string             | 具体的内容，是一个大json
| type               | string             | App页面类型
| brief              | string             | App页面描述
| remark             | string             | 备注
| switch             | string             | 开关
| version_min        | string             | 起始版本
| version_max        | string             | 版本截止
| channel            | array              | 数组类型，即使是单个渠道也是返回一个数组
| top_time           | datetime           | 置顶时间
| publish_user       | string             | 发布人
| publish_time       | datetime           | 发布时间
| create_user        | string             | 创建者
| modify_user        | string             | 修改者
| create_time        | string             | 创建时间
| modify_time        | string             | 修改时间




