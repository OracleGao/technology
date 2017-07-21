# 框架分层
- Controller层
- Service层
- Dao 层

## Controller层
### Controller层开发原则
- 定义入口、参数、出口和返回值
- 与业务逻辑无关
- 与事物无关
- 处理参数
- 调用Service层实例完成业务处理
- 处理返回结果

### 不应出现的情况
- 处理业务逻辑
- 处理事务
- 循环调用Service层方法

## Service层
### Service层开发原则
- 处理业务逻辑
- 整合业务实例
- 保证事务特性
- 调用数据访问层实例完成业务功能，并处理持久化
- 将业务处理结果返回给Controller层
- 对Dao层返回的数据结果进行计算和处理
- 对于关系数据库，尽量将关联查询放在Dao层处理，Service层按照业务逻辑做数据组装。

### 不应出现的情况
- 直接访问数据库
- 循环调用相同逻辑的Dao层实例（批量插入操作除外）

## Dao层（Data Access Object）
### Dao层开发原则
- 完成数据库层面的业务逻辑处理，以关联查询操作为主
- 完成数据的CRUD（增查更删）操作
- 查询时尽量减少数据量在做关联
- 查询时尽量使用索引进行检索

### Dao层不应出现的情况
- sql语句的where条件出现函数或计算
- sql语句出现无法使用索引的情况

# api异常处理
- 框架所有层不做异常处理，只抛出异常。除非需要对异常情况特殊处理，处理结束后依然要将异常抛出。
- 与业务逻辑有关的异常要统一使用com.fxtx.service.exception.ServiceBusinessException类抛出异常
```java
    public AdminUser login(AdminUser adminUser) {
        AdminUser adminUserTmp = this.getEntity("getByUserName", adminUser.getUserName());
        if (adminUserTmp == null) {
            // throw new Exception("用户名或密码错误");
        	throw new ServiceBusinessException("101", "用户名或密码错误");
        }
        if (AdminUser.Status.lock.code.equals(adminUserTmp.getStatus())) {
        	// throw new Exception("此账户已被锁定");
        	throw new ServiceBusinessException("102", "此账户已被锁定");
        }

        AdminUser adminUser1 = new AdminUser();
        adminUser1.setPassword(adminUser.getPassword());
        adminUser1.setSalt(adminUserTmp.getSalt());
        if (!adminUserTmp.getPassword().equals(encrypt(adminUser1))) {
            //throw new Exception("用户名或密码错误");
        	throw new ServiceBusinessException("103", "此账户已被锁定");
        }
        AdminUser updateAdminUser = new AdminUser();
        updateAdminUser.setId(adminUserTmp.getId());
        updateAdminUser.setLastLoginTime(new Date());
        updateAdminUser.setLastLoginIp(adminUser.getLastLoginIp());
        updateAdminUser.setLoginCount(adminUserTmp.getLoginCount() + 1);
        this.updateBySelective(updateAdminUser);
        return adminUserTmp;
    }
```
