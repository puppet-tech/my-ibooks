---
tags:
  - Maven
  - 工具
title: Maven项目编译工具优化升级
---

## 1.1 可选方案

- 对 Maven 自身进行优化提升
- 改用 mvnd
- 改用 Gradle

## 1.2 对 Maven 自身进行优化提升

使用正确的 maven phase（阶段） 可以为我们节省大量时间。如果只需要编译代码，就没必要运行完整的 install，并且这会污染我们的本地仓库。

在多模块项目中，可以只重建已更改的模块和依赖于这些模块的模块。例如，如果我们只修改了 module1 和 module2，我们可以运行：

```shell
mvn clean install -pl module1,module2 -am
```

### 1.2.1 使用多线程

默认情况下，Maven 构建在单线程中顺序运行。不过，如今所有电脑都有多核。让我们利用这一点，使用 `-T` 选项并行构建我们的模块：

```shell
mvn clean install -T 1C
```

- `-T 1C` 表示 Maven 将在每个可用内核上使用一个线程。
- `-T 4` 会强制 Maven 使用四个线程。
- `-T auto` 会让 Maven 决定使用的线程数。

最后但并非最不重要的一点是，Maven Reactor 可确保所有相互依赖的模块按顺序运行。

### 1.2.2 优化测试

测试是软件开发的重要组成部分。然而，运行测试需要花费大量时间。

#### 1.2.2.1 并行运行测试

