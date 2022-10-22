# 在命令提示符里显示 git 分支名 - TueAug30 2022
## 显示分支
命令 `git rev-parse --abbrev-ref HEAD 2>/dev/null` 能得到只含当前分支名字的字符串，且不在仓库里时什么都不输出，返回非 0 值。可以编写一个函数 `__git_ps1` 实现获取分支名的功能。
```sh
__git_ps1() {
  branchName=$(git rev-parse --abbrev-ref HEAD 2> /dev/null)
  if [ $? = 0 ]; then branchName="($branchName)"; fi
  echo $branchName
}
```

## 命令提示符
环境变量 `PS1` 记录了命令提示符的内容。比如说如果 `PS1=qwert `，那么每次提示命令输入时就会显示 `qwert `。它还支持通过各种转义实现显示工作目录、更改样式等，可以自行搜索 PS1 和 ANSI 转义序列。我的 $PS1 长这样：
```sh
PS1='\[\e[;92m\][\u@\H]\[\e[95m\]\t\[\e[91m\]#\#\[\e[96m\]\w\[\e[93m\]$(__git_ps1)\[\e[m\]\n\[\e[94m\]\$\[\e[m\] '
```
