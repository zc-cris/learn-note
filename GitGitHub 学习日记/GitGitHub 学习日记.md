# Git/GitHub 学习日记

## 1. Git安装（略）



## 2. Git结构

### 2.1 Git本地结构图

### ![1529849744406](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529849744406.png)



### 2.2 Git远程库和本地库的交互

#### 1. 团队内部协作

![1529850293554](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529850293554.png)



#### 2. 跨团队协作

 ![1529850397119](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529850397119.png)



## 3. Git命令行操作

### 3.1 本地库操作

#### 1. git init 本地库初始化

![1529854036822](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529854036822.png)

#### 2. 设置签名

作用：区分不同开发人员的身份

辨析：这个签名和github 账号没有任何关系

范围：项目级别（仓库级别）的签名/ 系统级别的签名

形式: 用户名：cris

​      Email 地址：xxx@qq.com

命令：

```shell
- 项目级别的签名设置
- git config user.name tom
- git config user.email tom@qq.com

- 系统级别的签名设置
- git config --global user.name tom_pro
- git config --global user.email tom_pro@qq.com

```

![1529855043243](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529855043243.png)

![1529855375407](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529855375407.png)



#### 3. 查看版本状态和提交修改文件

```bash
- git status	(查看当前工作区，暂存区的状态)
- git add . (添加所有文件到暂存区)
- git commit -m "提交信息" (将暂存区的修改提交到本地库)
```

- 图解

![1529856574522](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529856574522.png)



#### 4. github创建远程仓库并提交

```shell
- git remote add origin https://github.com/zc-cris/learn-note.git (建立和远程仓库的对应关系)
- git push -u origin master	（第一次 push 到远程仓库的master分支）
- git push origin master	（以后如果push 到master 分支这样写即可）
```





#### 5. git的历史版本操作

##### - 查看历史版本记录的几种方式

```bash
- git log	(用于查看历史记录)
```

![1529939834617](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529939834617.png)

- 满屏时，空格可以向下翻页，b可以向上翻页，q直接退出



```bash
- git log --pretty=oneline	(以一种好看的形式显示历史记录)
```

![1529939986164](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529939986164.png)



```bash
- git log --oneline (以一种更加简洁的形式显示历史记录)
```

![1529940066420](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529940066420.png)



```shell
- git reflog (另外的一种格式显示历史记录)
```

![1529940215572](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529940215572.png)



##### - 版本前进后退的几种方式

- 基于索引值的后退

![1529940851321](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529940851321.png)

​	版本前进同理：git reset --hard 前进版本号

- 使用^符号（只能后退，不能前进，不推荐）

![1529941278670](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529941278670.png)



- 使用~符号

![1529941374601](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529941374601.png)



##### - reset 命令的三个参数具体含义（hard，mixed，soft）

- --soft

  - 仅仅只是在本地库移动了HEAD指针，而没有去改变暂存区和工作区

  ![1529941857364](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529941857364.png)



- --mixed

  - 本地库移动HEAD指针
  - 重置暂存区

  ![1529941987329](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529941987329.png)



- --hard（用的最多）
  - 本地库移动HEAD指针
  - 重置暂存区
  - 重置工作区
  - 相当于本地库的版本回退将会导致暂存区和工作区的一致回退



##### - 永久删除文件如何找回（文件删除操作已经被记录为一个版本）？

```shell
- vim abc.txt
- git add .
- git commit -m'create new abc.txt'
- rm abc.txt(注意：这里是对工作区的操作)
- git status(工作区已经没有了abc.txt)
- git add .
- git commit -m'remove abc.txt'(删除abc.txt 这个操作已经被记录为一个版本)
- git reflog
- git reset --hard 新建abc.txt 的版本号（即可成功找回被删除的abc.txt，且工作区，暂存区，本地库均有）
```



##### - 暂存区的文件删除如何找回（删除操作还没有被commit）？

```bash
- rm apple.txt
- git add .
- git reset --hard HEAD(使用当前HEAD 指向版本的本地库去重置刷新暂存区和工作区，如果当前HEAD 指向的版本号是存在有apple.txt 这个文件的，那么重置后工作区和暂存区又有apple.txt 了)
```



##### - 删除文件并找回文件的方法总结

![1529943316110](C:\File\Typora\GitGitHub 学习日记\GitGitHub 学习日记.assets\1529943316110.png)



















### 3.2 远程库操作

