---
layout:     post
title:      "Git Commands"
date:       2019-11-06
author:     "Dylan"
header-img: "img/post-git-commands.jpg"
catalog: true
tags:
    - Git
    - Commands
---
## branch

#### 修改commit注释

git rebase -i <commitid>
git commit --amend
git rebase --continue

#### commit

git commit --allow-empty -m "init"

git add .
git commit --amend //将当前改动追加到最近一次提交上。

git reset HEAD^
https://blog.csdn.net/carolzhang8406/article/details/49761927

#### 回滚&恢复

git reset <commitid>会恢复到之前某个提交的版本，且那个版本之后提交的版本丢弃,之后应git push -f
git revert <commitid>只去除<commitid>的更改,而保留<commitid>之后的更改,提交记录保留<commitid>,并新增revert对应的commit

reset恢复
git reflog; git reset <commitid>

#### merge

合并不同线上的分支：(合并同一线上的直接：`git merge`)

```
--no-commit     只合并不commit，操作完仍处于合并态，没必要用
--squash        只合并不commit
git merge       合并并且commit，保留原commit历史
```

## git status

#### untracked files

```
git clean -f    #删除 untracked files
git clean -fd   #连 untracked 的目录也一起删掉
git clean -xfd  #连 gitignore 的untrack 文件/目录也一起删掉。
                #慎用，一般这个是用来删掉编译出来的 .o之类的文件用的
```

在用上述 git clean 前，建议加上 -n 参数来先看看会删掉哪些文件，防止重要文件被误删

```
git clean -nxfd
git clean -nf
git clean -nfd
```

#### 撤销add

`git reset`

#### 撤销没add修改

`git checkout .`

#### Please move or remove them before you switch branches

`git clean -dfx`


## 删除commit

```
git rebase -i <上一次commit id>  弹出界面里把要删的commit前的pick改成drop
git branch -r //查看远程分支
git push <branch name> -f //强制push,eg: git push origin nscscc -f
```


## 修改git分支名称

```
git branch -m oldbranch newbranch
git push --delete origin oldbranch
git push origin newbranch
```


## ssh

#### ssh permission

ssh密钥也分用户，root和一般用户可能用的不是一个密钥

#### 配置多个ssh-key

生成key的时候更改文件名为xx_id_rsa

在.ssh目录下新建文件 config，填入以下内容:

```
# Host和HostName填写git服务器的域名，IdentityFile指定私钥的路径
# gitee
Host gitee.com
HostName gitee.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/gitee_id_rsa
# github
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/github_id_rsa
```

首次使用需要确认并添加主机到本机SSH可信列表，即`The authenticity of host '' can't be established`错误