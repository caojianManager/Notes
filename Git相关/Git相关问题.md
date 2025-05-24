# Git相关问题总结

## 【】上传文件大小超过仓库限制？

```c
//下载并安装 Git 命令行扩展。下载并安装后，运行以下命令为您的用户帐户设置 Git LFS：

brew install git-lfs 
git lfs install

//选择你希望git lfs管理的文件或者文件类型
git lfs migrate import --include='*.pdf'
git lfs track '*.pdf'

//现在确保 .gitattributes 被跟踪：
git add .gitattributes

git push
```

# Git Ignore不生效的问题

git rm -r --cached .
git add .
git commit -m "刷新 .gitignore 规则"