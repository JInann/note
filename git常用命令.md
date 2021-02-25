

git常用命令

commit

```bash
git commit -m '提交信息'
```



log

```bash
git log # 查看提交记录
```



push

1. 多个远端，分别推送

```bash
git push -u origin # 指定推送的远端
```
2. 本地版本落后远端版本，使用强制推送
```bash
git push -f # 强制推送
```
3. 删除远端分支
```bash
git push :develop # 删除远端分支
```



pull

```bash
git pull
```



merge

```bash
git merge branchName # 合并指定分支到当前分支
```

```bash
git merge - # 合并之前的分支到当前分支
```

```bash
git merge --abort # 取消合并
```



checkout

```bash
git checkout branchName # 切换到指定分支
```

```bash
git checkout -b newBranch # 创建newBranch分支，并切换到newBranch分支
```

```bash
git checkout - # 切换到上一个分支
```

```bash
git checkout -b branchX hash # 根据提交记录hash，创建branchX分支
```

branch

```bash
git branch # 显示所有本地分支
```

```bash
git branch -a # 显示所有分支（本地+远端）
```

```bash
git branch newBranch # 创建分支
```

```bash
git branch newBracnh -d # 删除分支

```



cherry-pick

```bash
 git cherry-pick bc2506 # 合并bc2506这次提交到当前分支
```








