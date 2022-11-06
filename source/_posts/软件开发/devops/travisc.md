---
title: Travis CI的使用
toc: true
clearReading: true
thumbnailImagePosition: bottom
metaAlignment: center
date: 2021-10-30 20:06:42
thumbnailImage: https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/bg2017121901.png
categories: 其他
tags: DevOps
keywords: travis
excerpt: 由于一直使用travis CI 进行博客的自动化部署，在这里记录一下travis的基本使用方式
---

<!-- toc -->

## 概述

编写代码只是软件开发的一小部分，更多的时间往往花在构建（build）和测试（test），这也是 CI/CD 在最近不断追求的事情

## 持续集成

Travis CI 提供的是持续集成服务（Continuous Integration，简称 CI）。它绑定 Github 上面的项目，只要有新的代码，就会自动抓取。然后，提供一个运行环境，执行测试，完成构建，最终部署到服务器

:question:持续集成指的是只要代码有变更，就自动运行构建和测试，反馈运行结果。确保符合预期以后，再将新代码**集成**到主干

:+1:持续集成的好处在于，每次代码的小幅变更，就能看到运行结果，从而不断累积小的变更，而不是在开发周期结束时，一下子合并一大块代码

## .travis.yml

Travis 要求项目的根目录下面，必须有一个`.travis.yml`文件，这是配置文件，指定了 Travis 的行为。该文件必须保存在 Github 仓库里面，一旦代码仓库有新的 Commit，Travis 就会去找这个文件，执行里面的命令

```yml
language: python # 指定默认运行环境，这里设定使用 Python 环境
script: true # 要运行的脚本，true表示不执行任何脚本，状态直接设为成功
before_install: sudo pip install foo # 在安装依赖之前，需要安装foo模块
```

## 运行流程

Travis 的运行流程很简单，任何项目都会经过两个阶段

1. install 阶段：安装依赖
2. script 阶段：运行脚本

### install 阶段

`install`字段用来指定安装脚本，采用`yaml`格式，所以如果有多个脚本，可以写成下面的形式

```yml
install:
  - command1
  - command2
```

上面代码中，如果`command1`失败了，整个构建就会停下来，不再往下进行，如果不需要安装任何依赖，即可跳过安装阶段，直接设置为`true`

```
install: true
```

### script 字段

`script`字段用来指定构建或测试脚本，与`install`字段相同，如果有多个采用`yml`数组格式进行书写

```yml
script:
  - hexo clean # 解决缓存文件未更新问题
  - hexo generate # generate static files
```

:notes:`script`与`install`不一样，如果`command1`失败，`command2`会继续执行。但是，整个构建阶段的状态是失败，如果`command2`只有在`command1`成功后才能执行，就要写成下面这样

```yml
script: command1 && command2
```

### 部署

`script`阶段结束以后，还可以设置**通知步骤**和**部署步骤**，这两个步骤并不是必须的，部署的脚本可以在`script`阶段执行，也可以使用 Travis 为几十种常见服务提供的快捷部署功能。比如，要部署到 [Github Pages](https://docs.travis-ci.com/user/deployment/pages/)，可以写成下面这样

```yaml
deploy:
  provider: pages
  skip_cleanup: true
  github_token: $GITHUB_TOKEN # Set in the settings page of your repository, as a secure variable
  keep_history: true
  on:
    branch: main
```

### 钩子方法

Travis 为`install`和`script`两个阶段提供了 7 个钩子，能够更加精确的规定每个阶段的构建行为

|      钩子      |         阶段          |
| :------------: | :-------------------: |
| before_install | install 阶段之前执行  |
| before_script  |  script 阶段之前执行  |
| after_failure  | script 阶段失败时执行 |
| after_success  | script 阶段成功时执行 |
| before_deploy  |  deploy 步骤之前执行  |
|  after_deploy  |  deploy 步骤之后执行  |
|  after_script  |  script 阶段之后执行  |

完整的生命周期，从开始到结束是下面的流程

```
before_install
install
before_script
script
aftersuccess or afterfailure
[OPTIONAL] before_deploy
[OPTIONAL] deploy
[OPTIONAL] after_deploy
after_script
```

### 运行状态

Travis 每次运行，可能会返回四种状态

|   状态   |                                   含义                                   |
| :------: | :----------------------------------------------------------------------: |
|  passed  |                    运行成功，所有步骤的退出码都是`0`                     |
| canceled |                               用户取消执行                               |
| errored  | `before_install`、`install`、`before_script`有非零退出码，运行会立即停止 |
|  failed  |                    `script`有非零状态码 ，会继续运行                     |

## 附录

[持续集成服务 Travis CI 教程——阮一峰](http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html)
