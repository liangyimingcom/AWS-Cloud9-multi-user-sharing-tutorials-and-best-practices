# AWS Cloud9多用户共享教程与最佳实践

AWS Cloud9的优点之一是可以由多个人共享同一环境。我们可以同时更新同一文件或通过聊天进行交流，很强大哦。

**设置起来有一点烧脑，我们总结了在AWS Cloud9上准备共享环境并允许多人开发的步骤（最佳实践）如下。**

[TOC]

 **背景（可以忽略）：**

1）Cloud9（简称C9）很多时候会使用内网环境，需要注意的，要选择是 No-Ingress EC2 instance, access via SSM；

2）全部的C9权限已经放在了SSM的IAM ROLE，所以C9可以被任意管理员账号创建，都没问题；

3）通过C9的共享功能给到其他IAM用户，和SSM的IAM ROLE来控制C9的实际权限；

4）实际上只开了一台C9，而多个IAM用户可以同时访问。这台C9因为配置好了环境，可以保留给未来运维使用。

 

**共享Cloud9的操作步骤如下：**



### 【1-1】准备一个IAM账户用于创建单台C9

共有三种类型的策略(权限)：AWSCloud9Administrator，AWSCloud9User，AWSCloud9EnvironmentMember。

|                            | **新建** | **编辑** | **删除** |
| -------------------------- | -------- | -------- | -------- |
| AWSCloud9Administrator     | OK       | OK       | OK       |
| AWSCloud9User              | **OK**   | NO(*)    | NO(*)    |
| AWSCloud9EnvironmentMember | NO       | NO       | NO       |

**\* ：您可以编辑/删除自己创建的环境。**

![image-20210411095905821](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411095905821.png)



![image-20210411095914788](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411095914788.png)



Cloud9Admin IAM账号创建成功。

 

### 【1-2 】登录IAM账号并创建C9

进入C9点击创建：

![image-20210411095734937](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411095734937.png)



![image-20210411095800017](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411095800017.png)

![image-20210411095939374](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411095939374.png)



![image-20210411095950985](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411095950985.png)

Cloud9Admin EC2 创建成功。

 

### 【1-3】创建C9的共享用户

![image-20210411100031752](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100031752.png)



![image-20210411100045789](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100045789.png) 

普通的共享用户创建完毕。

 

### [2-1]与其他用户共享

登录IAM账号Cloud9Admin，尝试与其他用户共享环境Cloud9Admin。 

![image-20210411100128566](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100128566.png)

单击右上方的共享。

![image-20210411100140868](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100140868.png)


按[共享]，将显示以下屏幕。通过邀请用户来共享您的环境。

输入您要共享的用户名，选择R(只读)或R / W(可能读/写)，然后单击[邀请]。这样就完成了对用户的邀请。

![image-20210411100157358](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100157358.png)

 ![image-20210411100206221](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100206221.png)



### [3-1]使用共享环境

登录Cloud9EKSUser1账户后，

![image-20210411100227521](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100227521.png)





### [3-2]两个用户同时编辑

两个用户编辑同一文件时，编辑的内容几乎没有延迟地反映在彼此的屏幕上。

源代码行号的左侧有一种颜色，但是每个用户的颜色都不同。您可以看到哪个用户编辑了哪一行。

![image-20210411100246944](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100246944.png)

3个用户同时编辑：如果邀请其他用户并编辑同一文件，则同样如此。

![image-20210411100343275](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100343275.png)



### [3-3]聊天功能

聊天显示在屏幕右侧。 没有象形图等，其印象是仅提供了聊天所需的最低功能。

![image-20210411100350310](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100350310.png)

 

### [4-1] 权限配置

使用ADMIN的IAM账号，配置SSM Role的权限，比如增加Cloud9中访问S3的权限等，如下图：

先找到EC2

![image-20210411100419102](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100419102.png)




去IAM里面增加权限即可，如下：

![image-20210411100428864](https://raw.githubusercontent.com/liangyimingcom/storage/master/uPic/image-20210411100428864.png)

 

### [4-2] 总结

共享的环境非常适合以下情况。

- 配对编程（也称为*对等编程*). 这是指，两个用户在单个中一起处理相同的代码。环境. 在配对编程中，通常一个用户编写代码，同时另一个用户观察正在编写的代码。观察者为代码编写者提供即时输入和反馈。在完成项目期间，他们的位置经常发生变换。如果没有共享的环境，配对编程人员团队通常坐在一台计算机前面，并且每次只有一个用户可以编写代码。在共享的环境中，两个用户可以坐在自己的计算机前面，并且可以同时编写代码，即使他们在不同的办公室工作。
- 计算机科学课程。如果老师或助教要访问学生的环境以检查其家庭作业或实时解决其环境的问题，这非常有用。学生还可以与同学一起在单个环境中实时编写代码，以便共同完成共享的家庭作业项目。即使他们可能位于不同的位置并使用不同的计算机操作系统和 Web 浏览器类型，他们也可以完成该任务。
- 任何其他情况 - 多个用户需要实时协作处理相同的代码。



更多详情，请参考官方文档：

https://docs.aws.amazon.com/zh_cn/cloud9/latest/user-guide/share-environment.html#share-environment-about



