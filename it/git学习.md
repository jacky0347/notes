### 1.为什么学习git？

方便的使用git和github进行交互，记录自己的学习体会

### 2.git原理

- **工作区：**就是你在电脑里能看到的目录。
- **暂存区：**英文叫stage, 或index。一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
- **版本库：**工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：

![git原理](http://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)

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

