---
title: DevOps 概述
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 通用技能
tags: DevOps
keywords: DevOps
excerpt: 前几年非常火热的 DevOps 到底是什么？通过本文你将得到答案
date: 2022-01-16 19:16:58
thumbnailImage:
---

<!-- toc -->

## DevOps 概述

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210426001926727.png %}

{% alert success no-icon %}

DevOps 是 Development 和 Operations 的组合，也就是开发和运维的简写，DevOps 是针对企业中的研发人员、运维人员和测试人员的工作理念，是他们在应用开发、代码部署和质量测试等整条生命周期中协作和沟通的最佳实践，DevOps 强调整个组织的合作以及交付和基础设施变更的自动化、从而实现持续集成、持续部署和持续交付

{% endalert %}

目前常用的 DevOps 四大平台:代码托管(gitlab/svn)、项目管理(jira)、运维平台(腾讯蓝鲸/开源平台)、持续交付 ( Jenkins/gitlab)

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210426001133524.png %}

{% alert warning no-icon %}

为什么需要推广 DevOps ？

{% endalert %}

DevOps 强调团队协作、相互协作、持续发展，然而传统的模式是开发人员只顾开发程序，运维人员只负责基础环境管理和代码部署以及监控等，其并不是为了一个共同的目标而共同实现最终的目的，而 DevOps 则实现团队作战，即无论是开发、运维还是测试，都是为了最终的代码发布、持续部署和业务稳定而付出各自的努力，从而实现产品设计、开发、测试和部署的良性循环，实现产品的最终持续交付

:sparkles: DevOps 的优势：

{% alert success no-icon %}

- 速度：应用版本快速的迭代更新，以更好地适应不断变换的市场需求
- 快速交付：更快的将应用交付至生产环境
- 可靠性：保证应用交付的结果是成功的
- 规模：可以在大规模环境下且可靠的交付应用
- 增强合作：建立适应 DevOps 文化模式的团队，开发人员和运维人员协同工作
- 安全性：在快速迭代的同时保证应用的质量

{% endalert %}

### DevOps 的目标是什么？

{% alert success no-icon %}

DevOps 的目标是建立流水线式的准时制（JIT）的业务流程。DevOps 旨在通过合适的准时制业务流程来最大化业务产出，例如增加销售和利润率、提升业务速度，或尽量降低运营成本。

{% endalert %}

DevOps 意味着在业务中建立一条 IT 服务供应链，与其它产品的供应链嵌入业务的方式相同。这种从提供软件交付到供给 IT 服务的模式转变是巨大的。

从架构的角度看，DevOps 需要建立一个自动快速部署系统，有很多方法论和工具可以利用。DevOps 没有统一的实施模板，每个组织都不得不自己考虑并建立 DevOps 流程来提高业务。因此，真正理解 DevOps 的概念，对员工遵循正确的流程有效执行来说是至关重要的。

{% alert warning no-icon %}

DevOps 技术团队进行软件开发流程

{% endalert %}

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210426001322249.png)
![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210426001325079.png)

### 持续集成（CI-Continuous Integration）

{% alert success no-icon %}

CI（Continuous integration），持续集成是指多名开发者在开发不同功能代码的过程中，可以频繁的将代码行合并到一起并相互不影响工作。

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210426002705952.png %}

### 持续部署（CD-Continuous Deployment）

{% alert success no-icon %}

CD（continuous deployment）是基于某种工具或平台实现代码自动化的构建、测试和部署到线上环境以实现交付高质量的产品，持续部署在某种程度上代表了一个开发团队的更新迭代速率

{% endalert %}

### 持续交付（CD-Continuous Delivery）

持续交付是在持续部署的基础之上，将产品交付到线上环境，因此持续交付是产品价值的一种交付，是产品价值的一种盈利的实现

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210426003003240.png %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210426002737559.png %}

### CI/CD 总结

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210426003408743.png %}

使用 Jenkins 进行软件部署的流程如下，分别需要经过测试环境、预发布环境和生产环境。其中，测试环境比较单一，数据量比较简单；预发布环境：正规的代码流程，还有正规的测试人员进行测试，数据和生产环境几乎一致

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210426003509185.png %}

### 常见的部署方式

- 最原始的方案：开发自己上传
- 运维自动手动部署：开发给运维，随后运维手动部署
- 半自动化：运维使用脚本复制部署
- 自动化：结合 Web 界面一键部署

DevOps 就是希望越来越多的软件产品能够做到自动化的部署，这样可以加速软件迭代，快速完成软件需求

## 部署策略

软件最终都是要进行发布，随后部署在某台服务器上，但是不同的部署策略将严重影响到软件开发周期等因素，所以追求更短的迭代周期、更高频的发布，就需要发展出适应的 DevOps 要求的发布策略

