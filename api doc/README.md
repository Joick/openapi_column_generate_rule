# 开放平台接口（示例）

- [接口说明]
	- [公共请求/响应]
	- [请求获取授权码 Authorize Code]
	- [获取令牌 Access Token]
	- [查询会员授权信息]
- [验签生成规则（考虑完善中）]
- [公共响应码说明]
- [数据字典]

## 接口说明

1. 在使用一下开放接口之前，需要在开放平台，填写注册开发者账号，获取应用唯一标识（app\_code）；
2. 在开放接口功能列表中添加相应的接口功能后，即可使用开放接口功能

### 公共请求/响应

**公共请求域名**
>http://openapi.testPlatform.com


**请求公共参数（在header中传输）**

每次请求均需要在Header中添加对应的参数信息，用于开放平台验证每次的请求是否合法及有效性

| 参数 | 属性 | 是否必填 | 说明 |
| --- | --- | --- | --- |
| app\_code | string | 是 | 注册成功后拥有的标识码 |
| sign\_data | string | 是 | 验签数据（生成规则请见验签数据生成规则） |
| timestamp | string | 是 | 时间戳（10位精确到秒） |

**响应公共参数**

| 参数 | 属性 | 说明 |
| --- | --- | --- |
| code | string | 响应状态码 |
| msg | string | 响应描述 |
| result | object | 响应数据（具体数据格式请看各接口响应数据格式） |



## 请求获取授权码 Authorize Code

说明

平台在使用账号关联授权使用该接口，主要获取会员的唯一标识id（user\_id）和授权令牌（access\_token），便于其他平台处理自身业务使用。例如：获取会员的基本信息、授权信息、快捷登录等。

注明：

第三方请求获取授权码验证通过进入业务逻辑时，判断请求的授权信息是否属于会员个人信息，或是属于平台数据信息。

- 若是请求授权获取会员信息，则跳转用户登录页面，待用户登录成功后检查授权情况，再处理后续务；
- 若是请求授权获取平台数据信息时，不使用用户登录步骤，直接验证授权情况，再处理后续业务。

**请求地址**

	GET：/auth/authorize

**请求参数**

| 参数 | 属性 | 是否必填 | 说明 | 示例 |
| --- | --- | --- | --- | --- |
| redirect\_uri | string | 是 | 回调地址 | http://www.example.com/callback |
| scope | string | 是 | 请求获取权限，需要获取多个权限使用多个值，用英文逗号分隔（详情见数据字典） | base\_info |

**请求示例:**
```
http://open.testPlatform.com/auth/authorize?scope=base_Info&redirect_uri=http%3a%2f%2fwww.example.com%2fcallback
```

**响应参数说明**

| 参数 | 属性 | 说明 | 示例 |
| --- | --- | --- | --- |
| app\_code | string | 获取授权码的平台标识码 | hr78hif9q84t94t9 |
| authorize\_code | string | 授权码 | X1da4d9g3W4FG3 |
| expire\_time | string | 授权码有效期（单位：秒） | 300 |
| scope | string | 请求授权权限 | base\_Info |
| error\_info | string | 处理错误响应说明（仅在处理失败时数据有效） |   |

**响应示例**
```
http://www.example.com/callback?app_code=hr78hif9q84t94t9&authorize_code=X1da4d9g3W4FG3&scope=base_Info&expire_time=300
```

## 获取令牌 Access Token

**请求地址**

	GET：/auth/oauthToken

**请求参数**

| 参数 | 属性 | 是否必填 | 说明 | 示例 |
| --- | --- | --- | --- | --- |
| authorize\_code | string | 是 | 授权码（获取方式请查看 请求获取授权码Authorize Code） | d9a8b72e60d2b1114f65c30dedb843ee |

**请求示例**

``` 
{
    "authorize_code": "d9a8b72e60d2b1114f65c30dedb843ee"
}
```

**响应参数**

| 参数 | 属性 | 说明 | 示例 |
| --- | --- | --- | --- |
| user\_id | string | 用户在开放平台的用户id | 1000001 |
| access\_token | string | 请求令牌 | 9ba6b21d9141b83e4a87c292f8b63500 |
| expire\_time | string | 令牌有效期（单位：秒） | 2592000‬ |

