# GitHub

## 将现有仓库和远程结合

将仓库往GitHub上推送的时候，需要**本地的分支名和远程的相同**才能使用`git push -u origin master`

```bash
git remote add origin 

# 重命名分支 ---- important
git branch -M newName

# 推送分支
# 如果是远程不存在的分支 则会创建新的分支
git push -u origin newName
```

### 拉取远程分支

```bash
# 拉取远程分支
git checkout -b branch-name origin/branch-name
```

### 处理冲突

```bash
# 出现冲突
git pull

# 处理冲突之后执行这一步
git add .
git commit -m "fix-conflict"

# 提交
git push
```

