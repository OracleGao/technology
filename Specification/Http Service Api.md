# URI
URI 表示资源，资源一般对应服务器端领域模型中的实体类。URI指向的是唯一的资源对象。
## 命名规范
- 参数列表要encode，默认编码方式UTF-8
- 查询参数变量命名采用驼峰格式，且首字母小写
- 响应数据中变量命名采用驼峰格式，其首字母小写

# 请求（Request）
## Http 方法和特性
| 方法        | 功能                       |
|:-----------:|:--------------------------:|
| GET         | 查找获取单个对象或集合     |
| POST        | 增强查询和其它所有操作     |

## Http 方法举例
- GET /companies/fetch: 获取公司实体集合 List all Companies (ID and Name, not too much detail)
- POST /companies/fetch: 获取公司实体集合,使用body传递参数（格式为json） List all Companies (ID and Name, not too much detail)【条件复杂的查询】
- POST /companies/create: 创建一个公司实体 Create a new Company
- GET /companies/fetch/{comid}: 获取单个公司实体 Retrieve an entire Company object
- POST /companies/fetch/{comid}: 获取单个公司实体 Retrieve an entire Company object【可选】
- POST /companies/update/{comid}: 更新整个公司实体 Update a Company (entire object)
- POST /companies/delete/{comid}: 删除公司实体 Delete a Company

## 复杂查询
|          | 示例                        |  备注  |
|:--------:|:---------------------------:|:----:|
| 过滤条件  | ?type=1&status=0&...         |          |
| 排序     | ?sort=age-desc,name-asc,... | 复合语义值使用"减号"连接 |
| 投影     | ?whitelist=id,name,...      | 可选                  |
| 分页     | ?page=1&size=20             |                       |

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
- json（默认）：/companies/fetch/1.json（可选）

# 应答（Response）

## 业务响应码
| 响应码 | 消息模版             | 应用场景 |
|:------:|:--------------------:|:--------:|
| 0      | success              | 执行成功 |
| 1      | ${EXCEPTION_MESSAGE} | 执行失败 |
| 2      |                      |          |

## http响应码
- 参考[HTTP状态码](http://baike.baidu.com/link?url=wlRdinTy-Zq_2ZGJNWPwABqblA2V2TnbNBr4sOSoF1-hzFRsdZe37VVhBWTPdDnw8IDVgGFmslSX0zoqYA5SQlnWdCbMXY4CzxgSsodCw_c6EpoteGLfr9IhKvcr51B_)

## 返回业务数据
- 单实体查询
```json
{
    "code": "0",
    "message": "success",
    "content": {
        "companyId": "1",
        "companyName": "动视暴雪",
        "deleteFlag": "0" 
    }
}
```
- 多实体查询
```json
{
    "code": "0",
    "message": "success",
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
        }
    ]
}
```
- 多实体分页查询（分页采用spring标准Page<T>）
```json
{
    "code": "0",
    "message": "success",
    "content":{
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

## 应答数据格式
```json
{
    "code": "0",
    "message": "success",
    "content": {} | []
}
```

## 返回数据"content"值的约定
| 方法   | 约定               |
|:------:|:------------------:|
| GET    | 单个实体或实体集合 |
| POST   | 成功创建、更新的实体 |

## json格式约定
- 不传Null字段
- 属性值均使用String格式

# 版本（Version）
-/api/v{main-version}

# 参考（Reference）
- [RESTful Best Practices](https://segmentfault.com/a/1190000002949234)
- [Restful API 的设计规范](
http://novoland.github.io/%E8%AE%BE%E8%AE%A1/2015/08/17/Restful%20API%20%E7%9A%84%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83.html?utm_source=tuicool&utm_medium=referral)
- [Principles of good RESTful API Design](https://codeplanet.io/principles-good-restful-api-design/)
- [HTTP状态码](http://baike.baidu.com/link?url=wlRdinTy-Zq_2ZGJNWPwABqblA2V2TnbNBr4sOSoF1-hzFRsdZe37VVhBWTPdDnw8IDVgGFmslSX0zoqYA5SQlnWdCbMXY4CzxgSsodCw_c6EpoteGLfr9IhKvcr51B_)
