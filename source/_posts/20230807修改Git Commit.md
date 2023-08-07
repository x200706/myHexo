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