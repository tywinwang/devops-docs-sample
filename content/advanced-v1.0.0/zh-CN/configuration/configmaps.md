---
title: '配置'
---

配置项（ConfigMap）常用于存储工作负载所需的配置信息，许多应用程序会从配置文件、命令行参数或环境变量中读取配置信息。这些配置信息需要与 docker 镜像解耦，避免每修改一个配置需要重做一个镜像。ConfigMap 给我们提供了向容器中注入配置信息的机制，可以被用来保存单个属性，也可以用来保存整个配置文件或者 JSON 二进制对象，在工作负载中作为文件或者环境变量使用。

登录 KubeSphere 控制台，在所属的企业空间中选择已有 **项目** 或新建项目，访问左侧菜单栏，点击 **配置中心 ➡ ConfigMaps**，进入 ConfigMap 列表页。

![configmap列表](/configmaps-list.png)

## 创建 ConfigMap

创建 ConfigMap 支持两种方式，**页面创建** 和 **编辑模式** 创建，以下主要介绍页面创建的方式。若选择以编辑模式，可点击右上角编辑模式进入代码界面，支持 yaml 和 json 格式，可以方便习惯命令行操作的用户直接在页面上编辑 yaml 文件创建 ConfigMap。

![命令行模式](/configmap-cmd.png)

### 第一步：填写基本信息

在服务列表页，点击 **创建** 按钮，填写基本信息:

- 名称：为 ConfigMap 起一个简洁明了的名称，便于用户浏览和搜索。
- 描述信息：详细介绍 ConfigMap 的特性，当用户想进一步了解该服务时，描述内容将变得尤为重要。

![基本信息](/configmap-basic.png)

### 第二步：ConfigMap 设置

ConfigMap 资源用来保存键值对配置数据，通常用来在工作负载（Pod）中设置环境变量的值（高级选项中）、在容器数据卷里创建 config 文件，这些数据可以在工作负载里使用。

例如：

```
data:
  game.properties: 158 bytes
  ui.properties: 86 bytes
```

![](/configmap-setting.png)

创建好 ConfigMap 之后，创建工作负载时可以通过两种方式使用：

- 以 Volume 方式，在添加存储卷时点击 **引入配置中心** 选择创建的 ConfigMap。
- 以环境变量方式，在添加容器镜像的高级设置中，点击 **引入配置中心** 选择创建的 ConfigMap。