# URL
URI 表示资源，资源一般对应服务器端领域模型中的实体类。URI指向的是唯一的资源对象。
## URI命名规范
- 全部使用小写字母
- 使用减号（中杠）连接单词
- 参数列表要encode，默认编码方式UTF-8
- 名词表示资源集合，使用复数形式。

# 请求（Request）
## Http 方法和特性
| 方法        | 功能                       | 安全性  | 等幂性 |
|:-----------:|:--------------------------:|:-------:|-------:|
| GET         | 查找获取单个对象或集合     | R       | R      |
| POST        | 新增创建对象               | X       | X      |
| PUT         | 客户端提供完整对象进行更新 | X       | R      |
| PATCH       | 客户端提供部分属性更新对象 | X       | R      |
| DELETE      | 删除对象                   | X       | R      |

1. 安全性：不会改变资源状态，可以理解为只读的；
2. 幂等性：执行1次和执行N次，对资源状态改变的效果是等价的。

## Http 方法举例
- GET /companies: 获取zoo实体集合 List all Companies (ID and Name, not too much detail)
- POST /companies: 创建一个公司实体 Create a new Company
- GET /companies/{comid}: 获取单个公司实体 Retrieve an entire Company object
- PUT /companies/{comid}: 更新整个公司实体 Update a Company (entire object)
- PATCH /companies/{comid}: 更新部分公司实体 Update a Company (partial object)
- DELETE /companies/{comid}: 删除公司实体 Delete a Company
- GET /companies/{comid}/customers: 获取公司服务的客户集合 Retrieve a listing of Customers (ID and Name).
- GET /customers: 获取客户实体集合 List all Customers (ID and Name).
- POST /customers: 创建一个新客户 Create a new Customer
- GET /customers/{cusid}: 获取单个客户实体 Retrieve an Customer object
- PUT /customers/{cusid}: 更新整个客户实体 Update an Customer (entire object)
- PATCH /customers/{cusid}: 更新部分客户实体 Update an Customer (partial object)
- DELETE /customers/{cusid}: 删除客户实体 Delete a Customer
- GET /customers/{cusid}/companies: 获取为客户提供服务的公司集合 Retrieve a listing of Companies (ID and Name).

## 复杂查询
|          | 示例                        |
|:--------:|:---------------------------:|
| 过滤条件 | ?type=1&status=0&...        |
| 排序     | ?sort=age-desc,name-asc,... |
| 投影     | ?whitelist=id,name,...      |
| 分页     | ?page=1&size=20             |

## Body 格式

- Content-Type: application/json
```json
{
    "companyId": "1",
    "companyName": "动视暴雪",
    "deleteFlag": "0" 
}
```
- Content-Type: application/x-www-form-urlencoded (浏览器POST表单用的格式)
- Content-Type: multipart/form-data; boundary=—-RANDOM_jDMUxq4Ot5 (表单有文件上传时的格式)

## uri响应格式后缀
- json（默认）：/company/1.json

# 应答（Response）

## 业务响应码
| 响应码 | 消息模版 | 应用场景 |
|:------:|:--------:|:--------:|
| 0      | success  | 执行成功 |

## http响应码
- 参考[HTTP状态码](http://baike.baidu.com/link?url=wlRdinTy-Zq_2ZGJNWPwABqblA2V2TnbNBr4sOSoF1-hzFRsdZe37VVhBWTPdDnw8IDVgGFmslSX0zoqYA5SQlnWdCbMXY4CzxgSsodCw_c6EpoteGLfr9IhKvcr51B_)

## 返回数据
- 单实体查询
```json
{
    "rc": "0",
    "msg": "success",
    "data": {
        "companyId": "1",
        "companyName": "动视暴雪",
        "deleteFlag": "0" 
    }
}
```
- 多实体查询
```json
{
    "rc": "0",
    "msg": "success",
    "data": [
        {
            "companyId": "1",
            "companyName": "动视暴雪",
            "deleteFlag": "0" 
        },
        {
            "companyId": "2",
            "companyName": "Capcom",
            "deleteFlag": "0" 
        }
    ]
}
```
- 多实体分页查询(分页采用spring标准Page<T>)
```json
{
    "rc": "0",
    "msg": "success",
    "data":{
        "content": [
            {
                "companyId": "1",
                "companyName": "动视暴雪",
                "deleteFlag": "0" 
            },
            {
                "companyId": "2",
                "companyName": "Capcom",
                "deleteFlag": "0" 
            },
            {
                "companyId": "3",
                "companyName": "2K Games",
                "deleteFlag": "0" 
            }
        ],
        "last": true,
        "totalPages": 1,
        "totalElements": 3,
        "first": true,
        "numberOfElements": 3,
        "size": 20,
        "number": 0
    }
}
```

## 返回数据"data"值的约定
| 方法   | 约定               |
|:------:|:------------------:|
| GET    | 单个实体或实体集合 |
| POST   | 成功创建的实体     |
| PUT    | 成功更新的实体     |
| PATCH  | 成功更新的实体     |
| DELETE | 空                 |

## json格式约定
- 不传Null字段

# 版本（Version）
-/api/v{main-version}

# Refernce
- [RESTful Best Practices](https://segmentfault.com/a/1190000002949234)
- [Restful API 的设计规范](
http://novoland.github.io/%E8%AE%BE%E8%AE%A1/2015/08/17/Restful%20API%20%E7%9A%84%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83.html?utm_source=tuicool&utm_medium=referral)
- [Principles of good RESTful API Design](https://codeplanet.io/principles-good-restful-api-design/)
- [HTTP状态码](http://baike.baidu.com/link?url=wlRdinTy-Zq_2ZGJNWPwABqblA2V2TnbNBr4sOSoF1-hzFRsdZe37VVhBWTPdDnw8IDVgGFmslSX0zoqYA5SQlnWdCbMXY4CzxgSsodCw_c6EpoteGLfr9IhKvcr51B_)
