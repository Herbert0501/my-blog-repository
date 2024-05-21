---
title: Git 常用命令总结（持续更新...）
slug: git-chang-yong-ming-ling-zong-jie-chi-xu-geng-xin...
cover: ""
categories: []
tags: []
halo:
  site: https://blog.kangyaocoding.top
  name: 9e00c7b6-3b95-463a-b5a4-b78391403b30
  publish: false
---
在使用Git时，尤其是通过集成开发环境(IDE)的图形界面操作Git后，我们可能会发现自己不太容易记住一些命令。

以下是我整理的一些关于Git命令的总结 （Summary of commonly used Git commands）：

### 基本操作

1. **初始化仓库**
   ```sh
   git init
   ```

2. **克隆仓库**
   ```sh
   git clone <repository>
   
   // 克隆一个仓库
   git clone https://github.com/Herbert0501/gitignore.git
   // 指定分支
   git clone -b 分支名 https://github.com/Herbert0501/gitignore.git
   ```

3. **查看状态**（查看当前工作目录和暂存区的状态）
   ```sh
   git status
   ```

4. **添加文件到暂存区**
   ```sh
   git add <file>
   
   // 将所有变化添加到暂存区
   git add .
   ```

5. **提交修改**
   ```sh
   git commit -m "commit msg"
   ```

6. **查看提交历史**
   ```sh
   git log
   ```

### 分支操作

1. **查看分支**
   ```sh
   git branch
   ```

2. **创建新分支**
   ```sh
   git branch <new-branch>
   
   // 例如为模块新增功能-删除功能
   git branch 20240521-feature-delete
   ```

3. **切换分支**
   ```sh
   git checkout <branch>
   
   // 切换到主分支 （main）
   git checkout main
   ```

4. **创建并切换到新分支**
   ```sh
   git checkout -b <new-branch>
   ```

5. **删除分支**
   ```sh
   git branch -d <branch>
   ```

6. **合并分支**
   ```sh
   git merge <branch>
   
   // 1.切换到主分支（main）
   git checkout main
   // 2.将20240521-feature-delete分支合并到main
   git merge 20240521-feature-delete
   ```

### 远程仓库

1. **查看远程仓库**
   ```sh
   git remote -v
   ```

2. **添加远程仓库**
   ```sh
   git remote add <name> <url>
   ```

3. **拉取远程仓库**
   ```sh
   git pull <remote> <branch>
   ```

4. **推送到远程仓库**
   ```sh
   git push <remote> <branch>
   
   // 强制推送, 将本地覆盖到远程
   git push -f <remote> <branch>
   ```

5. **删除远程分支**
   ```sh
   git push <remote> --delete <branch>
   ```

### 撤销修改

1. **撤销未暂存的修改**
   ```sh
   git checkout -- <file>
   ```

2. **撤销已暂存的修改**
   ```sh
   git reset HEAD <file>
   ```

3. **撤销提交**
   ```sh
   git revert <commit>
   ```

4. **重置提交**
   ```sh
   git reset --soft <commit>
   git reset --hard <commit>
   ```
5. **将文件从暂存区中删除，但保留工作目录中的文件**
   ```sh
   git rm --cached <file1> <file2>
   
   // 1. 将文件添加到.gitignore
   file1
   file2
   // 2. 使用命令删除
   git rm --cached file1 file2
   // 3. 提交更改（删除远程文件，保留本地文件）
   git add .
   git commit -m "something modify"
   ```
### 标签

1. **列出标签**
   ```sh
   git tag
   ```

2. **创建标签**
   ```sh
   git tag <tag>
   ```

3. **删除标签**
   ```sh
   git tag -d <tag>
   ```

4. **推送标签**
   ```sh
   git push <remote> <tag>
   ```

5. **推送所有标签**
   ```sh
   git push <remote> --tags
   ```

参考 [Git 官方文档](https://git-scm.com/doc) 

如果您觉得今天的文章对您有帮助，我相信您一定会喜欢我的博客。
[哈利の小屋 - Kang Yao Coding - 努力有时候战胜不了天分，但至少能让别人看得起你](https://blog.kangyaocoding.top/ "哈利の小屋 - Kang Yao Coding - 努力有时候战胜不了天分，但至少能让别人看得起你")。
在那里，我会定期更新关于计算机类的文章，并与您分享更多实用的经验和知识。欢迎您来访问和留言交流。