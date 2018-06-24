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











### 3.2 远程库操作

