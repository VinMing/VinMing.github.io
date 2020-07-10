---
layout:     post
title:      Git Commit Message And Branching Model
subtitle:    "\"Git Commit Message And Branching Model\""
date:       2020-03-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Git
---
## 前言

本文参考业界实践对 Git 提交记录格式和分支模型所做的总结。

描述Commit Message提交记录格式和Branching Model分支模型，并介绍两个工具 commitizen 和 gitflow，分别处理维护提交记录格式和分支切换的工作。

## 环境
#### 系统环境
```text
Distributor ID:	Deepin
Description:	Deepin 20 Beta
Release:	20 Beta
Codename:	n/a
Linux version :     5.3.0-3-amd64 (debian-kernel@lists.debian.org)
Gcc version:        8.3.0 (Debian 8.3.0-6)
```
#### 软件信息
```text
version : 	
     None
```

## 正文

### Commit Message

在 Git Style 中已经介绍了提交记录（Commit Message）的格式，但是没有说明为什么要遵循这样的约定。事实上，这个格式参考了 [AngularJS’s commit message convention](https://github.com/angular/angular.js/blob/f3377da6a748007c11fde090890ee58fae4cefa5/CONTRIBUTING.md#commit)，而 AngularJS 制定这样的约定是出于几个目的

- 自动生成 CHANGELOG.md
- 识别不重要的提交
- 为浏览提交历史时提供更好的信息

后面简称 AngularJS’s commit message convention 为 conventional message。

#### git commit 格式约定

基本规约：50/72格式化

    第一行不超过50个字符
    然后是空白行
    其余文字应以72个字符换行

原因：

    一些工具将第一行作为主题行，将第二段作为正文(类似于电子邮件)。若工具流行，那么该格式将流行。
    git log不处理换行，因此如果行太长则很难阅读。
    git format-patch --stdout 可以将提交转换为电子邮件。
##### 格式

**Conventional message 格式**

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

`type` 则定义了此次变更的类型，只能使用下面几种类型

- feat：增加新功能
- fix：问题修复
- docs：文档变更
- style：代码风格变更（不影响功能）
- refactor：既不是新功能也不是问题修复的代码变更
- perf：改善性能
- test：增加测试
- chore：开发工具（构建，脚手架工具等）

`subject` 是对变更的简要描述。

`body` 是更为详细的描述。

`footer` 可以包含 Breaking Changes 和 Closes 信息。

#### CHANGELOG

通过 `git log` 可以生成 CHANGELOG.md 中版本间新增功能，

```sh
$ git log --grep feat --pretty=format:%s

feat: add "type" tag to distinguish client and server span
feat: instrument via Module._load hook
```

也可以同时获取修复的问题，

```sh
$ git log --grep 'feat\|fix' --pretty=format:%s

fix: wrapper function not returned
feat: add "type" tag to distinguish client and server span
feat: instrument via Module._load hook
```

**定位错误**

使用 `git bisect` 可以定位引入问题的提交，通过 `type` 可以快速辨别不会引入 bug 的提交，

```sh
(master) $ git bisect start
(master) $ git bisect bad
(master) $ gco v0.1.0
(7f04616) $ git bisect good

Bisecting: 15 revisions left to test after this (roughly 4 steps)
[433f58f26a53b519ab7fc52927895e67eb068c64] docs: how to use agent

(433f58f) $ git bisect good

Bisecting: 7 revisions left to test after this (roughly 3 steps)
[02894fb497f41eecc7b21462d3b020687b3aaee2] docs(CHANGELOG): 1.1.0

(02894fb) $ git bisect good

Bisecting: 3 revisions left to test after this (roughly 2 steps)
[9d15ca2166075aa180cd38a3b4d3be22aa336575] chore(release): 1.1.1

(9d15ca2) $ git bisect good

Bisecting: 1 revision left to test after this (roughly 1 step)
[f024d7c0382c4ff8b0543cbd66c6fe05b199bfbc] fix: wrapper function not returned

(f024d7c) $ npm test
...
```

因为 docs 或 chore 不会引入 bug，所以可以直接执行 `git bisect good`。

使用 `git bisect skip` 可以直接过滤掉这些提交，

```sh
$ git bisect skip $(git rev-list --grep 'style\|docs\|chore' v0.1.0..HEAD)
```

#### Commitizen

命令行工具 [commitizen](https://github.com/commitizen/cz-cli) 帮助开发者生成符合 conventional message 的提交记录。

https://cattail.me/tech/2016/06/06/git-commit-message-and-branching-model.html



##### 安装：全局

```sh
$ sudo npm install -g commitizen cz-conventional-changelog conventional-changelog-cli husky
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
/usr/local/bin/commitizen -> /usr/local/lib/node_modules/commitizen/bin/commitizen
/usr/local/bin/git-cz -> /usr/local/lib/node_modules/commitizen/bin/git-cz
+ cz-conventional-changelog@3.2.0
+ commitizen@4.1.2
added 9 packages from 6 contributors, updated 6 packages and moved 36 packages in 9.39s
```

commitizen安装在/usr/local/lib/node_modules/文件夹中

##### 检查

```sh
$ npm ls -g -depth=0
/usr/local/lib
├── commitizen@4.1.2
├── conventional-changelog@3.1.21
├── conventional-changelog-cli@2.0.34
├── husky@4.2.5
```



#### Commitizen适配器

如果需要在项目中使用commitizen生成符合AngularJS规范的提交说明，初始化cz-conventional-changelog适配器：

cd到自己项目的`.git`所在目录  

```sh
commitizen init cz-conventional-changelog --save --save-exact
```

##### 可能报错：

如果当前已经有其他适配器被使用，则会报以下错误，此时可以加上--force选项进行再次初始化 

```sh
Error: A previous adapter is already configured. Use --force to override
```

或者

```sh
$ commitizen init cz-conventional-changelog --save --save-exact --force
Attempting to initialize using the npm package cz-conventional-changelog
npm WARN using --force I sure hope you know what you are doing.
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN saveError ENOENT: no such file or directory, open '/your/project/package.json'
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN enoent ENOENT: no such file or directory, open '/your/project/package.json'
npm WARN common No description
npm WARN common No repository field.
npm WARN common No README data
npm WARN common No license field.
Error: ENOENT: no such file or directory, open '/your/project/package.json'
    at Object.openSync (fs.js:458:3)
    at Object.readFileSync (fs.js:360:35)
    at addPathToAdapterConfig (/usr/local/lib/node_modules/commitizen/dist/commitizen/adapter.js:1256:62)
    at init (/usr/local/lib/node_modules/commitizen/dist/commitizen/init.js:1033:5)
    at Object.bootstrap (/usr/local/lib/node_modules/commitizen/dist/cli/commitizen.js:34:30)
    at Object.<anonymous> (/usr/local/lib/node_modules/commitizen/bin/commitizen.js:2:38)
    at Module._compile (internal/modules/cjs/loader.js:1138:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1158:10)
    at Module.load (internal/modules/cjs/loader.js:986:32)
    at Function.Module._load (internal/modules/cjs/loader.js:879:14) {
  errno: -2,
  syscall: 'open',
  code: 'ENOENT',
  path: '/your/project/package.json'
}
```

缺少your/project/package.json这个文件，

```sh
npm init --yes
```

并项目生成的package.json新添加

```json
......... 
  "license": "ISC",  
 // 新添加
  "devDependencies": {
    "cz-conventional-changelog": "^3.2.0"
  },
  "config": {
    "commitizen": {
      "path": "./node_modules/cz-conventional-changelog"
    }
  },
```

##### 检查

```sh
$ git add .
$ git cz
cz-cli@4.1.2, cz-conventional-changelog@3.2.0

? Select the type of change that you're committing: (Use arrow keys)
❯ feat:     A new feature 
  fix:      A bug fix 
  docs:     Documentation only changes 
  style:    Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc) 
  refactor: A code change that neither fixes a bug nor adds a feature 
  perf:     A code change that improves performance 
  test:     Adding missing tests or correcting existing tests 
(Move up and down to reveal more choices)

```

然后进入到你要操作的项目目录，执行

```sh
conventional-changelog -p angular -i CHANGELOG.md -s
```

此时项目中多了CHANGELOG.md文档，表示生成 Change log成功了。以后，凡是用到git commit 命令的时候统一改为git cz,然后就会出现选项，生成符合格式的Commit Message。

可能你会遇到：一个这样的错误：

```sh
Could not resolve /usr/local/lib/node_modules/cz-conventional-changelog. Cannot find module '/usr/local/lib/node_modules/cz-conventional-changelog'
Require stack:
- /usr/local/lib/node_modules/commitizen/dist/commitizen/adapter.js
- /usr/local/lib/node_modules/commitizen/dist/commitizen.js
- /usr/local/lib/node_modules/commitizen/dist/cli/git-cz.js
- /usr/local/lib/node_modules/commitizen/bin/git-cz.js
- /usr/local/lib/node_modules/commitizen/bin/git-cz
```

只需做个软连接即可：

```sh
sudo ln -s /home/stephen/node_modules /usr/local/lib/node_modules
```



### Commitizen校验

 commitlint 校验提交说明是否符合规范，安装校验工具commitlint： 

#### 安装
```bash
npm install -g @commitlint/cli @commitlint/config-conventional
```

#### 配置

```bash
echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js
```



#### 安装huksy（git钩子工具）
```sh
npm install husky --save-dev 
```
在package.json中添加配置git commit提交时的校验钩子： 
```json
{   ....
    "husky": {  
        "hooks": {    
            "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"  
        }   
    }
}
```
 需要注意，使用该校验规则不能对.cz-config.js进行不符合Angular规范的定制处理，例如之前的汉化，此时需要将.cz-config.js的文件按照官方示例文件[cz-config-EXAMPLE.js](https://github.com/leoforfree/cz-customizable/blob/master/cz-config-EXAMPLE.js)进行符合Angular风格的改动。





#### 应用

除此之外，commitizen 还依据 conventional message，创建起一个生态

- [conventional-changelog-cli](https://github.com/conventional-changelog/conventional-changelog-cli)：通过提交记录生成 CHANGELOG.md
- [conventional-github-releaser](https://github.com/conventional-changelog/conventional-github-releaser)：通过提交记录生成 github release 中的变更描述
- [conventional-recommended-bump](https://github.com/conventional-changelog/conventional-recommended-bump)：根据提交记录判断需要升级 [Semantic Versioning](http://semver.org/) 哪一位版本号
- [validate-commit-msg](https://github.com/kentcdodds/validate-commit-msg)：检查提交记录是否符合约定

使用这些工具可以简化 npm 包的发布流程，

```
#! /bin/bash

# https://gist.github.com/stevemao/280ef22ee861323993a0
# npm install -g commitizen cz-conventional-changelog trash-cli conventional-recommended-bump conventional-changelog-cli conventional-commits-detector json
trash node_modules &>/dev/null;
git pull --rebase &&
npm install &&
npm test &&
cp package.json _package.json &&
preset=`conventional-commits-detector` &&
echo $preset &&
bump=`conventional-recommended-bump -p angular` &&
echo ${1:-$bump} &&
npm --no-git-tag-version version ${1:-$bump} &>/dev/null &&
conventional-changelog -i History.md -s -p ${2:-$preset} &&
git add History.md &&
version=`cat package.json | json version` &&
git commit -m"docs(CHANGELOG): $version" &&
mv -f _package.json package.json &&
npm version ${1:-$bump} -m "chore(release): %s" &&
git push --follow-tags &&
npm publish
```

运行上述脚本会更新 CHANGELOG.md、升级版本号并发布新版本到 npm，所有这些操作都基于提交记录自动处理。



### Branching Model

[Vincent Driessen](http://nvie.com/about/) 的分支模型（Branching Model）介绍 Git 分支和开发，部署，问题修复时的工作流程，

![Image text](/img/git_workflow.png)

> Author: Vincent Driessen Original blog post:  http://nvie.com/posts/a-succesful-git-branching-model License: Creative  Commons BY-SA

在整个开发流程中，始终存在 master 和 develop 分支，其中 master 分支代码和生产环境代码保持一致，develop 分支还包括新功能代码。

**分支模型主要涉及三个过程：功能开发，代码发布和问题修复**。

**功能开发**
```
1. 从 develop 创建一个新分支（feature/*）
2. 功能开发
3. 生产环境测试
4. Review
5. Merge 回 develop 分支
```
**代码发布**
```
需要发布新功能到生产环境时

1. 从 develop 创建新分支（release/*）
2. 发布 feature 分支代码到预上线环境
3. 测试并修复问题
4. Review
5. 分别 merge 回 develop 和 master 分支
6. 发布 master 代码到生产环境
```
**问题修复**

当生产环境代码出现问题需要立刻修复时
```
1. 从 master 创建新分支（hotfix/*）
2. 发布 hotfix 代码到预上线环境
3. 修复问题并测试
4. Review
5. 分别 merge 会 develop 和 master 分支
6. 发布 master 代码到生产环境
```
该分支模型值得借鉴的地方包括，

- **规范的分支命名**
- **将分支和代码运行环境关联起来**

分支和代码运行环境的关系是这样的，

- master => 生产环境
- release/*，hotfix/* => 预上线环境
- feature/*，develop => 开发环境

####  gitflow

Vincent Driessen 的分支模型将开发流程和Git分支很好的结合起来，但在实际使用中涉及复杂的分支切换，[gitflow](https://github.com/nvie/gitflow) 可以简化这些工作。

安装并在代码仓库初始化 gitflow 后，就可以使用它完成分支工作流程，

```
$ git flow init

No branches exist yet. Base branches must be created now.
Branch name for production releases: [master]
Branch name for "next release" development: [develop]

How to name your supporting branch prefixes?
Feature branches? [feature/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []
```

**功能开发**

开始开发时

```
(develop) $ git flow feature start demo

Switched to a new branch 'feature/demo'

Summary of actions:
- A new branch 'feature/demo' was created, based on 'develop'
- You are now on branch 'feature/demo'
```

完成开发后

```
(feature/demo) $ git flow feature finish demo

Switched to branch 'develop'
Already up-to-date.
Deleted branch feature/demo (was 48fbada).

Summary of actions:
- The feature branch 'feature/demo' was merged into 'develop'
- Feature branch 'feature/demo' has been removed
- You are now on branch 'develop'
```

**代码发布**

发布代码前

```
(develop) $ git flow release start demo

Switched to a new branch 'release/demo'

Summary of actions:
- A new branch 'release/demo' was created, based on 'develop'
- You are now on branch 'release/demo'
```

测试完成准备上线时

```
(release/demo) $ git flow release finish demo

Switched to branch 'master'
Deleted branch release/demo (was 48fbada).

Summary of actions:
- Latest objects have been fetched from 'origin'
- Release branch has been merged into 'master'
- The release was tagged 'demo'
- Release branch has been back-merged into 'develop'
- Release branch 'release/demo' has been deleted
```

发布 master 代码到线上环境

**问题修复**

发现线上故障时，

```
(master) $ git flow hotfix start demo-hotfix

Switched to a new branch 'hotfix/demo-hotfix'

Summary of actions:
- A new branch 'hotfix/demo-hotfix' was created, based on 'master'
- You are now on branch 'hotfix/demo-hotfix'
```

修复问题后

```
(hotfix/demo-hotfix) $ git flow hotfix finish demo-hotfix

Deleted branch hotfix/demo-hotfix (was 48fbada).

Summary of actions:
- Latest objects have been fetched from 'origin'
- Hotfix branch has been merged into 'master'
- The hotfix was tagged 'demo-hotfix'
- Hotfix branch has been back-merged into 'develop'
- Hotfix branch 'hotfix/demo-hotfix' has been deleted
```

## 后记

@[TOC](这里写自定义目录标题)


