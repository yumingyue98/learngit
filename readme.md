### Git 学习

 -- 于明月

------

#### 一、什么是Git

1. 最先进的分布式版本控制系统

2. 版本控制：源码控制（SCM:source control management）是版本控制（VCS:version control system）的子集

3. 集中式与分布式：

   集中式版本控制系统（SVN等）的版本库是集中存放于中央服务器的。缺点是必须联网使用。

   分布式版本控制系统没有中央服务器，每个人的电脑都是一个完整的版本库。
   
4. 版本库只能跟踪文本文件的改动，对于二进制文件虽能管理，但无法知道文件具体更改的内容

   

#### 二、Git创建版本库

1. 设置用户名及邮箱

   ```
   $ git config --global user.name "Your Name" 
   $ git config --global user.email "email@example.com"
   ```

   global参数代表这台机器上的所有仓库都使用这个配置

2. 将**当前目录**初始化为git可管理的仓库

   ```
   $ git init
   ```

3. 增加文件到版本库

   ```
   $ git add 文件名
   $ git commit -m "本次提交的说明"
   ```

   add是将文件提交到暂存区，commit将暂存区内容一次性提交到当前分支

   * **工作区、暂存区与版本库**

     ![QQ截图20200903233334](C:\Users\hp\Desktop\实验班\QQ截图20200903233334.jpg)

     工作区：working directory 就是在自己电脑上看到的目录

     暂存区：staging index 存在于.git/index

     版本库：repository 工作区的.git隐藏目录。版本库中包括暂存区

     

#### 三、Git版本管理

1. 查看仓库当前状态

   ```
   $ git status
   ```

   结果中modified显示被修改的文件名

2. 查看修改内容

   ```
   $ git diff 文件名
   ```

3. 查看提交历史

   ```
   $ git log
   ```

   加`--pretty=oneline`参数可以简洁的显示commit id，可用这个id进行版本回退

   版本号（commit id）不是1、2、3，而是一个SHA1计算出来的非常大的数字，用十六进制表示，可在多人协作时避免冲突

4. 查看命令历史

   ```
   $ git reflog
   ```

5. 版本回退

   ```
   $ git reset --hard HEAD^
   ```

   版本回退需要指定回退的版本：HEAD指向当前版本，HEAD^指向上一个版本，往上100个版本可用HEAD~100表示。也可直接用commit id的前几位表示，git会自动找全。



#### 四、Git 管理修改

1. 进行如下测试

   第一次修改->`git add`->第二次修改->`git commit`

   版本库中的内容是第一次修改后的结果，说明**Git跟踪管理的是修改而非文件**

2. 撤销修改

   ```
   $ git checkout -- 文件名
   ```

   结果：让文件退回到最近一次commit/add的提交

   + 文件在工作区修改后尚未放入stage，则回到最新的repository的提交
   + 文件添加到stage后又在工作区进行了修改，则回到最新的stage提交

3. 文件删除

   在工作区将文件删除后，使用以下两种命令（效果相同）

   ```
   $ git rm 文件名
   $ git add 文件名
   ```

   再用`git commit`提交，版本库就会删除该文件

   

#### 五、分支管理

1. 查看分支

   ```
   $ git branch
   ```

   结果中*后标记的是当前分支

2. 创建分支

   ```
   $ git branch <新建分支名>
   ```

3. 切换分支

   ```
   $ git checkout <分支名>
   $ git switch <分支名>
   ```

4. 创建并切换至新建分支

   ```
   $ git checkout -b <分支名>
   $ git switch -c <分支名>
   ```

   + `git switch`命令是2.23版本发布的新命令，旧版本升级为最新版本后使用

     - 2.17.1之前的版本升级用

       ```$ git update```

     - 2.17.1之后的版本升级用

       ```$ git update-git-for-windows```

   + `switch -c`/`checkout -b`相当于两个命令

     ```
     $ git branch <新建分支名>
     $ git checkout <该分支名>
     ```

5. 删除分支

   ```
   $ git branch -d <分支名>
   ```

6. 合并分支到**当前分支**

   ```
   $ git merge <待合并分支名>
   ```

   + **分支合并的冲突解决**

     - 冲突发生的场景

       当两个分支修改同一个文件，且各自都有了新的提交时，发生冲突，git无法自动合并分支，如图所示

       ![图片1](C:\Users\hp\Desktop\实验班\图片1.png)

       此时若使用`git merge`命令，查看文件，可以发现Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容。

     - 冲突的解决方法

       把git合并失败的文件手动编辑解决冲突，再`add`，`commit`提交。

       结果如图所示：

       ![QQ截图20200904102434](C:\Users\hp\Desktop\实验班\QQ截图20200904102434.jpg)

       再将多余分支用`$ git branch -d feature1`删除即可

   + 分支合并图

     使用带参数的`git log`命令可以简洁的查看分支合并图

     ```
     $ git log --graph --pretty=oneline --abbrev-commit
     ```

     ![2分支图](C:\Users\hp\Desktop\实验班\2分支图.jpg)

   

#### 六、远程仓库

1. 远程仓库的意义：

   找一台电脑充当服务器，托管git仓库，其他人可随时从这个电脑上克隆仓库到自己的电脑中。

   远程仓库既可以作为备份，又可让其他人通过该仓库协作

2. Github

   GitHub 是全球最大的开源代码托管平台，是基于 Git 的编程社区

3. 将本地库内容推送到远程仓库

   + 在Github上创建自己的账号

   + 设置SSH Key

     - 若用户主目录下有.ssh目录，则该目录下的`id_rsa.pub`文件就是公钥，设置到Github自己账户下即可

     - 没有.ssh目录则需要创建SSH Key

       ```
       $ ssh-keygen -t rsa -C "邮箱地址"
       ```

       再去主目录下找.ssh并复制公钥添加到账户下

   + 在自己的账号下新建一个仓库

   + 将本地仓库与新建的仓库关联

     ```
     $ git remote add origin git@server-name:path/repo-name.git
     ```

     origin为远程库起的名字，Git默认为origin

   + 将本地库内容推送到远程库上

     - 第一次推送

       ```
       $ git push -u origin master
       ```

     - 后续推送

       ```
       $ git push origin master
       ```

4. Github的协作模型

   ![图片2](C:\Users\hp\Desktop\实验班\图片2.png)

