### 1.为什么学习git？

方便的使用git和github进行交互，记录自己的学习体会

### 2.git原理

- **工作区：**就是你在电脑里能看到的目录。

- **暂存区：**英文叫stage, 或index。一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。

- **版本库：**工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

  有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫**HEAD**

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：

![git原理](http://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)

前面讲了我们把文件往Git版本库里添加的时候，是分两步执行的：

第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。

你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改

### 3.git基本操作

```java
git init 初始化git本地仓库
```

```java
git add . <files> 添加本地所有修改或者新增文件到暂存区
```

```java
git commit -m 'description' 提交到本地版本库
```

```java
git status 查看文件修改状态
```
```java
git clone [url] 克隆远程仓库代码
```

```java
 git reset --hard [commitid]|HEAD^[^...]|HEAD~n  
 回退到commitid那个版本|回退到上[上...]个版本|回退到n个版本之前
```

```java
git diff 
提交后，用git diff HEAD -- readme.txt命令可以查看工作区和版本库里面最新版本的区别
```

