- ###<span id="android">Android项目的自动化构建</span>

  **Android的集成部署**相对来说比较简单，跟我们的android工程师沟通，他们习惯使用
Android studio这种集成的ide，如果部署，有集成在IDE中的Gradle。  

  所以想要在linux上构建Android代码，只需要两件事情：

  1.安装Android SDK；  
  2.配置Gradle环境；(JDK环境就不用说了吧)

  other和[java篇](#java)的配置相同，只是再需要安装一个Gradle插件，如果
你需要向不同的应用市场打包，在Gradle的配置文件中配置就好了。
