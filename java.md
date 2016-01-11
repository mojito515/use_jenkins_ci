- ###<span id="java">java项目的自动化构建</span>
**在linux环境下安装Jenkins**有两种方式：  

  1.`sudo java -jar jenkins.war –httpPort=18080 –ajp13Port=18009`  

  2.`yum install jenkins`  

  第一种方式下，如果你是SSH连接到linux主机，当你关闭连接的时候，这个命令也会被中断；  

  第二种方式,是在CentOS系统中使用yum命令安装（熟悉CentOS系统的人应该不陌生），安装完成之后,
Jenkins会成为系统中的一个serviMce，只要在命令行执行`service jenkins start|stop|restart`
就可以完成服务的启动停止和重启。  

  **配置文件路径**为：`/etc/sysconfig/jenkins`，可能需要root权限。配置文件中主要需要修改的是启动
端口`JENKINS_PORT` (默认是8080)，使用jenkins的用户`JENKINS_USER`(默认生成一个jenkins)。
我这里的用户是cms,端口是8818,
  > JENKINS_USER="cms"  
  > JENKINS_AJP_PORT="8819"

  修改jenkins涉及到的目录和文件的权限所属：
>sudo chown -R cms /usr/lib/jenkins  
sudo chgrp -R cms /usr/lib/jenkins  
sudo chown -R cms  /var/log/jenkins  
sudo chgrp -R cms  /var/log/jenkins  
sudo chown -R cms  /var/lib/jenkins  
sudo chgrp -R cms  /var/lib/jenkins  
sudo chown -R cms  /var/cache/jenkins  
sudo chgrp -R cms  /var/cache/jenkins  

  之后就可以启动Jenkins了，执行命令`sudo service jenkins start`  

  ![show](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-show-jenkins.png)

  jenkins安装好之后，首先需要确认你本地是否有:

   **Maven**  负责编译java代码  
   **Git**    负责从代码管理服务器中拉取最新提交的代码  
   **JDK**   这个就不用说了，你没有jenkins你也安装不了  

  好了，这里就不赘述以上工具的环境变量的配置了，一定要有。

  打开Jenkins主界面，首先下载构建所需的插件，依次进入`系统管理`-`管理插件`，下载:  

   **GIT plugin**  
   **Maven Integration plugin**  
   **Email Extension Plugin**  （对jenkins自带邮件通知的扩展，可以自定义邮件模板）  

  ####配置Jenkins `系统管理`-`系统设置`  

  这里主要是对maven、Git、JDK的路径做一些配置  

  ![system-config](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-system-config.png)

  如果你下载了**Email Extension Plugin**插件，在系统配置中可以设置你想要的邮件通知属性  

  ![email-config](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-email-config.png)

  系统配置之后，回到主界面，选择`新建`，填写Item名称，选择构建一个maven项目  

  ![create-job](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-create-job.png)  

  下一步，源码管理中填入你拉取代码的git地址，可以是HTTP协议，也可以是SSH。当然，如果是**HTTP**，要有
对应的用户名和密码；如果是**SSH**，用户名和密码是git版本管理所在的服务器主机的用户名和密码，
并且需要在git版本管理器所在服务器上添加ssh登录的auto文件中添加公钥
private key是jenkins所在主机的用户私钥。  

  ![config-git](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-config-git.png)  

  构建触发器中勾选`Build whenever a SNAPSHOT dependency is built`和`Poll SCM`
这样构建的触发器，会每三分钟（H/3 * * * *）轮询一遍你的代码库，只要你往git的develop
分支中commit代码，Jenkins就会构建一次

  ![config-trigger](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-config-trigger.png)  

  编译使用的是maven，所以要确定到maven的pom文件和执行命令，如图  

  ![config-build](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-config-build.png)  

  添加构建后的动作，比如你希望执行什么shell、邮件通知到developer
或者管理员等  

  ![config-email](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-config-email.png)  

  这里打包好的文件在你的Jenkins主目录`/var/lib/jenkins`下地workspace里面，并且会保留
你每一次的构建代码包，有关构建结束的部署工作，你可以自己写**shell**脚本执行，也可以在pom文件中
写有关tomcat的插件属性，**利用maven**直接将代码部署到tomcat的部署目录下（其中也涉及到访问权限
的问题，这里就不展开描述了）。

  回到主目录，你的构建job会在job list中显示，其中`S`的颜色用来区分你构建失败还是成功，
蓝色是成功，红色是失败；`W`表示你最近几次构建的情况

  ![show-jobs](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-show-jobs.png)  

  除了自动触发构建任务，你也可以手动计划一次构建  

  ![build-list](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-build-list.png)  

  下图为每次测试结果的一个统计图：  

  ![show-test](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-show-test.png)  

  在变更记录中，能看到每次构建中，提交了哪些代码，commit的comments，谁提交的等等  

  ![show-summary](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-show-summary.png)  

  更具体的构建信息可以查看**Console Output** ，如下图：  

  ![show-console](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-show-console.png)  

  让我很舒服的是，当Jenkins自动构建完成之后，我会收到一个邮件提醒  

  ![show-email](http://7xpd26.com1.z0.glb.clouddn.com/jenkins-show-email.png)  

  最后，别让你的系统谁都可以访问，用户权限在`首页`-> `系统管理`->
`Configure Global Security`里配置。
