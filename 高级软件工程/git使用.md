1.将github的下载地址复制  

http： git clone https://github.com/sunnyszq/StudyNotes.git

或者ssh：

$ git clone git@github.com:sunnyszq/LeetCode.git

2.下载到本地

3.将文件中的.git 和其它文件剪切至文件夹，然后删除下载的文件

4.git add 文件名

5.git commit -m "描述"

6.git push -u origin master  （注：此操作目的是把本地仓库push到github上面，此步骤需要你输入帐号和密码）

第一次是用 -u 

以后用

git push origin master 

成功



注意托管文件夹时，不可以托管空文件夹！

**问题描述**：在git bash中键入 $ git push origin master 进行提交的时候出现 如下错误：

error: failed to push some refs to 'https://github.com/bluetata/

![img](https://img-blog.csdnimg.cn/20190103111104776.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RpZXRpbWUxOTQz,size_16,color_FFFFFF,t_70)

**问题原因**：远程库与本地库不一致造成的，在hint中也有提示把远程库同步到本地库就可以了。

**解决办法**：使用命令行：

```bash
git pull --rebase origin master
```

该命令的意思是把远程库中的***\*更新合并\****到（**pull=fetch+merge**）本地库中，**–-rebase**的作用是取消掉本地库中刚刚的commit，并把他们**接到**更新后的版本库之中。出现如下图执行pull执行成功后，可以成功执行git push origin master操作。

**图形描述问题发生的原因及解决办法**：

**1**. 发生问题时候的状态：

![img](https://img-blog.csdnimg.cn/20190103165316596.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RpZXRpbWUxOTQz,size_16,color_FFFFFF,t_70)

**2**. 执行 git pull -–rebase origin master 操作，意为先取消commit记录，并且把它们**临时**保存为补丁(patch)(这些补丁放到”.git/rebase”目录中)，之后同步远程库到本地，最后合并补丁到本地库之中。

![img](https://img-blog.csdnimg.cn/20190103165623544.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RpZXRpbWUxOTQz,size_16,color_FFFFFF,t_70)

**3**. 最后把本地库push到远程库当中，使本地与远程仓库保持一致。

![img](https://img-blog.csdnimg.cn/2019010316575311.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RpZXRpbWUxOTQz,size_16,color_FFFFFF,t_70)



git add 后一定要有git commit -m "描述" ，再push

