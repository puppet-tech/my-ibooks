![the webpack how to work.png](https://oss.puppetdev.top/image/note/21c20a886cd579bb5965fea1e9843e49.png)

<!--more-->

## 1.1 环境说明

## 1.2 Error: EACCES: permission denied, mkdir '/home/jenkins/agent/workspace/compile-demo/node_modules/node-sass/vendor'

```txt
Unable to save binary /home/jenkins/agent/workspace/compile-demo/node_modules/node-sass/vendor/linux-x64-72 : Error: EACCES: permission denied, mkdir '/home/jenkins/agent/workspace/compile-demo/node_modules/node-sass/vendor'
    at Object.mkdirSync (fs.js:921:3)
    at sync (/home/jenkins/agent/workspace/compile-demo/node_modules/node-sass/node_modules/mkdirp/index.js:74:13)
    at Function.sync (/home/jenkins/agent/workspace/compile-demo/node_modules/node-sass/node_modules/mkdirp/index.js:80:24)
    at checkAndDownloadBinary (/home/jenkins/agent/workspace/compile-demo/node_modules/node-sass/scripts/install.js:114:11)
    at Object.<anonymous> (/home/jenkins/agent/workspace/compile-demo/node_modules/node-sass/scripts/install.js:157:1)
    at Module._compile (internal/modules/cjs/loader.js:1015:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1035:10)
    at Module.load (internal/modules/cjs/loader.js:879:32)
    at Function.Module._load (internal/modules/cjs/loader.js:724:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:60:12) {
  errno: -13,
  syscall: 'mkdir',
  code: 'EACCES',
  path: '/home/jenkins/agent/workspace/compile-demo/node_modules/node-sass/vendor'
}
```

### 1.2.1 解决方案

`node-sass` 有时会遇到特定于环境的安装问题。尝试以全局方式安装 `node-sass`，或者更改其安装路径。

```shell
npm install -g node-sass
```

或者考虑使用 `--unsafe-perm` 标志运行 npm 命令，这有时可以解决安装过程中的权限问题。

```shell
npm install --unsafe-perm
```
