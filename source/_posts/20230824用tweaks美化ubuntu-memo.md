---
title: 20230824用Tweaks美化Ubuntu MEMO
date: 2023-08-24T18:38:41.184+08:00
tag:
  - Ubuntu
  - Tweak
  - Linux
updated: 2023-08-24T21:29:18.456+08:00
categories:
  - Ubuntu
---
如昨天文章的狀況顯示—睽違一陣子又來用Ubuntu啦

美化當然是不可少的<3

參考了一些資料順利做了初步的美化～

### 參考資料

- [Ubuntu 更改桌面主題 Gnome, Theme, Tweak](https://ubuntu1804.blogspot.com/2019/06/ubuntu-gnome-theme-tweak.html)
- [很多GTK主題的網站](https://www.pling.com/p/1309218/)

### 重點整理

- Tweaks更換主題的方法：家目錄加一個`.home`資料夾（記得打開顯示隱藏檔案的選項），把下載下來的主題解壓縮後放到裡面，打開Tweak進行更換
- 如果是icon新版比較繁瑣，這邊的例子是我誤以為可以跟以前一樣放在.icon資料夾，結果無效，要參考[這篇](https://askubuntu.com/questions/1385810/downloaded-icons-not-showing-in-the-tweaks-in-ubuntu-21-10)搬到`/usr/share/icons`的情景：（[玩壞了請服用](https://askubuntu.com/questions/1474445/help-i-changed-my-icons-with-tweaks-now-nothing-works)）

```shell
x200706@ubuntu:~/.icon$ sudo cp -r Tela-purple/ /usr/share/icons
```

- 終端機的部份需要先

```shell
sudo apt install gnome-shell-extensions
```

- 類似xfce的工具列




