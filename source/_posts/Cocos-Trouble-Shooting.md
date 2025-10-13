---
title: 各類cocos creator 3.x版問題與成因
date: 2025-10-13 14:12:57
categories: 軟體開發
tags: Cocos
---

## 本文章可以幫你
1. 了解cocos 3.x版引擎部分問題
2. 解決問題與其成因

## 同層級bundle使用到同素材可能因引擎打包順序不一造成問題

### Encountering

- bundle A與bundle B priority 都是2, bundle B有使用到某素材且該素材放置於bundle B內，但bundle !也有使用到該素材

### Finding

- 打包時該素材被打包到bundle A內，需要有bundle B才能使用
- 打包時會依據bundle的priority再看名稱順序，bundle A / B priority 都是2但字母 "A" 大於 "B"，所以素材會先被包在bundle A內

### Solution

目前想到三種方法

- 把bundle A的priority降低
- 將bundle A / B都有用到的素材放置priority更高的bundle
- 改bundle Name，把bundle UI的名稱層級往上提
