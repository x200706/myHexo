---
title: 20230629 Spring Boot Maven包版失敗可能與解法
date: 2023-08-01T15:00:00.000+08:00
updated: 
tag: 
  - Spring Boot
  - Maven
categories: 
  - [Spring Boot]
  - [Maven]
cover: 
---
1. 沒吃到環境參數，[這篇討論有範例](https://stackoverflow.com/questions/25420745/how-to-set-spring-active-profiles-with-maven-profiles)
2. Test有問題（但如果平常run沒問題，就比較偏向上方的問題），[類似的案例](https://blog.csdn.net/lh155136/article/details/126302063)

有些人的如何排除教學是使用：
- [套件忽略測試](https://blog.csdn.net/weixin_45151960/article/details/125065441)
- [註釋掉測試](https://blog.csdn.net/hyh17808770899/article/details/118896136)
- [帶參數忽略測試](https://blog.csdn.net/weixin_42534940/article/details/104042962)

但感覺比較消極?_?