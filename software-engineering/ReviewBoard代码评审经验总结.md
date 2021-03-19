## 代码评审

代码评审（CodeReview），顾名思义是对代码进行评审，是软件工程的活动之一。 通过代码评审可以保证代码质量，促进团队知识共享……好处多多。

## 版本控制与代码评审

软件工程的各个活动总是离不开工具的支持。 代码评审工具首先必须和版本控制工具相结合的。 现在主流的两种版本控制工具：SVN和GIT。

GIT有个Google开发的代码评审工具Gerrit，可以在提交前进行代码评审，评审通过之后才允许提交到版本库。 其次，代码托管平台GitLab（号称是GitHub的开源实现）也可以用来进行代码评审。 如果版本控制工具是GIT，当然优先选择用Gerrit或者GitLab来尝试做代码评审了。

但是如果版本控制工具是SVN呢？这目前还没有发现很好的解决方案。 所以问题来了，在技术选型上，该选择什么工具来进行代码评审呢？

## 代码评审工具选型

关于代码评审，有很多支持工具，可以查看： 简单实用的Code Review工具

开源的代码评审工具有： ReviewBoard、 Facebook Phabricator、 Codestriker、 Groogle、 Rietveld、 JCR（Java Code Reviewer）、 Jupiter、 ReviewClipse

商业版的代码评审工具有： Atlassian Crucible、 Jetbrains Upsource 曾了解过上述大多数工具的使用，曾试过Crucible、Jupiter、ReviewBoard，最终综合考量（如：流行度、易用度、文档完善度）选择了ReviewBoard。

## ReviewBoard简介

ReviewBoard是个开源的、可扩展的、友好的基于Web的代码评审工具，是用Python框架Django开发的。
ReviewBoard的官方网站：https://www.reviewboard.org，其title为： Take the pain out of code review | Review Board
Take the pain out of code review 可以翻译为：从代码评审的痛苦中解脱出来
ReviewBoard的源码托管在GitHub上： https://github.com/reviewboard/reviewboard
ReviewBoard的源码也是通过ReviewBoard来进行评审的： https://reviews.reviewboard.org/
ReviewBoard的DEMO： http://demo.reviewboard.org/，可以通过DEMO简单体验下ReviewBoard的基本使用

### ReviewBoard官方指南介绍

要了解ReviewBoard，最好的方式莫过于阅读官方指南： https://www.reviewboard.org/docs/，ReviewBoard的官方指南有： User Guide（用户指南）， Administration Guide（管理员指南），Web API Guide（Web API指南），Extending Review Board（扩展ReviewBoard）和 Frequently Asked Questions（常见问答FAQ）。 用户指南的提纲：开始（包括代码评审的介绍、一般工作流、账户设置）、使用评审请求（评审请求的创建、修改、发布、关闭等）、评审、搜索、使用MarkDown。 管理员指南的提纲：安装、升级、优化、管理员UI、配置、扩展和站点管理。 Web API是RESTful架构，使得ReviewBoard可以用各种编程语言来集成。

### ReviewBoard安装及创建站点

ReviewBoard的安装在互联网上有很多博文分享，笔者的建议是 以官方指南为准，同时可以参考互联网上的博文分享 例如，2.0版本在linux下安装指南： https://www.reviewboard.org/docs/manual/2.0/admin/installation/linux/ 在安装完之后，是创建ReviewBoard站点： https://www.reviewboard.org/docs/manual/2.0/admin/installation/creating-sites/ 可以创建多个ReviewBoard站点 笔者安装过程中曾出现的问题及解决方式如下： python连接mysql时 出现DeprecationWarning: the sets module is deprecated 警告 SVN出现“error while loading shared libraries”错误 AttributeError: 'module' object has no attribute 'HAVE_DECL_MPZ_POWM_SEC' Reviewboard时区问题

### 使用ReviewBoard进行代码评审

代码评审（CodeReview）一般有两种形式：pre-commit-review，post-commit-review。 pre-commit-review是指代码提交到代码库前进行代码评审； post-commit-review是指代码提交到代码库后进行代码评审。 ReviewBoard同时支持以上两种形式，代码的评审主要通过ReviewRequest（评审请求）来进行的。 其中pre-commit-review的工作流为： 在代码修改后，提交人创建代码评审请求 相应的评审人通过评审请求对代码进行评审，如果评审不通过，提交人可以更新该评审请求 评审通过之后，提交人将代码提交至版比库 当然，笔者始终认为代码评审的最好方式是提交前评审，这样能够很好的保证提交到版本库的代码都是经过评审的。

使用ReviewBoard客户端或Eclipse插件 在Web界面创建/更新评审请求的过程是比较繁琐的，好在有相应的工具简化了这个过程： RBtools是ReviewBoard官方提供的命令行客户端，可以使用命令行进行评审请求的相关操作； eReviewBoard是ReviewBoard的Eclipse插件； TaoReviewBoard是淘宝开发的ReviewBoard的Eclipse插件。 笔者根据使用经历，整理出如下eReviewBoard和TaoReviewBoard功能对比表格：

![image](https://note.youdao.com/src/87031D61CCDB4BE385780512C238732D?ynotemdtimestamp=1616140822101)

### SVN与ReviewBoard集成，实现post-commit-review

曾经尝试过用pre-commit-review进行代码评审，在实施或推广之时，遇到如下问题： 代码提交人在评审请求通过之后还需要再提交代码至版本库，同时无法确保被评审的代码和提交的代码的一致性 没有实现在代码评审请求评审通过后自动提交代码（以提交人的账号）至版本库（如同Gerrit那样） 总之，还没有类似Gerrit那样的成熟方案

所以，选择了post-commit-review，关于post-commit-review，可以参考如下文档： svn post-commit脚本样例： reviewboard源码中用户贡献的样例 rbt post 命令官方指南 svn集成ReviewBoard，让post-commit hook后台运行