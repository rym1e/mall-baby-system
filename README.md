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


