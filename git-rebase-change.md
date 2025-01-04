# 用分支维护自己的修改 - SatJan4 2025
假设我 clone 了一个远程仓库，我自己改了些代码，到时更新时就会有冲突。

```bash
git checkout -b xmh
git commit -am 'personal changes'
# 很多次 commit
git checkout main
git pull
git checkout xmh
git rebase main
```

接着用
```bash
git rebase --skip
```
直到你跳到最后一次提交，把冲突解决掉
```bash
vim xxx
git rebase --continue
```
就好了。
