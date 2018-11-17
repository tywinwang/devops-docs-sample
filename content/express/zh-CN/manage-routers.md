---
title: "应用路由管理"
---

应用路由是来聚合集群内服务的方式，对应的是 Kubernetes 的 Ingress 资源，后端使用了 Nginx Controller 来处理具体规则。Ingress 可以给 service 提供集群外部访问的 URL、负载均衡、SSL termination、HTTP 路由等。

本节通过以下几个方面介绍如何管理应用路由：

- 启用外网访问入口
- 创建应用路由
- 访问应用路由


## 启用外网访问入口

首先登录 KubeSphere 控制台，在左侧导航栏选择 **服务与网络** 菜单的 **应用路由** 进入到应用路由列表页面。

![应用路由列表](/router-gateway1.png)

在创建应用路由之前，需要先启用外网访问入口，即网关。这一步是创建对应的应用路由控制器，来负责将请求转发到对应的后端服务。如不预先启用外网访问入口，创建的应用路由规则将无法访问。

1. 点击应用路由规则列表上方的 **外网访问入口** 按钮。

2. 在弹出的窗口左侧选择需要启用的项目，在右侧选择网关的类型。每个项目都有一个独立的网关。

> - NodePort: 此方式网关可以通过工作节点对应的端口来访问
> - LoadBalancer: 此方式网关可以通过统一的一个外网 IP 来访问 (需要对应的负载均衡器插件)

3. 选择对应的网关启用方式后，点击 **应用** 来创建网关，如下图选择的是 NodePort 的方式，左边节点端口生成的两个端口，分别是 HTTP 协议的 80 端口和 HTTPS 协议的 443 端口。完成后选择关闭。

![网关设置-NodePort](/router-gateway.png)

4. 若选择的是 LoadBalancer，则需要将公网 IP 的 ID 填入 Annotation，如下图所示，点击 **应用** 来创建网关。

![网关设置-LoadBalancer](/router-gateway-conf.png)

## 创建应用路由

> 应用路由需要先启用外网访问入口，请按照上面步骤启用外网访问入口

1. 点击应用路由规则右上角的 **创建应用路由** 按钮，填写应用路由的名称。

2. 填写应用路由的规则，如下图所示。

> - Hostname: 应用规则的访问域名，最终使用此域名来访问对应的服务。如果访问入口是以 NodePort 的方式启用，需要保证 Host 能够在客户端正确解析到集群工作节点上；如果是以 LoadBalancer 方式启用，需要保证 Host 正确解析到负载均衡器的 IP 上。
> - Path: 应用规则的路径和对应的后端服务，端口需要填写成服务的端口。

![创建应用路由](/router-create.png)

3. 点击下一步，为应用规则添加注解。如无特殊设置，这一步可以跳过。

4. 添加标签后，创建完成。


## 访问应用路由

应用路由创建完成后，确保设置的域名可以解析到外网访问入口的 IP 地址，即可使用域名访问。如在私有环境中，可以使用修改本地 hosts 文件的方式来使用应用路由。例如，

|设置的域名|路径|外网访问入口方式|端口/IP|集群工作节点IP|
----|---|---|---|---
|demo.kubesphere.io|/|NodePort|32586,31920|192.168.0.4,192.168.0.3,192.168.0.2|
|demo2.kubesphere.io|/|LoadBalancer|139.198.1.1|192.168.0.4,192.168.0.3,192.168.0.2

如上表格，创建了两条应用路由规则，分别使用 NodePort 和 LoadBalancer 方式的访问入口。

**NodePort**

对于外网访问方式设置的 NodePort，如果是在私有环境下，我们可以直接在主机的 hosts 文件中添加记录来使域名解析到对应的 IP。例如，对于 `demo.kubesphere.io`，我们添加如下记录：

```
192.168.0.4 demo.kubesphere.io
```

需要保证客户端与集群工作节点 192.168.0.4 网络可通，可以使用其它工作节点的 IP，只要客户端和工作节点网络是通的，设置完之后，在浏览器中使用域名和网关的端口号 `http://demo.kubesphere.io:32586` 即可访问。(此示例中用的是第一个端口 32586，它对应的 HTTP 协议的 80 端口，目前仅支持 HTTP 协议，将在下个版本中支持 HTTPS 协议。) 

**LoadBalancer**

如果外网访问方式设置的是 LoadBalancer，对于 `demo2.kubesphere.io`，除了参考以上方式在 hosts 文件中添加记录之外，还可以使用 [nip.io](http://nip.io/)  作为应用路由的域名解析。nip.io 是一个免费的域名解析服务，可以将符合下列格式的域名解析对应的ip，可用来作为应用路由的解析服务，省去配置本地 hosts 文件的步骤。

**格式**

```
10.0.0.1.nip.io maps to 10.0.0.1  
app.10.0.0.1.nip.io maps to 10.0.0.1
customer1.app.10.0.0.1.nip.io maps to 10.0.0.1
customer2.app.10.0.0.1.nip.io maps to 10.0.0.1
otherapp.10.0.0.1.nip.io maps to 10.0.0.1
```

例如，应用路由的网关公网IP地址为 139.198.121.154 , 在创建应用路由时，Hostname 一栏填写为 `demo2.kubesphere.139.198.121.154.nip.io`，其它保持原来的设置。
![路由规则](/router-rules-conf.png)

创建完成后，直接使用 [http://demo2.kubesphere.139.198.121.154.nip.io](http://demo2.kubesphere.139.198.121.154.nip.io)，即可访问对应的服务。
![访问域名登录页面](/router-login.png)

