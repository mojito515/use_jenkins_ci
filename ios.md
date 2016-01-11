###<span id="ios">ios项目的自动化构建</span>
**在Mac上安装Jenkins**比较方便，只要从官网上下载dmg安装包就可以。

  注意：如果你Mac上得JDK不是1.7+，安装会失败。  

  卸载Jenkins执行：` /Library/Application Support/Jenkins/Uninstall.command`  

  安装之后，Mac上会多出一个用户：Jenkins，如果你想更改成为你的用户来执行Jenkins，先停掉jenkins，
更改配置文件，再重启jenkins服务（launchctl有点像linux的Service），如下执行：

  `sudo launchctl unload /Library/LaunchDaemons/org.jenkins-ci.plist`  

  `sudo vim +1 +/daemon +'s/daemon/staff/' +/daemon +'s/daemon/bixiaopeng' +wq org.jenkins-ci.plist`  

  `sudo launchctl load /Library/LaunchDaemons/org.jenkins-ci.plist`  


  别忘了修改日志目录的用户权限和主目录的用户权限。

  ios的app构建需要有xcode环境，所以你的部署一定是台mac电脑，但我们做的也是自动构建，所以必然不能使用
xcode可视化工具进行构建，所以我尝试了**xcodebuild**,大概步骤是：build->archive->IPA  

  `xcodebuild -alltargets clean`  

  `xcodebuild -target HelloJenkins PROVISIONING_PROFILE="00000000-0000-0000-0000-000000000000"
  CONFIGURATION_BUILD_DIR=JenkinsBuild`  

  `xcodebuild -scheme HelloJenkins archive PROVISIONING_PROFILE="00000000-0000-0000-0000-000000000000" CODE_SIGN_IDENTITY="iPhone Developer: Justin Hyland (XXXXXXXXXX)" -archivePath ./JenkinsArchive/HelloJenkins.xcarchive`  

  `xcodebuild -exportArchive -exportFormat IPA -exportProvisioningProfile iOS\ Team\ Provisioning\ Profile:\ com.yourAPP.HelloJenkins -archivePath ./JenkinsArchive/HelloJenkins.xcarchive -exportPath ./JenkinsIPAExport/HelloJenkins.ipa`  

  详见：http://www.cnblogs.com/rosepotato/p/3884851.html

  但是构建并没有想象当中的顺利，如果你使用了第三方的jar包，总是会报错：  

  > no provisioning profile matches ‘xxx’

  通过调研，发现xcodebuild可以通过workspace文件构建，可以避开这些问题，
前提是你有scheme文件，跟ios的同时沟通完之后，他每次提交代码都会share scheme
文件上来。构建偶尔成功，但是还是频频报错。  

  `xcodebuild -workspace MyProject.xcworkspace -scheme MyScheme SYMROOT=$(PWD)/build`  

  最后的构建成功是借鉴了刘先宁在InfoQ上的一篇文章（[构建iOS持续集成平台（一）——自动化构建和依赖管理](http://www.infoq.com/cn/articles/build-ios-continuous-integration-platform-part1)）

  实际上是使用到了FaceBook给出的替代xcodebuild的解决方案**xctool**
顿时感觉，我之前就好像一直在用javac编译java代码，而不知道还有maven这个东西。
通过xctool和cocoapod，代码构建成功。  

  `xctool -workspace SDJG.xcworkspace -scheme SDJG clean`  

  `xctool -workspace SDJG.xcworkspace -scheme SDJG build SYMROOT=$(PWD)/JenkinsBuild`  

  `xcrun -sdk iphoneos PackageApplication -v bbbuild/Debug-iphoneos/SDJG.app -o /Users/fangrichird/git/shangde1216/JenkinsIPAExport/SDJG.ipa`  

  ios项目也就只停留在IPA这里了，因为TestFlight自从被苹果收购之后，
再不能通过jenkins的插件完成上传。解决办法停留在手动将IPA发向各种云
服务平台，或者直接用iTunes安装到测试机当中。

  至于jenkins用到的插件和其他环境部署，请参考 [java篇](#java)
