---
tags: 
title: 提高npm install效率
---

采用了华为云 CCE + Jenkins 实现的动态 Agent 方式，搭建了 CICD 平台，在该平台上对前端项目进行打包时，发现效率比 GitLab 的 Runner 效率要低得多，在平台上执行 npm install 要 80s，但是在 Runner 上只需要 6s，对此，该如何继续优化？

<!--more-->

## 1.1 环境说明

采用了华为云 CCE + Jenkins 实现的动态 Agent 方式

## 1.2 解决方案

### 1.2.1 使用缓存

`npm` 会默认缓存已下载的包，以便在未来安装时加快速度。确保缓存机制正常工作，可以提高效率。

- 确保 Jenkins 有一个持久的工作空间来存储 npm 缓存。这通常是 Jenkins 作业的默认行为。
- 可以通过设置环境变量或在 `.npmrc` 文件中指定缓存目录：

    ```shell
    npm config set cache /path/to/your/cache
    ```

- 使用 Jenkins 缓存插件
    如果可能，使用 Jenkins 的缓存插件来缓存 `node_modules` 目录。这将在多次构建间保留已下载的模块，从而加快构建速度。

### 1.2.2 使用 `npm ci` 命令

如果你正在设置自动化构建（比如在 CI/CD 系统中），使用 `npm ci` 命令代替 `npm install` 可以加快安装速度。`npm ci` 会跳过某些面向最终用户的功能，专注于快速、一致地安装依赖。

`npm ci` 命令非常适合在自动化构建（如 Jenkins）中使用。它会忽略 `package-lock.json` 文件的更改，确保依赖的一致性，并且通常比 `npm install` 更快。

在 Jenkins 的构建脚本或 Jenkinsfile 中，你可以替换 `npm install` 命令为：

```shell
npm ci
```
