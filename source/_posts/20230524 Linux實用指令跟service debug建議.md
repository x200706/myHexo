---
title: 20230524 Linux實用指令跟service debug建議
date: 2023-08-01T15:00:00.000+08:00
updated: 
tag: 
  - Linux
categories: 
  - Linux
cover: 
---
### ls不要顯示所有檔案
```shell
ls -l 檔案名稱
# 或
ls -ld 檔案名稱
```
只會顯示該檔案的資訊行

### Sudoer
- 特性是授權的語法、副檔名要一字一句完全相符（書寫設定時雖然部分指令可能要加上`/bin/`，但在終端機可以忽略，例如設定寫`/bin/systemctl`，實際上可以只下`systemctl`）
- 想要授權給特定使用者，得編修這個檔案：[[Reply] Linux中的sudoers檔案設定簡介](https://ithelp.ithome.com.tw/articles/10053821)

***
### Linux服務異常故障排除步驟
1. 注意資料夾與檔案權限與用戶組
1. 如果是server，用`netstat`看欲啟動服務之port號有無在上面
1. 檢查防火牆狀態，各個系統指令不同（連線面）
1. 如果是server，在你的程式使用日誌記錄功能
1. 同網段主機丟`wget http://ip:port`看看能不能成功（連線面）
1. 使用以下指令查看service日誌
    ```shell
    journalctl -xeu service名稱
    ```
1. 如果它說找不到service，但service確實存在於系統目錄，可能要注意SELinux細部權限設置問題 [鳥哥](https://linux.vbird.org/linux_server/rocky9/0140selinux.php) [over stackflow的討論](https://stackoverflow.com/questions/63619681/systemctl-myservice-service-not-found)
    ```shell
    ls -Z 檔案或service名稱 #這可以看細部權限設置
   chcon 描述 檔案或service名稱 #修改細部權限設置
   # 或
   restorecon 檔案或service名稱
   ```
   指令參考文章：[1](https://dywang.csie.cyut.edu.tw/dywang/linuxsecurity/node7.html) [2](https://happybevis.github.io/2018/05/02/The-Magic-Selinux-Restore-Rule/)
1. 嘗試reload你的所有服務
    ```shell
   systemctl daemon-reload
   ```