### 停机发布

{% alert success no-icon %}

停机发布会在发布以前关闭服务，停止用户访问，然后一次性的升级所有服务。这种发布策略的发布频率往往比较低，且需要在发布之前做好充足的测试

{% endalert %}

:sparkles: 停机发布的特点：
{% alert success no-icon %}

- 所有需要升级的组件被整合到一次发布中
- 一个项目中的大部分应用都会被更新
- 发布之前的研发流程和测试流程往往需要花很长的时间
- 发布时如果出现问题, 修复和回滚的成本很高
- 完成一次停机发布, 需要花费很久的时间, 且需要很多团队在一起才能完成
- 往往需要客户端和服务器端同步升级

{% endalert %}

停机发布并不适合互联网公司，因为两次发布的间隔很久，从功能特性提出到进入市场的时间太长，对市场反应不敏感，会在充分竞争的市场里处于下风。每次发布因为要停机，也会带来经济损失

停机发布的最大优势就是部署简单，不需要考虑新旧版本共存时的兼容性问题；但是仍然存在许多劣势：发布过程中。服务不可用；只能在业务低峰期（往往是夜间）发布，并且需要很多团队在一起工作；这种发布出现错误之后很难进行回滚

{% alert warning no-icon %}

:notes: 这种发布方式适用于开发测试环境；非关键应用，用户影响面小；兼容性比较难管控的场景

{% endalert %}

### 金丝雀发布

{% alert success no-icon %}

金丝雀发布这个术语源自 20 世纪初期，当时英国的煤矿工人在下井采矿之前，会把笼养的金丝雀携带到矿井中，如果矿井中一氧化碳等有毒气体的浓度过高，在影响矿工之前，金丝雀相比人类表现的更加敏感快速，金丝雀中毒之后，煤矿工人就知道该立刻撤离。金丝雀发布是在将整个软件的新版本发布给所有用户之前，先发布给部分用户，用真实的客户流量来测试，以保证软件不会出现严重问题，降低发布风险

{% endalert %}

{% alert info no-icon %}

在实践中，金丝雀发布一般会先发布到一个小比例的机器，比如 2% 的服务器做流量验证，然后从中快速获得反馈，根据反馈决定是扩大发布还是回滚。金丝雀发布通常会结合监控系统，通过监控指标，观察金丝雀机器的健康状况。如果金丝雀测试通过，则把剩余的机器全部升级成新版本，否则回滚代码

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/devops/devops-金丝雀发布.webp %}

金丝雀发布对用户体验影响较小，再金丝雀发布过程中，只有少量用户会受到影响，发布安全能够得到保障；但是由于金丝雀的机器数量比较少，有一些问题并不能有效的暴露出来

{% alert warning no-icon %}

:notes: 金丝雀发布适用于监控比较完备且于发布系统集成的环境

{% endalert %}

### 灰度/滚动发布

{% alert success no-icon %}

灰度发布是金丝雀发布的延伸，是将发布分成不同的阶段/批次，每个阶段/批次的用户数量逐级增加。如果新版本在当前阶段没有发现问题，就再增加用户数量进入下一个阶段，直至扩展到全部用户

{% endalert %}
灰度发布可以减小发布风险，是一种零宕机时间的发布策略。它通过切换线上并存版本之间的路由权重，逐步从一个版本切换为另一个版本。整个发布过程会持续比较长的时间, 在这段时间内，新旧代码共存，所以在开发过程中，需要考虑版本之间的兼容性，新旧代码共存不能影响功能可用性和用户体验。当新版本代码出现问题时，灰度发布能够比较快的回滚到老版本的代码上

结合特性开关等技术，灰度发布可以实现更复杂灵活的发布策略

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/devops/devops-滚动发布.webp %}

{% alert warning no-icon %}

金丝雀发布、灰度发布步骤组成：

{% endalert %}

1. 准备好部署各个阶段的工件，包括:构建工件，测试脚本，配置文件和部署清单文件
2. 从负载均衡列表中移除掉「 金丝雀 」服务器
3. 升级「 金丝雀 」应用(排掉原有流量并进行部署)
4. 对应用进行自动化测试
5. 将 「 金丝雀 」服务器重新添加到负载均衡列表中(连通性和健康检查)
6. 如果 「 金丝雀 」在线使用测试成功，升级剩余的其他服务器。(否则就回滚) 灰度发布可以保证整体系统的稳定，在初始灰度的时候就可以发现、调整问题，以保证其影响度。

