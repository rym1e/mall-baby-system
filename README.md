# mall-baby-system

## 一、概述

一个基于gin+vue3的经典前后端分离商城系统

## 二、需求分析与设计

### 2.1 功能需求

**- 用户端：**
* 商品
     * 商品分类展示
     * 商品列表展示
     * 搜索
     * 详情页
* 购物车
     * 添加商品
     * 修改数量
     * 删除商品  
* 订单
     * 创建订单
     * 详情
     * 支付订单
     * 取消未支付订单
 * 我的
     * 历史订单
     * 退出系统
     * *查询物流状态

**- 管理员**

CRUD：
· 商品
· 商品类目
· 用户
· 角色
· 菜单
· 系统设置（管理员信息修改，支付功能配置）

### 2.2 技术栈

* 后端语言：GoLang(Go语言)
* 数据库：MySQL8.0
* 前端框架：vue.js(vue3)
* API 设计：RESTful API
* 身份验证：JWT（JSON WEB TOKEN）
* 部署：Docker

### 2.3 概要设计

* 网站首页：导航栏，展示热门商品，单机可进入，右上角设置搜索框
 ![image-20241127210612352](http://picture.xueden.cn/typora/image-20241127210612352.png)
* 商品列表页：将所有商品以一定顺序排列展示，如销量，价格，新品，并且在页面的左侧设置分类列表，选择这一类即可筛选出相应的商品，可以加上分页功能
* 详情页，展示商品的详细内容，如图片、名称、价格、数量、详细介绍
  
  <img width="1171" height="662" alt="image" src="https://github.com/user-attachments/assets/a72d267b-88d0-45e1-883c-bc9c82e76091" />
  
* 购物车：需要登录后访问，以列表形式展现已经选购的商品，包含图片，名称，价格，数量，合计数量和总额；删除车内商品，用户可以自动增减数量，自动计算总额
  
  <img width="1191" height="673" alt="image" src="https://github.com/user-attachments/assets/d32a4525-0019-481a-87c7-5fd00f5c0abe" />
  
* 个人中心，展示订单信息，需要登录
* 用户登录与注册

## 三、数据表结构设计和模型设计

### 3.1 用户表


| 字段        | 类型     | 说明                           |
| ----------- | -------- | ------------------------------ |
| id          | bigint   | 主键，自增ID                   |
| created_at  | datetime | 创建时间                       |
| updated_at  | datetime | 更新时间                       |
| deleted_at  | datetime | 删除时间                       |
| username    | varchar  | 用户名                         |
| password    | varchar  | 登录密码                       |
| phone       | varchar  | 手机号                         |
| wx_union_id | varchar  | 微信union_id(用来微信扫码登录) |
| wx_open_id  | varchar  | 微信open_id(用来微信扫码登录)  |
| avatar      | varchar  | 用户头像                       |
| sex         | varchar  | 用户性别                       |
| email       | varchar  | 邮箱                           |
| remarks     | varchar  | 备注                           |
| role_id     | bigint   | 角色ID                         |
|             |          |                                |

对应的数据模型如下所示：

```go
type SysUser struct {
	gorm.Model
	UserName  string `gorm:"column:username;type:varchar(50);" json:"userName"`
	PassWord  string `gorm:"column:password;type: varchar(36);" json:"passWord"`
	Phone     string `gorm:"column:phone;type:varchar(20);" json:"phone"`
	WxUnionId string `gorm:"column: wx_union_id;type:varchar(255);" json:"wxUnionId"`
	WxOpenId  string `gorm:"column:wx_open_id;type:varchar(255);" json:"wxOpenId"`
	Avatar    string `gorm:"column:avatar;type:varchar(255);" json:"avatar"`
	Sex       string `gorm:"column:sex;type:varchar(20);" json:"sex"`
	Email     string `gorm:"column:email;type:varchar(20);" json:"email"`
	Remarks   string `gorm:"column:remarks;type:varchar(255);" json:"remarks"`
	RoleId    uint   `gorm:"column:role_id;type:bigint(20);" json:"roleId"`
}
```




### 3.2 角色表（sys_role）

数据表结构如下所示：

| 字段       | 类型     | 说明         |
| ---------- | -------- | ------------ |
| id         | bigint   | 主键，自增ID |
| created_at | datetime | 创建时间     |
| updated_at | datetime | 更新时间     |
| deleted_at | datetime | 删除时间     |
| name       | varchar  | 角色名称     |
| is_admin   | tinyint  | 是否管理员   |
| sort       | int      | 排序         |
| remarks    | varchar  | 备注         |

对应的数据模型如下所示：

```go
type SysRole struct {
	gorm.Model
	Name    string `gorm:"column:name;type:varchar(100);" json:"name"`                // 角色名称
	IsAdmin int8   `gorm:"column:is_admin;type:tinyint(1);default:0" json:"is_admin"` // 是否是超管【0-否 1-是】
	Sort    int64  `gorm:"column:sort;type:int(11);default:0;" json:"sort"`           // 排序，序号越小越靠前
	Remarks string `gorm:"column:remarks;type:varchar(255);" json:"remarks"`          // 备注
}
```



### 3.3 菜单表（sys_menu）

数据表结构如下所示：

| 字段           | 类型     | 说明             |
| -------------- | -------- | ---------------- |
| id             | bigint   | 主键，自增ID     |
| created_at     | datetime | 创建时间         |
| updated_at     | datetime | 更新时间         |
| deleted_at     | datetime | 删除时间         |
| parent_id      | bigint   | 父级ID           |
| name           | varchar  | 菜单名称         |
| web_icon       | varchar  | 图标             |
| path           | varchar  | 路由地址         |
| sort           | int      | 排序             |
| level          | tinyint  | 等级             |
| component_name | varchar  | 对应前端组件路径 |

对应的数据模型如下所示：

```go
type SysMenu struct {
	gorm.Model
	ParentId      uint   `gorm:"column:parent_id;type:int(11);" json:"parent_id"`
	Name          string `gorm:"column:name;type:varchar(100);" json:"name"`
	WebIcon       string `gorm:"column:web_icon;type:varchar(100);" json:"web_icon"`
	Path          string `gorm:"column:path;type:varchar(255);" json:"path"`
	Sort          int    `gorm:"column:sort;type:int(11);default:0;" json:"sort"`                // 排序规则，默认升序，值越小越靠前
	Level         int    `gorm:"column:level;type:tinyint(1);default:0;" json:"level"`           // 菜单等级，{0：目录，1：菜单，2：按钮}
	ComponentName string `gorm:"column:component_name;type:varchar(100);" json:"component_name"` //组件名称
}
```



### 3.4 角色菜单关系表（sys_role_menu）

数据表结构如下所示：

| 字段       | 类型     | 说明         |
| ---------- | -------- | ------------ |
| id         | bigint   | 主键，自增ID |
| created_at | datetime | 创建时间     |
| updated_at | datetime | 更新时间     |
| deleted_at | datetime | 删除时间     |
| role_id    | bigint   | 角色ID       |
| menu_id    | bigint   | 菜单ID       |



### 3.5 日志表（sys_log）

数据表结构如下所示：

| 字段         | 类型     | 说明           |
| ------------ | -------- | -------------- |
| id           | bigint   | 主键，自增ID   |
| created_at   | datetime | 创建时间       |
| updated_at   | datetime | 更新时间       |
| deleted_at   | datetime | 删除时间       |
| browser      | varchar  | 浏览器信息     |
| class_method | varchar  | 请求类型.方法  |
| http_method  | varchar  | 操作方式       |
| params       | varchar  | 操作提交的数据 |
| remote_addr  | varchar  | 操作IP地址     |
| request_uri  | varchar  | 请求URI        |
| response     | text     | 返回内容       |
| status_code  | int      | 状态码         |
| use_time     | varchar  | 执行时间       |
| country      | varchar  | 地区国家       |
| region       | varchar  | 区域           |
| province     | varchar  | 省份           |
| city         | varchar  | 城市           |
| isp          | varchar  | 运营商         |

对应的数据模型如下所示：

```go
// SysLog 日志结构体
type SysLog struct {
	gorm.Model
	Browser     string `gorm:"column:browser;type:varchar(50)" json:"browser"`
	ClassMethod string `gorm:"column:class_method;type:varchar(255);" json:"class_method"`
	HttpMethod  string `gorm:"column:http_method;type:varchar(20)" json:"http_method"`
	Params      string `gorm:"column:params;type:varchar(255);" json:"params"`
	RemoteAddr  string `gorm:"column:remote_addr;type:varchar(255)" json:"remote_addr"`
	RequestUri  string `gorm:"column:request_uri;type:varchar(255)" json:"request_uri"`
	Response    string `gorm:"column:response;type:text(10000);" json:"response"`
	StatusCode  int    `gorm:"column:status_code;type:int(11)" json:"status_code"`
	UseTime     int64  `gorm:"column:use_time;type:varchar(255);" json:"use_time"`
	Country     string `gorm:"column:country;type:varchar(255);" json:"country"`   //国家
	Region      string `gorm:"column:region;type:varchar(255);" json:"region"`     // 区域
	Province    string `gorm:"column:province;type:varchar(255);" json:"province"` // 省份
	City        string `gorm:"column:city;type:varchar(255);" json:"city"`         // 城市
	Isp         string `gorm:"column:isp;type:varchar(255);" json:"isp"`           // 运营商
}
```

### 3.6 支付宝信息表（ali_pay，ali_pay_sandbox）

数据表结构如下所示：

| 字段           | 类型     | 说明                 |
| -------------- | -------- | -------------------- |
| id             | bigint   | 主键，自增ID         |
| created_at     | datetime | 创建时间             |
| updated_at     | datetime | 更新时间             |
| deleted_at     | datetime | 删除时间             |
| app_id         | varchar  | 支付宝开放平台APPID  |
| private_key    | varchar  | 支付宝私钥           |
| ali_public_key | varchar  | 支付宝公钥           |
| notify_url     | varchar  | 支付成功异步通知地址 |
| return_url     | varchar  | 支付成功跳转地址     |

对应的数据模型如下所示：

```go
type AliPay struct {
	gorm.Model
	AppID        string `gorm:"column:app_id;type:varchar(100);" json:"appId"`                 // 支付宝开放平台APPID
	PrivateKey   string `gorm:"column:private_key;type:varchar(5000);" json:"privateKey"`      // 支付宝私钥
	AliPublicKey string `gorm:"column:ali_public_key;type:varchar(5000);" json:"aliPublicKey"` // 支付宝公钥
	NotifyURL    string `gorm:"column:notify_url;type:varchar(1000);" json:"notifyURL"`        // 支付成功异步通知地址
	ReturnURL    string `gorm:"column:return_url;type:varchar(1000);" json:"returnURL"`        // 支付成功跳转地址
}

func (table *AliPay) TableName() string {
	return "ali_pay"
}
```

### 3.7 商品类目表（baby_category）

 [ˈkætəɡəri]

数据表结构如下所示：

| 字段       | 类型     | 说明          |
| ---------- | -------- | ------------- |
| id         | bigint   | 主键，自增 ID |
| created_at | datetime | 创建时间      |
| updated_at | datetime | 更新时间      |
| deleted_at | datetime | 删除时间      |
| firsts     | varchar  | 一级类目      |
| seconds    | varchar  | 二级类目      |

对应的数据模型如下所示：

```go
// BabyCategory 定义商品类别数据表结构
type BabyCategory struct {
	gorm.Model
	Firsts  string `json:"firsts" gorm:"type:varchar(255)"`
	Seconds string `json:"seconds" gorm:"type:varchar(255)"`
}

// TableName 设置数据库表名
func (table *BabyCategory) TableName() string {
	return "baby_category"
}
```

### 3.8 商品信息表（baby_goods）

数据表结构如下所示：

| 字段       | 类型     | 说明         |
| ---------- | -------- | ------------ |
| id         | bigint   | 主键，自增ID |
| created_at | datetime | 创建时间     |
| updated_at | datetime | 更新时间     |
| deleted_at | datetime | 删除时间     |
| name       | varchar  | 商品名称     |
| size       | varchar  | 商品规格     |
| types      | varchar  | 商品类目     |
| price      | double   | 商品价格     |
| discount   | double   | 活动价格     |
| stock      | bigint   | 库存         |
| sold       | bigint   | 销售数量     |
| likes      | bigint   | 收藏数量     |
| img        | varchar  | 商品封面     |
| details    | varchar  | 商品详情     |

对应的数据模型如下所示：

```go
// BabyGoods 商品信息结构体
type BabyGoods struct {
	gorm.Model
	Name     string  `json:"name" gorm:"type:varchar(255)"`
	Size     string  `json:"size" gorm:"type:varchar(255)"`
	Types    string  `json:"types" gorm:"type:varchar(255)"`
	Price    float64 `json:"price"`
	Discount float64 `json:"discount"`
	Stock    int64   `json:"stock"`
	Sold     int64   `json:"sold"`
	Likes    int64   `json:"likes"`
	Img      string  `json:"img" gorm:"type:varchar(255)"`
	Details  string  `json:"details" gorm:"type:varchar(255)"`
}

// TableName 设置数据库表名
func (table *BabyGoods) TableName() string {
	return "baby_goods"
}
```

### 3.9 购物车信息表（baby_carts）

数据表结构如下所示：

| 字段       | 类型     | 说明         |
| ---------- | -------- | ------------ |
| id         | bigint   | 主键，自增ID |
| created_at | datetime | 创建时间     |
| updated_at | datetime | 更新时间     |
| deleted_at | datetime | 删除时间     |
| quantity   | bigint   | 购买数量     |
| goods_id   | bigint   | 商品ID       |
| user_id    | bigint   | 购买者ID     |

对应的数据模型如下所示：

```go
type BabyCarts struct {
	gorm.Model
	Quantity uint      `json:"quantity"`
	GoodsId  uint      `json:"goodsId"`
	Goods    BabyGoods `gorm:"foreignkey:GoodsId"`
	UserId   uint      `json:"userId"`
	Users    SysUser   `json:"-" gorm:"foreignkey:UserId"`
}
// TableName 设置数据表名称
func (table *BabyCarts) TableName() string {
	return "baby_carts"
}
```

### 3.10 订单表（baby_orders）

数据表结构如下所示：

| 字段         | 类型     | 说明         |
| ------------ | -------- | ------------ |
| id           | bigint   | 主键，自增ID |
| created_at   | datetime | 创建时间     |
| updated_at   | datetime | 更新时间     |
| deleted_at   | datetime | 删除时间     |
| price        | double   | 商品价格     |
| order_number | varchar  | 订单编号     |
| user_id      | bigint   | 购买者ID     |
| state        | bigint   | 订单状态     |
| address      | varchar  | 收货地址     |

对应的数据模型如下所示：

```go
type BabyOrders struct {
	gorm.Model
	Price       float64 `json:"price" gorm:"type:float"`
	OrderNumber string  `json:"orderNumber" gorm:"type:varchar(255)"`
	UserId      uint
	Users       SysUser `json:"-" gorm:"foreignkey:UserId"`
	State       uint    `json:"state"`
	Address     string  `json:"address" gorm:"type:varchar(255)"`
}

func (table *BabyOrders) TableName() string {
	return "baby_orders"
}
```

### 3.11 订单明细表（baby_order_item）

数据表结构如下所示：

| 字段            | 类型     | 说明         |
| --------------- | -------- | ------------ |
| id              | bigint   | 主键，自增ID |
| created_at      | datetime | 创建时间     |
| updated_at      | datetime | 更新时间     |
| deleted_at      | datetime | 删除时间     |
| order_id        | bigint   | 订单ID       |
| goods_id        | bigint   | 商品ID       |
| goods_name      | varchar  | 商品名称     |
| goods_cover_img | varchar  | 商品封面     |
| selling_price   | double   | 商品价格     |
| goods_count     | int      | 商品数量     |

对应的数据模型如下所示：

```go
type BabyOrderItem struct {
	gorm.Model
	OrderId       uint    `json:"orderId" gorm:"type:int(11)"`
	GoodsId       uint    `json:"goodsId" gorm:"type:int(11)"`
	GoodsName     string  `json:"goodsName" gorm:"type:varchar(255)"`
	GoodsCoverImg string  `json:"goodsCoverImg" gorm:"type:varchar(255)"`
	SellingPrice  float64 `json:"sellingPrice" gorm:"type:float"`
	GoodsCount    uint    `json:"goodsCount" gorm:"type:int(11)"`
}

func (table *BabyOrderItem) TableName() string {
	return "baby_order_item"
}
```

```go
// 自动建表
	err = db.AutoMigrate(
		&SysUser{},
		&SysRole{},
		&SysMenu{},
		&RoleMenu{},
		&SysLog{},
		&AliPaySandbox{},
		&AliPay{},
		&BabyCarts{},
		&BabyCategory{},
		&BabyOrders{},
		&BabyGoods{},
		&BabyOrderItem{})
```