默认情况下，Surefire 插件会按顺序运行单元测试。不过，我们可以将其配置为并行运行。例如，要 [并行](https://springdoc.cn/maven-fast-build/%5D(https://maven.apache.org/surefire/maven-surefire-plugin/examples/fork-options-and-parallel-execution.html#parallel-test-execution)) 运行所有测试套件，并在每个可用内核上使用一个线程，我们可以运行：

```shell
mvn clean install -Dparallel=all -DperCoreThreadCount=true
```

但是，如果我们的项目中没有大量的单元测试，并行化的开销可能会导致更低的效率。

#### 1.2.2.2 跳过测试执行

有时，我们并不需要在本地环境中运行测试。Maven `-DskipTests` 选项会跳过测试的执行，但仍会编译 test 文件夹：

```shell
mvn clean install -DskipTests
```

在高度测试的项目中，当我们不需要测试时，跳过测试可以节省我们的时间！

#### 1.2.2.3 跳过测试编译

此外，我们还可以使用 `-Dmaven.test.skip=true` 选项跳过 test execution，甚至不编译它们：

```shell
mvn clean install -Dmaven.test.skip=true
```

这种方法可以进一步缩短总的构建时间。

### 1.2.3 优化 JVM 参数

**默认情况下，HotSpot JVM 使用分层编译**：它同时使用 client 和 server 编译器来优化 Java 字节码。这种功能可优化服务器上的长期进程。然而，编译是一个连续的短期进程。因此，让我们使用以下 JVM 参数：

- `-XX:-TieredCompilation`: JVM 不会使用分层编译。
- `-XX:TieredStopAtLevel=1`: JVM 只使用 client 编译器。

我们可以创建包含以下内容的 `.mvn/jvm.config` 文件，让 Maven 使用这些选项：

```plaintext
-XX:-TieredCompilation -XX:TieredStopAtLevel=1
```

实际上，Maven 使用缓存和增量构建技术来避免重新编译未更改的代码。因此，Maven 需要编译的新代码越多，这种技术的效率就越高。

### 1.2.4 离线运行

在构建过程中，Maven 会多次往服务器发起请求，例如进行依赖关系解析和插件下载。特别是，它每次都会检查新的 snapshot 更新。我们可以通过进入离线模式（offline mode）来避免这种情况：

```shell
mvn clean install -o
```

显然，当我们需要更新某些依赖项时，我们不能使用离线模式。

### 1.2.5 确定瓶颈

如果前面的技巧不能帮助我们将构建时间缩短到可以接受的程度，我们可以借助 profiler 来排除构建过程中的故障。

首先，让我们创建一个新的 Maven 项目。然后，按照 [Maven Profiler](https://github.com/jcgay/maven-profiler) 项目 GitHub 上的说明进行安装。最后，我们需要在 `pom.xml` 中添加依赖：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-profiler-plugin</artifactId>
            <version>1.7</version>
        </plugin>
    </plugins>
</build>
```

现在，我们可以使用该命令来更深入地了解我们的构建时间：

```shell
$ mvn clean install -Dprofile
```

构建完成后，我们可以在 `.profiler` 文件夹中找到 HTML 文本形式的报告。

![maven profiler](https://oss.puppetdev.top/image/note/08876f769f56df1a301ba0bbb314e8f5.webp)

可以看到，profiler 列出了所有插件的执行情况，并记录了它们所耗费的时间。

第二部分列出了下载的 artifact（工件）。这些信息可以帮助我们识别那些运行时间很长，但在目标环境中几乎没有任何价值的插件。

### 1.2.6 使用 Maven Profiles

一旦发现瓶颈，我们就可以使用 Maven profiles 按需跳过它们，从而节省时间。例如，执行集成测试通常需要很长时间。此外，每次都在本地环境中运行也没有用。

让我们在 `pom.xml` 中添加 failsafe 插件配置：

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-failsafe-plugin</artifactId>
    <version>3.1.2</version>
    <executions>
        <execution>
            <goals>
                <goal>integration-test</goal>
                <goal>verify</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

现在，我们可以添加一个跳过它们的 profile：

```xml
<profiles>
    <profile>
        <id>skipITs</id>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-failsafe-plugin</artifactId>
                    <configuration>
                        <skip>true</skip>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </profile>
</profiles>
```

由于重写了配置，激活了 `skipITs` profile，集成测试现在被跳过了：

```shell
$ mvn clean install -PskipITs
```

## 1.3 改用 mvnd

### 1.3.1 什么是 mvnd

mvnd 是 Maven Daemon 的缩写 ，翻译成中文就是 Maven 守护进程。

mvnd 是 apache/maven 的一个子项目，它并不是一个全新的构建工具，而是对 maven 的扩展。它内置了 maven，其实现原理是构建了一个或者多个 maven 守护进程来执行构建服务。

### 1.3.2 mvnd 优点

用于运行构建的 JVM 不需要每次构建都重新启动。

持有 Maven 插件类的类加载器在多个构建中会被缓存。因此，插件 jar 文件只被读取和解析一次。(快照版本的 Maven 插件不会被缓存)

JVM 中即时 (JIT) 编译器生成的本地代码也被保留，与常规 Maven 相比，JIT 编译所花费的时间更少。在重复构建期间，JIT 优化的代码立即可用，这不仅适用于来自 Maven 插件和 Maven Core 的代码，也适用于所有来自 JDK 本身的代码。

## 1.4 结合 mvnd

1. 使用 Maven daemon
2. 在不需要运行测试时跳过测试。
3. 对构建进行配置，使用 Maven profiles 排除耗时的低价值任务。

## 1.5 改用 Gradle

### 1.5.1 gradle 和 maven 的比较

虽然 gradle 和 maven 都可以作为 java 程序的构建工具。但是两者还是有很大的不同之处的。我们可以从下面几个方面来进行分析。

#### 1.5.1.1 可扩展性

Google 选择 gradle 作为 android 的构建工具不是没有理由的，其中一个非常重要的原因就是因为 gradle 够灵活。一方面是因为 gradle 使用的是 groovy 或者 kotlin 语言作为脚本的编写语言，这样极大的提高了脚本的灵活性，但是其本质上的原因是 gradle 的基础架构能够支持这种灵活性。

你可以使用 gradle 来构建 native 的 C/C++ 程序，甚至扩展到任何语言的构建。

相对而言，maven 的灵活性就差一些，并且自定义起来也比较麻烦，但是 maven 的项目比较容易看懂，并且上手简单。

所以如果你的项目没有太多自定义构建需求的话还是推荐使用 maven，但是如果有自定义的构建需求，那么还是投入 gradle 的怀抱吧。

#### 1.5.1.2 性能比较

虽然现在大家的机子性能都比较强劲，好像在做项目构建的时候性能的优势并不是那么的迫切，但是对于大型项目来说，一次构建可能会需要很长的时间，尤其对于自动化构建和 CI 的环境来说，当然希望这个构建是越快越好。

Gradle 和 Maven 都支持并行的项目构建和依赖解析。但是 gradle 的三个特点让 gradle 可以跑的比 maven 快上一点：

- 增量构建

    gradle 为了提升构建的效率，提出了增量构建的概念，为了实现增量构建，gradle 将每一个 task 都分成了三部分，分别是 input 输入，任务本身和 output 输出。下图是一个典型的 java 编译的 task。

![](https://oss.puppetdev.top/image/note/62fcda325821467863f530222781862e.png)

以上图为例，input 就是目标 jdk 的版本，源代码等，output 就是编译出来的 class 文件。

增量构建的原理就是监控 input 的变化，只有 input 发送变化了，才重新执行 task 任务，否则 gradle 认为可以重用之前的执行结果。

所以在编写 gradle 的 task 的时候，需要指定 task 的输入和输出。

并且要注意只有会对输出结果产生变化的才能被称为输入，如果你定义了对初始结果完全无关的变量作为输入，则这些变量的变化会导致 gradle 重新执行 task，导致了不必要的性能的损耗。

还要注意不确定执行结果的任务，比如说同样的输入可能会得到不同的输出结果，那么这样的任务将不能够被配置为增量构建任务。

- 构建缓存

    gradle 可以重用同样 input 的输出作为缓存，大家可能会有疑问了，这个缓存和增量编译不是一个意思吗？

    在同一个机子上是的，但是缓存可以跨机器共享.如果你是在一个 CI 服务的话，build cache 将会非常有用。因为 developer 的 build 可以直接从 [CI服务器](https://cloud.tencent.com/act/pro/promotion-cvm?from_column=20065&from=20065) 上面拉取构建结果，非常的方便。

- Gradle 守护进程

    gradle 会开启一个守护进程来和各个 build 任务进行交互，优点就是不需要每次构建都初始化需要的组件和服务。

    同时因为守护进程是一个一直运行的进程，除了可以避免每次 JVM 启动的开销之外，还可以缓存项目结构，文件，task 和其他的信息，从而提升运行速度。

    我们可以运行 gradle –status 来查看正在运行的 daemons 进程。

    从 Gradle 3.0 之后，daemons 是默认开启的，可以使用 org.gradle.daemon=false 来禁止 daemons。

我们可以通过下面的几个图来直观的感受一下 gradle 和 maven 的性能比较：

- 使用 gradle 和 maven 构建 Apache Commons Lang 3 的比较：

![](https://oss.puppetdev.top/image/note/0c6f37efd14ae36a690558da76c48905.png)

- 使用 gradle 和 maven 构建小项目（10 个模块，每个模块 50 个源文件和 50 个测试文件）的比较：

![](https://oss.puppetdev.top/image/note/db5bdbaa21aa0ff852868adb329e6c98.png)

- 使用 gradle 和 maven 构建大项目（500 个模块，每个模块 100 个源文件和 100 个测试文件）的比较：

![](https://oss.puppetdev.top/image/note/4bdcbf87392b2dc94ca0ef2bc73d45f3.png)

可以看到 gradle 性能的提升是非常明显的。

#### 1.5.1.3 依赖的区别

gralde 和 maven 都可以本地缓存依赖文件，并且都支持依赖文件的并行下载。

在 maven 中只可以通过版本号来覆盖一个依赖项。而 gradle 更加灵活，你可以自定义依赖关系和替换规则，通过这些替换规则，gradle 可以构建非常复杂的项目。

### 1.5.2 从 maven 迁移到 gradle

因为 maven 出现的时间比较早，所以基本上所有的 java 项目都支持 maven，但是并不是所有的项目都支持 gradle。如果你有需要把 maven 项目迁移到 gradle 的想法，那么就一起来看看吧。

根据我们之前的介绍，大家可以发现 gradle 和 maven 从本质上来说就是不同的，gradle 通过 task 的 DAG 图来组织任务，而 maven 则是通过 attach 到 phases 的 goals 来执行任务。

虽然两者的构建有很大的不同，但是得益于 gradle 和 maven 相识的各种约定规则，从 maven 移植到 gradle 并不是那么难。

要想从 maven 移植到 gradle，首先要了解下 maven 的 build 生命周期，maven 的生命周期包含了 clean，compile，test，package，verify，install 和 deploy 这几个 phase。

我们需要将 maven 的生命周期 phase 转换为 gradle 的生命周期 task。这里需要使用到 gradle 的 Base Plugin，Java Plugin 和 Maven Publish Plugin。

先看下怎么引入这三个 plugin：

```javascript
plugins {
    id 'base'
    id 'java'
    id 'maven-publish'
}
```

clean 会被转换成为 clean task，compile 会被转换成为 classes task，test 会被转换成为 test task，package 会被转换成为 assemble task，verify 会被转换成为 check task，install 会被转换成为 Maven Publish Plugin 中的 publishToMavenLocal task，deploy 会被转换成为 Maven Publish Plugin 中的 publish task。

有了这些 task 之间的对应关系，我们就可以尝试进行 maven 到 gradle 的转换了。

#### 1.5.2.1 自动转换

我们除了可以使用 gradle init 命令来创建一个 gradle 的架子之外，还可以使用这个命令来将 maven 项目转换成为 gradle 项目，gradle init 命令会去读取 pom 文件，并将其转换成为 gradle 项目。

#### 1.5.2.2 转换依赖

gradle 和 maven 的依赖都包含了 group ID, artifact ID 和版本号。两者本质上是一样的，只是形式不同，我们看一个转换的例子：

```javascript
<dependencies>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.12</version>
    </dependency>
</dependencies>
```

上是一个 maven 的例子，我们看下 gradle 的例子怎写：

```javascript
dependencies {
    implementation 'log4j:log4j:1.2.12'  
}
```

可以看到 gradle 比 maven 写起来要简单很多。

注意这里的 implementation 实际上是由 Java Plugin 来实现的。

我们在 maven 的依赖中有时候还会用到 scope 选项，用来表示依赖的范围，我们看下这些范围该如何进行转换：

- compile：

    在 gradle 可以有两种配置来替换 compile，我们可以使用 implementation 或者 api。

    前者在任何使用 Java Plugin 的 gradle 中都可以使用，而 api 只能在使用 Java Library Plugin 的项目中使用。

    当然两者是有区别的，如果你是构建应用程序或者 webapp，那么推荐使用 implementation，如果你是在构建 Java libraries，那么推荐使用 api。

- runtime：可以替换成 runtimeOnly 。
- test：

    gradle 中的 test 分为两种，一种是编译 test 项目的时候需要，那么可以使用 testImplementation，一种是运行 test 项目的时候需要，那么可以使用 testRuntimeOnly。

- provided：可以替换成为 compileOnly。
- import：

    在 maven 中，import 经常用在 dependencyManagement 中，通常用来从一个 pom 文件中导入依赖项，从而保证项目中依赖项目版本的一致性。

在 gradle 中，可以使用 `platform()` 或者 `enforcedPlatform()` 来导入 pom 文件：

```javascript
dependencies {
    implementation platform('org.springframework.boot:spring-boot-dependencies:1.5.8.RELEASE') 

    implementation 'com.google.code.gson:gson' 
    implementation 'dom4j:dom4j'
}
```

比如上面的例子中，我们导入了 spring-boot-dependencies。因为这个 pom 中已经定义了依赖项的版本号，所以我们在后面引入 gson 的时候就不需要指定版本号了。

platform 和 enforcedPlatform 的区别在于，enforcedPlatform 会将导入的 pom 版本号覆盖其他导入的版本号：

```javascript
dependencies {
    // import a BOM. The versions used in this file will override any other version found in the graph
    implementation enforcedPlatform('org.springframework.boot:spring-boot-dependencies:1.5.8.RELEASE')

    // define dependencies without versions
    implementation 'com.google.code.gson:gson'
    implementation 'dom4j:dom4j'

    // this version will be overridden by the one found in the BOM
    implementation 'org.codehaus.groovy:groovy:1.8.6'
}
```

#### 1.5.2.3 转换 repositories 仓库

gradle 可以兼容使用 maven 或者 lvy 的 repository。gradle 没有默认的仓库地址，所以你必须手动指定一个。

你可以在 gradle 使用 maven 的仓库：

```javascript
repositories {
    mavenCentral()
}
```

我们还可以直接指定 maven 仓库的地址：

```javascript
repositories {
    maven {
        url "http://repo.mycompany.com/maven2"
    }
}
```

如果你想使用 maven 本地的仓库，则可以这样使用：

```javascript
repositories {
    mavenLocal()
}
```

但是 mavenLocal 是不推荐使用的，为什么呢？

mavenLocal 只是 maven 在本地的一个 cache，它包含的内容并不完整。比如说一个本地的 maven repository module 可能只包含了 jar 包文件，并没有包含 source 或者 javadoc 文件。那么我们将不能够在 gradle 中查看这个 module 的源代码，因为 gradle 会首先在 maven 本地的路径中查找这个 module。

并且本地的 repository 是不可信任的，因为里面的内容可以轻易被修改，并没有任何的验证机制。

#### 1.5.2.4 控制依赖的版本

如果同一个项目中对同一个模块有不同版本的两个依赖的话，默认情况下 Gradle 会在解析完 DAG 之后，选择版本最高的那个依赖包。

但是这样做并不一定就是正确的， 所以我们需要自定义依赖版本的功能。

首先就是上面我们提到的使用 `platform()` 和 `enforcedPlatform()` 来导入 BOM（packaging 类型是 POM 的）文件。

如果我们项目中依赖了某个 module，而这个 module 又依赖了另外的 module，我们叫做传递依赖。在这种情况下，如果我们希望控制传递依赖的版本，比如说将传递依赖的版本升级为一个新的版本，那么可以使用 dependency constraints：

```javascript
dependencies {
    implementation 'org.apache.httpcomponents:httpclient'
    constraints {
        implementation('org.apache.httpcomponents:httpclient:4.5.3') {
            because 'previous versions have a bug impacting this application'
        }
        implementation('commons-codec:commons-codec:1.11') {
            because 'version 1.9 pulled from httpclient has bugs affecting this application'
        }
    }
}
```

> _注意，dependency constraints 只对传递依赖有效，如果上面的例子中 commons-codec 并不是传递依赖，那么将不会有任何影响。_ _同时 Dependency constraints 需要 Gradle Module Metadata 的支持，也就是说只有你的 module 是发布在 gradle 中才支持这个特性，如果是发布在 maven 或者 ivy 中是不支持的。_

上面讲的是传递依赖的版本升级。同样是传递依赖，如果本项目也需要使用到这个传递依赖的 module，但是需要使用到更低的版本（因为默认 gradle 会使用最新的版本），就需要用到版本降级了。

```javascript
dependencies {
    implementation 'org.apache.httpcomponents:httpclient:4.5.4'
    implementation('commons-codec:commons-codec') {
        version {
            strictly '1.9'
        }
    }
}
```

我们可以在 implementation 中指定特定的 version 即可。

strictly 表示的是强制匹配特定的版本号，除了 strictly 之外，还有 require，表示需要的版本号大于等于给定的版本号。prefer，如果没有指定其他的版本号，那么就使用 prefer 这个。reject，拒绝使用这个版本。

除此之外，你还可以使用 Java Platform Plugin 来指定特定的 platform，从而限制版本号。

最后看一下如何 exclude 一个依赖：

```javascript
dependencies {
    implementation('commons-beanutils:commons-beanutils:1.9.4') {
        exclude group: 'commons-collections', module: 'commons-collections'
    }
}
```

#### 1.5.2.5 多模块项目

maven 中可以创建多模块项目：

```javascript
<modules>
    <module>simple-weather</module>
    <module>simple-webapp</module>
</modules>
```

我们可以在 gradle 中做同样的事情 settings.gradle：

```javascript
rootProject.name = 'simple-multi-module'  

include 'simple-weather', 'simple-webapp'  
```

#### 1.5.2.6 profile 和属性

maven 中可以使用 profile 来区别不同的环境，在 gradle 中，我们可以定义好不同的 profile 文件，然后通过脚本来加载他们：

build.gradle：

```javascript
if (!hasProperty('buildProfile')) ext.buildProfile = 'default'  

apply from: "profile-${buildProfile}.gradle"  

task greeting {
    doLast {
        println message  
    }
}
```

profile-default.gradle：

```javascript
ext.message = 'foobar'  
```

profile-test.gradle：

```javascript
ext.message = 'testing 1 2 3'
```

我们可以这样来运行：

```javascript
> gradle greeting
foobar

> gradle -PbuildProfile=test greeting
testing 1 2 3
```

#### 1.5.2.7 资源处理

在 maven 中有一个 process-resources 阶段，可以执行 resources:resources 用来进行 resource 文件的拷贝操作。

在 Gradle 中的 Java plugin 的 processResources task 也可以做相同的事情。

比如我可以执行 copy 任务：

```javascript
task copyReport(type: Copy) {
    from file("buildDir/reports/my-report.pdf")
    into file("buildDir/toArchive")
}
```

更加复杂的拷贝：

```javascript
task copyPdfReportsForArchiving(type: Copy) {
    from "buildDir/reports"
    include "*.pdf"
    into "buildDir/toArchive"
}
```

当然拷贝还有更加复杂的应用。这里就不详细讲解了。
