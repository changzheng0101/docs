# fork后同步更新

## 问题描述

在fork别人的仓库之后，希望别人的远程仓库更新之后，自己fork的仓库也可以同步的更新

## 解决思路

本地clone自己fork的仓库，用origin代表fork的仓库，创建一个新的base指向原来的仓库，先将原来仓库的变更fetch到本地，merge之后再push到fork的仓库。

```bash
# 查看拥有的远程仓库变量
git remote -v
# 添加原仓库的地址
git remote add base 原仓库url
# 查看所有的remote
git remote 
# 拉取base的变更到本地
git fetch base
# 将base的变更和本地合并（以master分支为例子）
git checkout master
git merge base/master
# 推送到fork之后的仓库
git push
```

### 额外命令

```bash
# 将remote中的base重命名为upstream
git remote rename base upstream
# 删除base
git remote rm base
```

