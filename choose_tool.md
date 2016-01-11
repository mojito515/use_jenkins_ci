* ###选择构建工具
我简单总结了一下几个开源持续集成工具（详细的比较可以移步[点我](http://cloud.51cto.com/art/201508/487605.htm)）：  

  * **Jenkins**  使用java语言编写 良好的插件环境，支持扩展
  * **Buildbot**   python语言开发的项目   已经为Mozilla、Webkit、Chromium所支持
  * **Travis**     适合新手   提供Saas  接入github账户
  * **Strider**    由node.js+javascript编写    需要安装mongodb和node.js   需要编写脚本  上手困难
  * **Drone**     开源的，支持各种语言的CI工具     但是你的项目必须是开源的 https://drone.io

  综上，公司项目中涉及到ios、android、java三种环境，并且代码并不开
源，所以在持续构建工具中使用**Jenkins**再合适不过。此外Jenkins
有很好地扩展能力，有很健全的插件支持各种环境的代码构建。
Jenkins本身是java实现，在tomcat中就能很好地运行，
这一点比较适合java出身的开发同仁。
