# 代码修改和提交流程规范
1. 拉取指定分支代码
```shell
git clone https://github.com/OracleGao/technology.git -b master
```

2. 完成代码修改

3. 按文件比较代码变化，确认要提交的代码
```shell
git diff <file1>
git add <file2>
git diff <file2>
git add <file2>
...
```

4. 提交本地代码
```shell
git commit -m "<desc>"
```

5. 合并远程主干分支代码
```shell
git merge origin/master
```

6. 如果有冲突解决冲突
```shell
git status
git diff
git log
...
```

7. 提交本地分支到远程（分支命名参见远程分支命名规范）
```shell
git push origin dev-test:dev-test 
```

8. 发起pull request
 1. 填写pull request名称和必要的说明，选择代码review和check人
 2. 比较确认要合并的代码first check
 3. 创建pull request并通知code reviewer

9. code revicewer 检查代码如果符合规范，则合并代码 double check

10. 如果不符合规范，关闭pull request，通知提交人重新修改并发起新的pull request

# 远程分支命名规范
```text
<基线分支名>_<姓名汉语拼音缩写（英文名亦可）>-<分支编号>
```
## 举例
目前有dev-p2基线分支，现创建一个分支。合法分支名称： 
```text
dev-p2_gjz-001
dev-p2_gjz-refactor-001
dev-p2_gjz-bug-001
dev-p2_gjz-bug-002
```

# git常用指令
1. 拉取默认分支代码
```shell
git clone https://github.com/OracleGao/technology.git
```

2. 拉取指定分支代码
```shell
git clone  https://github.com/OracleGao/technology.git -b master
```

3. 查看分支
```shell
git branch
```

4. 查看所有分支
```shell
git branch -a
```

5. 拉取并且换远程分支
```shell
git checkout -b master origin/master
```

6. 切换分支
```shell
git checkout dev-p2
```

7. 基于当前分支创建新分支
```shell
git checkout -b dev-test
```

8. 提交分支到远程
```shell
git push origin dev-test:dev-test 
```

9. 合并远程分支
```shell
git merge origin/master
```
10. 查看代码修改状态
```shell
git status
```

11. 比较代码
```shell
git diff app/Http/Controllers/CustomerAppService/CashFlowController.php
```

12. 回滚代码修改
```shell
git checkout app/Http/Controllers/CustomerAppService/CashFlowController.php
```

13. 回滚所有代码修改
```shell
git checkout .
```

14. 确认代码修改
```shell
git add app/Http/Controllers/CustomerAppService/CashFlowController.php
```

15. 取消确认的代码修改（git add 逆操作）
```shell
git reset app/Http/Controllers/CustomerAppService/CashFlowController.php
```

16. 提交代码修改
```shell
git commit -m "fix bug"
```

17. 推送远程代码库
```shell
git push
```

18. 同步远程代码库
```shell
git pull
```

19. 查看提交信息
```shell
git log
```

20. 回滚commit
```shell
git reset --hard <commit_id> 
```
