---
tags:
  - Jenkins
  - 解决方案
title: Jenkins作业日志太大导致Jenkins崩溃
---

![image.png](https://oss.puppetdev.top/image/note/d7d95225cef35da6a867289e072bb864.png)

Jenkins 流水线运行时由于某些原因，Console Output 日志太大了，导致 Jenkins 主节点内存溢出，程序崩溃。

<!--more-->

## 1.1 环境说明

- Jenkins 2.426
- Docker 方式部署

## 1.2 解决方案

我们的最终核心目的是避免 Jenkins 崩溃，因此想到了对 Console Output 日志的大小进行限制。当超过指定大小的时候，自动终止任务，并报错提示。

目前已知的，可以实现预期效果的方案有两种：

- 借助已有插件：Log Size Checker，安装后，在 Build Environment 中配置，设置 Set maximum log size，并设置超过指定大小时的策略，例如 Fail the build
- 借助 groovy 脚本
    - 在构建过程中添加一个步骤来运行 Groovy 脚本。
    - 编写脚本定期检查日志文件的大小，并在超过指定阈值时终止构建，并发送通知。

**优劣分析**

- 使用插件，插件的可用性和功能可能会随着 Jenkins 和插件版本的更新而变化。
- 使用 Groovy 脚本可以提供更高的灵活性，但需要对 Jenkins 和 Groovy 有一定的掌握。
- 在限制日志大小时，要确保设置的大小足够记录必要的信息，以便于后续的问题诊断和分析。

所以最终选择采用 Groovy 脚本来进行编写。

### 1.2.1 Groovy 脚本实现对构建日志的限制

```Groovy
import groovy.json.JsonOutput

node {
    try {
        // 设置日志大小限制（例如：30MB）
        def logSizeLimit = 30 * 1024 * 1024

        stage('Example') {
            // 示例步骤
            echo 'Running some steps'

            // 实时检查日志大小
            checkLogSize(logSizeLimit)
        }

        // 其他阶段...

    } catch (e) {
        // 发送钉钉通知并终止构建
        sendDingTalkNotification("Jenkins Job FAILED: Log Size Limit Exceeded")
        throw e
    }
}

// 函数：检查日志大小
def checkLogSize(logSizeLimit) {
    // 检查日志大小的逻辑
    // ...

    if (/* 日志大小超过限制 */) {
        sendDingTalkNotification("Jenkins Job WARNING: Log Size Approaching Limit")
        error "Terminating build due to log size limit exceeded."
    }
}

// 函数：发送钉钉通知
def sendDingTalkNotification(String message) {
    def dingTalkWebhookUrl = 'https://oapi.dingtalk.com/robot/send?access_token=your_token'
    def payload = JsonOutput.toJson([
        "msgtype": "text",
        "text": [
            "content": message
        ]
    ])

    sh "curl -H 'Content-Type: application/json' -d '${payload}' ${dingTalkWebhookUrl}"
}


// 函数：发送邮件通知
def notifyBuild(String status, logSizeLimit) {
    emailext(
        subject: "Jenkins Job ${status}: Log Size Limit Exceeded",
        body: "The log size of the Jenkins job '${env.JOB_NAME}' has exceeded the limit of ${logSizeLimit} bytes.",
        recipientProviders: [[$class: 'DevelopersRecipientProvider']]
    )
}
```
