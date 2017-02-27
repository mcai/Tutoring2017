## 设置用户名和邮件地址

`git config --global user.name "<name>"`

`git config --global user.email "<email>"`

## 创建项目

`git init <repo_name>`

`git clone <remote_repo_url>`

## 管理本地改动

`git status`

`git add <path_to_file>`

`git commit -m "<comment>"`

## 查看提交日志

`git log`

## 同步改动

`git push <alias> <branch>`

`git pull`

## 管理远程

`git remote -v`

`git remote add <alias> <remote_repo_url>`

`git remote remove <alias>`

## 共享到github
```
git remote add origin https://github.com/<user>/<repo>.git
git push -u origin master
```
