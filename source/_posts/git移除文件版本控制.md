---
title: git移除文件版本控制
img: /images/git.jpg
categories:
- 工具
tags:
- git
---

## 移除某文件/文件夹版本控制
```bash
git rm -r -n --cached "file/dir" //展示要移除的文件列表
git rm -r --cached  "file/dir" //执行
git commit -m "message"  
git push 
```
## 增加.gitignore文件
```
file/dir
```

