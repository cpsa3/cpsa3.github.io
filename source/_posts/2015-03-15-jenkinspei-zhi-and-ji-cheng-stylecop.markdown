---
layout: post
title: "Jenkins配置&amp;集成StyleCop"
date: 2015-03-15 22:14:49 +0800
comments: true
categories: ['持续集成']
---
##概要
* 安装
* 系统配置
* 项目配置
   * 源码管理
   * 构建触发器
   * 构建项目
* 邮件配置
* 集成StyleCop

##正文

###安装
网上有教程，略过...

###系统配置

配置MSBuild（后续项目构建中需要使用）：

![这里写图片描述](http://img.blog.csdn.net/20150213145641618)

###项目配置
1.新建项目：
![这里写图片描述](http://img.blog.csdn.net/20150213145724596)

2.选择项目类型：
![这里写图片描述](http://img.blog.csdn.net/20150213145834019)

3.进入项目配置页面
![这里写图片描述](http://img.blog.csdn.net/20150213145907575)

4.源码管理
![这里写图片描述](http://img.blog.csdn.net/20150213145932301)

5.项目构建
![这里写图片描述](http://img.blog.csdn.net/20150213145957729)

6.首先使用MSBuild命名将项目编译后的文件放到临时目录，然后再用batch command将临时目录复制到iis指向的目录。
![这里写图片描述](http://img.blog.csdn.net/20150213150101392)

7.配置触发器
日程表表达式可以参考说明，0 * * * * （表示每个整点运行项目构建）
![这里写图片描述](http://img.blog.csdn.net/20150213150130814)

8.构建项目
![这里写图片描述](http://img.blog.csdn.net/20150213150215820)

构建信息：
图中标示的为本次构建的svn revision
![这里写图片描述](http://img.blog.csdn.net/20150213150339527)

本次构建的所有变更记录
![这里写图片描述](http://img.blog.csdn.net/20150213150359529)

###邮件配置
1.进入系统配置页面配置邮件发送的SMTP
![这里写图片描述](http://img.blog.csdn.net/20150213150443615)

2.进入项目配置页面，配置邮件通知：（每次不稳定构建时会邮件通知）
![这里写图片描述](http://img.blog.csdn.net/20150213150503489)

###集成StyleCop
1.首先在build机器上安装StyleCop（4.7）

2.为了避免所有开发机强依赖StyleCop，这里使用MSBuild.Extension.Pack （4.0.9.0）

3.在项目根目录下，创建目录并复制所有的文件及dll（其中StyleCop.CSharp.dll,StyleCop.CSharp.Rules.dll,StyleCop.dll 是StyleCop安装目录下的3个dll；MSBuild.ExtensionPack.StyleCop.dll,MSBuild.ExtensionPack.tasks是MSBuild.Extension.Pack安装目录下；CodeQuailty.targets是MsBuild需要识别的target文件）
![这里写图片描述](http://img.blog.csdn.net/20150213150600239)

4.CodeQuailty.targets
```xml
  <Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003" DefaultTargets="Measure">
  <!--<Import Project="$(MSBuildStartupDirectory)\MSBuild.ExtensionPack.tasks"/>-->
  <UsingTask AssemblyFile="$(MSBuildStartupDirectory)\MsBuildCodeQuality\MSBuild.ExtensionPack.StyleCop.dll" TaskName="MSBuild.ExtensionPack.CodeQuality.StyleCop"/>
  <PropertyGroup>
    <OutDir>$(MSBuildStartupDirectory)</OutDir>
  </PropertyGroup>
  <Target Name="Measure">
    <Message Text="Measure called." />
    <CreateItem Include="$(MSBuildStartupDirectory)\Neo.Web\**\*.cs">
      <Output TaskParameter="Include" ItemName="StyleCopFiles"/>
    </CreateItem>
    
    <MSBuild.ExtensionPack.CodeQuality.StyleCop
          TaskAction="Scan"
          ShowOutput="true"
          ForceFullAnalysis="true"
          CacheResults="false"
          SourceFiles="@(StyleCopFiles)"
          logFile="$(OutDir)\MsBuildCodeQuality\StyleCopLog.txt"
          SettingsFile="$(MSBuildStartupDirectory)\Neo.Web\Settings.StyleCop"
          ContinueOnError="false">
          <Output TaskParameter="Succeeded" PropertyName="AllPassed"/>
          <Output TaskParameter="ViolationCount" PropertyName="Violations"/>
          <Output TaskParameter="FailedFiles" ItemName="Failures"/>
    </MSBuild.ExtensionPack.CodeQuality.StyleCop>
    <Message Text="Succeeded: $(AllPassed), Violations: $(Violations)" />
  </Target>
</Project>
```

5.Jenkins中安装插件Violations（可用于展示StyleCop运行结果）

![这里写图片描述](http://img.blog.csdn.net/20150213150641457)
![这里写图片描述](http://img.blog.csdn.net/20150213150658883)

6.进入项目配置页面，增加构建步骤，使用MSBuild运行上述的CodeQuailty.targets
![这里写图片描述](http://img.blog.csdn.net/20150213150802450)

7.在项目配置页面，增加构建后步骤，开启Report Violations
![这里写图片描述](http://img.blog.csdn.net/20150213150821672)

配置StyleCop运行结果的xml路径
![这里写图片描述](http://img.blog.csdn.net/20150213150841780)

8.查看build后StyleCop结果
![这里写图片描述](http://img.blog.csdn.net/20150213150859518)


###参考
* http://blog.codeinside.eu/2010/12/15/howto-msbuild-stylecop/
* https://ferritedog.wordpress.com/2011/05/27/1-hour-guide-to-continuous-integration-setup-jenkins-meets-net/
* https://wiki.jenkins-ci.org/display/JENKINS/Violations