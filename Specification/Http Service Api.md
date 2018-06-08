# Version
|版本号|更新时间 | 更新内容 |
|:----:|:----:|:----|
| v1.2.1 | 20180608 | 完善接口编号规范增加公共接口类型；修改json返回数据属性值数据类型；完善URI命名规范；增加uri格式规范；废弃接口编号|
| v1.2.0 | 20171220 | 增加接口编号规范 |
| v1.1.0 | 20171220 | 增加导出操作，增加版本 |
| v1.0.0 | 20171220 | 新建 |

# 接口编号规范@Deprecated
## 编号格式 TXXYY
- T 接口类型， 占1位，包括：F：应用前台，B：管理后台，M：移动端，L：小程序，C：公用接口
- XX 功能编号，占2位，01-99（功能对应原型中的每一个页面）
- YY 操作编号，占2位，01-99（操作对应原型中每一个页面中的操作）

## 编号举例
1. 后台管理页面具添加好友与查询好友信息功能，这个页面中的编号如下
- 假设管理页面功能编号01
- 添加好友接口编号B0101,而查询好友接口编号B0102
2. 移动端用户注册功能，包括提交注册信息和获取手机验证码功能
- 假设移动端注册页面功能编号03
- 提交注册信息接口编号M0301,获取手机验证码的接口编号M0302

## 原型标注
- 服务端设计人员负责标注
- 普通接口标注在原型对应的功能按钮上
- 服务类接口标注在对应页面上

# URI
- URI 表示资源，资源一般对应服务器端领域模型中的业务实体对象。URI指向的是唯一的业务实体对象。

## 命名规范
- 参数列表要encode，默认编码方式UTF-8
- 查询参数变量命名采用驼峰格式，且首字母小写
- 响应数据中变量命名采用驼峰格式，其首字母小写
- 实体名称使用英文名词单数形式（不同于restful规范，使用名词复数表示资源），与handler(controller)对应

## URI格式规范
- /api/v${version}/${group}/${function}/${opt}/${id}

### verson
- uri接口版本，合法的格式x.y。比如1.0

### group
- 功能组名称，可选，对应代码中的类目录（文件夹）或分组【Controller所在文件夹或分组】

### function
- 功能或实体名称，必填，对应代码中的类名【controller类对应名称】。

### opt
- 操作名称，必填，对应代码类中的方法名【controller类中的具体方法名】
- add(create),update,save,list(retrieve),del(delete),export为保留字，不可在url中用于表达其它业务语义

### id
- 用于表达业务实体id

### 举例
- 有uri为：/api/v1.0/system/user/list。
1. php代码对应关系Controller/System/UserController.php文件，UserController中方法名为list
2. java代码对应关系controller.system.UserController.java文件，UserController中方法名为list

## 原型标注
- 将uri标注在原型上
- 服务端设计人员负责标注
- 普通接口标注在原型对应的功能按钮上
- 服务类接口标注在对应页面上

# 请求（Request）
## Http 方法和特性
| 方法        | 功能                       |
|:-----------:|:--------------------------:|
| GET         | 查找获取单个对象或集合     |
| POST        | 增强查询和其它所有操作     |

## Http 方法举例
- GET /company/list: 获取公司实体集合 List all company (ID and Name, not too much detail)
- POST /company/list: 获取公司实体集合,使用body传递参数（格式为json） List all company (ID and Name, not too much detail)【条件复杂的查询】
- POST /company/add: 创建一个公司实体 Create a new Company
- GET /company/list/{comid}: 获取单个公司实体 Retrieve an entire Company object
- POST /company/list/{comid}: 获取单个公司实体 Retrieve an entire Company object【可选】
- POST /company/update/{comid}: 更新公司实体 Update a Company (entire object)
- POST /company/save
- POST /company/del/{comid}: 删除公司实体 Delete a Company
- GET /company/export/{comid}: 导出公司信息列表content中为文件下载url

## 复杂查询
|          | 示例                        | 备注 |
|:--------:|:---------------------------:|:----:|
| 过滤条件  | ?type=1&status=0&... |    |
| 排序  | ?sort=age-desc,name-asc,... | 复合语义值使用"减号"连接 |
| 投影 | ?whitelist=id,name,...   | 可选 |
| 分页 | ?page=1&size=20 |  |

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
- json（默认）：/company/list/1（.json可选"/company/list/1.json"亦可）

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
- 分页信息根据具体框架格式定
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
| POST   | 成功创建、更新的实体 |

## json格式约定
- 传递所有必要的属性字段，null值传递null
- 属性值只使用String，除null

# 版本（Version）
-/api/v{main-version}

# 参考（Reference）
- [RESTful Best Practices](https://segmentfault.com/a/1190000002949234)
- [Restful API 的设计规范](
http://novoland.github.io/%E8%AE%BE%E8%AE%A1/2015/08/17/Restful%20API%20%E7%9A%84%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83.html?utm_source=tuicool&utm_medium=referral)
- [Principles of good RESTful API Design](https://codeplanet.io/principles-good-restful-api-design/)
- [HTTP状态码](http://baike.baidu.com/link?url=wlRdinTy-Zq_2ZGJNWPwABqblA2V2TnbNBr4sOSoF1-hzFRsdZe37VVhBWTPdDnw8IDVgGFmslSX0zoqYA5SQlnWdCbMXY4CzxgSsodCw_c6EpoteGLfr9IhKvcr51B_)