**响应示例**

```
{
	"code": "10000",
	"msg": "Success",
	"result": {
		"user_id": "1000001",
		"access_token": "9ba6b21d9141b83e4a87c292f8b63500",
		"expire_time": "2592000"
	}
}
```

**异常示例**

```
{
	"code": "30004",
	"msg": "Invalid authorization code",
	"result": null
}
```


## 查询会员授权信息

**请求地址**

	GET: /user/userShareInfo

**请求参数**

| 参数 | 属性 | 是否必填 | 说明 | 示例 |
| --- | --- | --- | --- | --- |
| access\_token | string | 是 | 请求令牌（获取方式请访问获取令牌 Access Token） | 9ba6b21d9141b83e4a87c292f8b63500 |
| user\_id | string | 是 | 用户在网的标识id | 1000001 |
| scope | string | 是 | 申请获取的权限信息 | base\_info |

**请求示例**
```
http://openapi.testPlatform.com/auth/userInfoShare?access_token=9ba6b21d9141b83e4a87c292f8b63500&user_id=1000001&scope=base_info
```

**响应参数**

| 参数 | 属性 | 说明 | 示例 |
| --- | --- | --- | --- |
| user\_id | string | 用户在开放平台的标识id | 1000001 |
| nick\_name | string | 用户在开放平台的昵称 | 汇图小二 |
| avatar | string | 用户在开放平台使用的头像图片地址 | http://www.testPlatform.com/avatar/20003429.gif |
| is\_certified | string | 是否已实名认证T-已认证，F-未认证 | T |
| gender | string | 性别（仅在is\_certified值为T时有效） | M |

**响应示例**

```
{
	"code": "10000",
	"msg": "Success",
	"result": {
		"user_id": "1000001",
		"nick_name": "客服小二",
		"avatar": "http://www.testPlatform.com/avatar/20003429.gif",
		"is_certified": "T",
		"gender": "M"
	}
}
```

**异常示例**

```
{
	"code": "30005",
	"msg": "Access token has expired",
	"result": null
}
```



## 验签生成规则（考虑完善中）

将请求数据包中请求参数正序排序后，以"参数1参数值1参数2参数值2…."的格式拼接成字符串，之间不使用符号（如遇到参数值为对象或数组的，则转成json格式，保留数据符号），在数据末尾加上时间戳和Secret Key（在开放平台注册时生成） 的值。对字符串进行MD5加密，32位

示例
```
请求数据
http://openapi.testPlatform.com/auth/authorize?scope=base_Info&redirect_uri=http%3a%2f%2fexample.com%2fcallback

sign_data的生成规则如下

将请求参数正序排序
redirect_urihttp://example.com/callbackscopebase_Info

行尾拼接时间戳Secret Keyredirect_urihttp://example.com/callbackscopebase_Info15608235138dsh4mgkxnxf20sk7ksle7w3

使用MD5加密输出（32位）87ccb60ccc105711065722cb098d21e6

之后的请求将sign_data数据写入请求的header中发送请求

```

（或者可以考虑使用接入的平台在开放平台注册时生成的密钥（公钥）进行加密，在接口拦截器处解密验证数据）

## 公共响应码说明

| code（响应状态码） | msg（响应状态描述） | 说明 |
| --- | --- | --- |
| 10000 | Success | 请求响应成功 |
| 20000 | Unknow error | 服务暂时不可用 |
| 30001 | Invalid client code | 平台标识码有误 |
| 30002 | Invalid secret key | 平台密钥有误 |
| 30003 | Invalid signature data | 验签数据有误 |
| 30004 | Invalid authorization code | 授权码有误 |
| 30005 | Access token has expired | 令牌已过期，请重新获取Access Token |
| 40001 | Insufficient permissions | 权限不足 |

## 数据字典 dictionary

<table>
<tr><td>参数</td><td>可选内容</td><td>说明</td></tr>
<tr><td rowspan="3">scope</td><td>base_info</td><td>会员基本信息（头像昵称等）</td></tr>
<tr><td>location_info</td><td>会员地址信息</td></tr>
<tr><td>certified_info</td><td>会员认证信息</td></tr>
</table>