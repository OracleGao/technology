# URI
URI 表示资源，资源一般对应服务器端领域模型中的实体类。URI指向的是唯一的资源对象。
## URI命名规范
- 复合单词是用驼峰格式
- 参数列表要encode，默认编码方式UTF-8
- 名词表示资源集合，使用复数形式。

# 请求（Request）
## Http 方法和特性
| 方法        | 功能                      | 安全性  | 等幂性 |
|:-----------:|:------------------------:|:-------:|-------:|
| GET         | 查找获取单个对象或集合     | R       | R      |
| POST        | 新增创建对象              | X       | X      |
| PUT         | 客户端提供对象进行全量更新 | X       | R      |
| PATCH       | 客户端提供对象进行部分更新 | X       | R      |
| DELETE      | 删除对象                 | X       | X      |

1. 安全性：不会改变资源状态，可以理解为只读的；
2. 幂等性：执行1次和执行N次，对资源状态改变的效果是等价的。

## Consumes
- application/json
- application/x-www-form-urlencoded (浏览器POST表单用的格式)
- multipart/form-data; (表单有文件上传时的格式)

## URI举例
- GET /companies/list: 分页获取公司实体集合
- GET /companies/listall: 获取公司实体集合
- POST /companies: 创建一个公司实体 Create a new Company
- GET /companies/{comid}: 获取单个公司实体 Retrieve an entire Company object
- PUT /companies/{comid}: 全量更新整个公司实体 Update a Company (entire object)（全量更新，没有传递的参数更新为Null）
- PATCH /companies/{comid}: 部分更新公司实体（根据参数更新，没有传递或null的参数不进行属性更新）
- DELETE /companies/{comid}: 删除公司实体 Delete a Company
- GET /companies/{comid}/customers/list: 分页获取公司服务的客户集合.
- GET /companies/{comid}/customers/listall: 获取公司服务的客户集合 Retrieve a listing of Customers (ID and Name).
- GET /customers/list: 分页获取客户实体集合.
- GET /customers/listall: 获取客户实体集合 List all Customers (ID and Name).
- POST /customers: 创建一个新客户 Create a new Customer
- GET /customers/{cusid}: 获取单个客户实体 Retrieve an Customer object
- PUT /customers/{cusid}: 全量更新整个客户实体 Update an Customer (entire object)
- PATCH /customers/{cusid}: 部分更新整个客户实体 Update an Customer
- DELETE /customers/{cusid}: 删除客户实体 Delete a Customer
- GET /customers/{cusid}/companies/list: 分页获取为客户提供服务的公司集合.
- GET /customers/{cusid}/companies/listall: 获取为客户提供服务的公司集合 Retrieve a listing of Companies (ID and Name).

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

## 实体模型表达
- 使用uri的资源和资源id关系表达关系数据模型
- 返回的数据从uri左侧资源属性展开
- 修改数据属性操作只针对uri的资源为单一结果（非集合）
- 获取分页集合的uri使用list(单数)结尾
- 获取全部集合的uri使用listall(单数)结尾
- 建立或解除关联关系的uri使用relation(单数)结尾
- 使用【CRUD】（Create，Read，Update，Delete）表示可用操作
- 返回单一对象的情况是对对象的处理，返回集合的情况是做关联操作
- uri去除最左一层数据，从第二层开始展开数据，第二层仅包含第一层的id关系

### 一对一
- /实体1/实体2/{实体1id}，操作实体2的数据【CRUD】
- /实体2/实体1/{实体2id}，操作实体1的数据【CRUD】

#### 举例
- 学生信息(std)和学生附加信息(stdattach)两个实体形成一对一的关系
- 获取学生信息的uri设计为：/stdattachs/stds/{said}，操作学生信息【CRUD】
- 获取或修改附加信息的uri设计为：/stds/stdattachs/{stdid}，操作学生附加信息【CRUD】

### 一对多
- 一对多：/一端实体/{一端id}/多端实体/{多端id}， 返回的数据为一端实体集合
- 一对一：/一端实体/多端实体/{一端id}， 返回的数据只包含多端实体

#### 举例
- 班级(cls)和学生(std)两个实体形成一对多的关系：一个班级有多个学生，而一个学生只能属于一个班级
- 获取或更改学生信息设计：/clses/{clsid}/stds/{stdid}， 返回学生【CRUD】
- 获取学生信息集合设计：/clses/{clsid}/stds/list，返回学生集合【R】
- 获取或修改班级信息设计：/stds/clses/{stdid}，返回班级【R】
- 把学生移除班级：/stds/clses/{stdid}/relation【D】
- 班级分配学生:/clses/{clsid}/stds/relation【CUD】

### 多对一
- 同一对多

### 多对多
- 拆分成两个一对多的关系（不包扩一对多关系中的一对一关系），只处理关联，不处理属性，属性使用单一对象操作
- 多对多映射：/实体1/实体2， 返回的数据实体1关联实体2 或 /实体2/实体1，返回的数据实体2关联实体1
####
- 学生(std)和课程(cur)两个实体形成多对多的关系
- 获取某一学生的课程集合(学生课程一对多关系)：/stds/{stdid}/curs/list【R】
- 获取某一课程的所有学生(课程学生一对多关系)：/curs/{curid}/stds/list【R】
- 操作某一学生的某个课程：/stds/{stdid}/curs/{curid}【CURD】
- 操作莫一课程的某个学生：/curs/{curid}/stds/{stdud}【CURD】
- 学生选课/stds/{stdid}/curs/relation【CUD】
- 课程分配学生/curs/{cusid}/stds/relation【CUD】
- 学生统一分配课程/curs/stds/relation【CUD】

### 关系传递
- 关系传递按照对应关系进行传递
- 传递关系只针对获取操作，不支持修改操作

#### 举例
- 用户(user)，角色(role)，权限(permission)三者分别为用户角色多对多，角色权限多对多
- 获取用户的角色权限集合设计: /users/{userid}/roles/permissions/listall
- 获取所有用户的所有角色权限集合：/users/roles/permissions/listall
- 获取拥有某种权限的角色和用户:/permissions/{permissionid}/roles/users/listall
- 获取拥有权限的所有角色和用户：/permissions/roles/users/listall

## uri响应格式后缀
- json（默认）：/companies/1.json（.json可选）

# 应答（Response）

## Produces
- application/json
- application/x-download 文件下载

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
- 多实体分页查询
```json
{
    "code": "0",
    "message": "success",
    "content":{
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
            },
            {
                "companyId": "3",
                "companyName": "2K Games",
                "deleteFlag": "0" 
            }
        ],
        "currentPage": 0,
        "perPage": 0,
        "total": 0,
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
- 入参允许部分字段，使用对象方式，允许Null
- 出参返回全部字段，用Null表示空对象

# 版本（Version）
-/api/v{main-version}

# 参考（Reference）
- [RESTful Best Practices](https://segmentfault.com/a/1190000002949234)
- [Restful API 的设计规范](
http://novoland.github.io/%E8%AE%BE%E8%AE%A1/2015/08/17/Restful%20API%20%E7%9A%84%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83.html?utm_source=tuicool&utm_medium=referral)
- [Principles of good RESTful API Design](https://codeplanet.io/principles-good-restful-api-design/)
- [HTTP状态码](http://baike.baidu.com/link?url=wlRdinTy-Zq_2ZGJNWPwABqblA2V2TnbNBr4sOSoF1-hzFRsdZe37VVhBWTPdDnw8IDVgGFmslSX0zoqYA5SQlnWdCbMXY4CzxgSsodCw_c6EpoteGLfr9IhKvcr51B_)
