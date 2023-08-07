---
title: 20230807修改Git Commit
date: 2023-08-07T08:16:55.971
tag:
  - git
updated: 2023-08-07T08:16:55.971
categories:
  - [git]
---
[參考文章](https://gitbook.tw/chapters/using-git/amend-commit1)

重點整理：
- 修改最後一次commit
```shell
git commit --amend -m "想改的訊息"
```
- 修改更早之前的，要用[rebase](https://gitbook.tw/chapters/rewrite-history/change-commit-message)
  - 我的嘗試：
```shell
git log --oneline # 看要修改的範圍 如果有commit1, 2, 3 想修改2 就得回到3
git rebase -i 編號
# 進入互動模式，使用方法同Vim
# pick 要保留的commit
# reword 要修改訊息的commit
# esc->:wq存檔離開
# 進入第一個reword
# 修改commit esc->:wq存檔離開
```
但不知為何發生了問題（類似[如此](https://stackoverflow.com/questions/31069316/error-with-git-rebase-could-not-apply)），所以我暫時是下載了[Sourcetree](https://www.sourcetreeapp.com/)，之後再到模擬環境好好練習`git rebase` `~_~`

>補充：如果有rebase不想做了，checkout回main分支，如果是Windows，終端機鍵入`rmdir .git/rebase`（Linux可看[這篇](https://stackoverflow.com/questions/34496834/how-do-i-remove-an-old-rebase)，基本上在已經有其他rebase的狀況下輸入`git rebase -i 編號`它也會提醒你一樣的事情）

不過我用Sourcetree也是拋差不多的錯誤，可能第一次下`git commit --amend -m "想改的訊息"`又順便修改東西一起commit&push，中間可能發生甚麼事情導致現在的局面...之後來好好解...