---
title: 20230615 Database相關MEMO
date: 2023-08-01T15:00:00.000+08:00
updated: 
tag: 
  - database
categories: 
  - database
cover: 
---
>20230614於台北恆毅DB2全日講座

### 雜項
- 通俗DB構造
  - 像DB2或Oracle都有[tablespae](https://zh.wikipedia.org/zh-tw/%E8%A1%A8%E7%A9%BA%E9%97%B4)的構造
  - 不同DB的物理儲存方式略有不同
- 不少DB有提供排程功能，但多數使用者傾向寫shell，用作業系統排程管理
- auto commit了解下
- 有趣的`check`語法

***
### DB的Page單位
- [OLTP&OLAP](https://ithelp.ithome.com.tw/questions/10200075)
- [Columnar-based&Row-based](https://datadrivenai.wordpress.com/2019/10/27/row-oriented-%E8%B3%87%E6%96%99%E5%BA%AB-v-s-columnar-%E8%B3%87%E6%96%99%E5%BA%AB/)
  - 最小查詢單位稱為page，看看一個DB page是一筆row還是欄位，判斷它現在的模式屬於何種

***
### 如何使用DB
跟DB交流的方法很多，如：
1. 透過互動模式（進到psql、db2）
2. 直接下命令（優點是可以輸出文件），建議可以給SQL語句加雙引號避免跳掉
3. 或提交SQL語法，例如DB2是：
   ```shell
   db2 -svtf *.sql
   # s這個參數我記得是關於有錯是否終止？
   # t這個參數是調用預設終止符為分號，有其他指令可以修改
   ```
4. 應用程式或API，可以透過JDBC、ODBC橋接
5. Stored Procedure
- 一般來說DB主機跟AP主機會是分開的，畢竟不希望因為程式問題導致記憶體溢位，影響DB運作
- 但DB引擎內還是可以預存一些程式
- 不過有人認為[不要用Stored Procedure](https://medium.com/@steph.c/5-%E5%80%8B%E4%BD%A0%E4%B8%8D%E8%A9%B2%E4%BD%BF%E7%94%A8-stored-procedure-%E7%9A%84%E5%8E%9F%E5%9B%A0-9ac524fe37ba)

***
### 常見DB物件
- Schema識別符
```sql
aaaschema.bbbtable # Fully Qualified Name
```
- Index：增進查詢效率
  - PK
  - 自訂索引
- View：不會儲存資料的視圖
- Constraint：約束，常見的有
  - Unique
  - References
- Trigger
- Partition