灰度发布对用户体验影响比较小, 不需要停机发布、并且能够控制发布风险。但是发布时间会比较长，需要复杂的发布系统和负载均衡器。在发布完成之后还需要考虑新旧版本共存时的兼容性

{% alert warning no-icon %}

:notes: 灰度发布适用于可用性较高的生产环境发布

{% endalert %}

### 蓝绿发布

{% alert success no-icon %}

蓝绿部署是指有两个完全相同的、互相独立的生产环境，一个叫做“蓝环境”，一个叫做“绿环境”。其中，绿环境是用户正在使用的生产环境。当要部署一个新版本的时候，先把这个新版本部署到蓝环境中，然后在蓝环境中运行冒烟测试，以检查新版本是否正常工作。如果测试通过，发布系统更新路由配置，将用户流量从绿环境导向蓝环境，蓝环境就变成了生产环境。这种切换通常在一秒钟之内就能搞定。如果出了问题，把路由切回到绿环境上，再在蓝环境中调试，找到问题的原因。因此，蓝绿部署可以做到仅仅一次切换，立刻就向所有用户推出新版本，新功能对所有用户立刻生效可见

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/devops/devops-蓝绿发布.webp %}

:+1: 蓝绿发布优势：

- 升级切换和回退速度非常快
- 零停机时间

:persevere: 蓝绿发布的不足

- 一次性的全量切换，如果发布出现问题, 会对用户产生比较大的影响
- 需要两倍的机器资源
- 需要中间件和应用自身支持热备集群的流量切换
  {% alert warning no-icon %}

:notes: 蓝绿发布适用于机器资源比较富余或者按需分配 (背靠云厂商)
{% endalert %}

### A/B 测试

{% alert success no-icon %}

A/B 测试和灰度发布非常像，可以从发布的目的上进行区分。AB 测试侧重的是根据 A 版本和 B 版本的差异进行决策，最终选择一个版本进行部署。和灰度发布相比，AB 测试更倾向于去决策，和金丝雀发布相比，AB 测试在权重和流量的切换上更灵活

{% endalert %}
举个例子，某功能有两个实现版本 A 和 B，通过细粒度的流量控制，把 50% 的用户总是引导到 A 实现上，把剩下的 50% 用户总是引导到 B 实现上，通过比较 A 实现和 B 实现的转化率，最终选择转化率较高的 A 实现作为功能的最终版本

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/devops/devops-ab测试.webp %}

:+1: AB 测试发布的优势：

- 快速实验能力
- 用户体验影响小
- 可以使用生产环境流量做测试
- 可以针对某些特定用户做测试
  :persevere: AB 测试发布的不足：

- 需要较为复杂的业务流量识别和控制能力
- 需要考虑较为复杂的新旧版本兼容性问题

{% alert warning no-icon %}

:notes: AB 测试发布适用于做业务探索和创新测试的场景，常常用来对多个方案进行决策
{% endalert %}

### 流量隔离环境发布

{% alert success no-icon %}

在上述的发布策略中，发布的单位都是应用，但是一个功能模块往往是由多个应用组合在一起提供的服务，即使当前发布的应用出现了异常，这个异常也未必体现在当前应用中，在复杂的情况下，异常会延迟到它的下游应用才体现出来，如何发现此类问题并且不影响用户体验是非常重要的。此外，我们有时候还希望新版本的代码上线以后，只影响到一小部分用户。而传统的灰度发布，因为无法识别业务流量，所以即使某个应用只有一台机器出现了问题，也可能会影响到所有的用户。

{% endalert %}

如下图左侧的灰度发布，App1 的所有机器都有一定概率会路由到出现问题的红色 App2 机器上。而右侧的隔离环境发布中，新版本的代码会先发布在全链路隔离环境中，即使发布中出现问题，也只会影响少量用户。

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/devops/devops-流量隔离环境发布.webp %}

:+1: 流量隔离环境发布优势:

- 能够发现一些复杂的, 涉及到多应用的问题
- 出现故障时, 只会影响很小一部分用户

:persevere: 流量隔离环境发布的不足:

- 需要对流量隔离环境进行独立监控
- 系统设计复杂, 需要中间件和链路上的所有应用能够识别业务流量

{% alert warning no-icon %}

:notes: 流量隔离环境发布只适用于较为核心的生产业务场景
{% endalert %}

## 附录

[redhat]: https://www.redhat.com/zh/topics/devops/devops-engineer

[阿里巴巴 devops 发布技术策略](https://mp.weixin.qq.com/s/FgQgg9wANAvzNyJ4VmFmTQ)
[蓝绿部署、红黑部署、AB 测试、灰度发布、金丝雀发布、滚动发布的概念与区别](https://cloud.tencent.com/developer/article/1529300)
