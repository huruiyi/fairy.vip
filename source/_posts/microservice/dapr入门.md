---
title: Step.1 dapr入门
categories:
- [Microservice]
---



# dapr入门

1. [安装 Dapr CLI](https://docs.dapr.io/getting-started/install-dapr-cli/)。 它使你能够启动、运行和管理 Dapr 实例。 它还提供调试支持。

2. 安装 [Docker Desktop](https://docs.docker.com/get-docker/)。 如果正在运行 Windows，请确保将适用于 Windows 的 **Docker Desktop** 配置为使用 Linux 容器。

    备注

   默认情况下，Dapr 使用 Docker 容器提供最佳的开箱即用体验。 若要在 Docker 外部运行 Dapr，可以跳过此步骤[并执行精简初始化](https://docs.dapr.io/operations/hosting/self-hosted/self-hosted-no-docker/)。 本章中的示例要求使用 Docker 容器。

3. [初始化 Dapr](https://docs.dapr.io/getting-started/install-dapr/)。 此步骤通过安装最新的 Dapr 二进制文件和容器映像来设置开发环境。

4. 安装 [.NET 6 SDK](https://dotnet.microsoft.com/download/dotnet/6.0)。



## 1：安装dapr

```yaml
Linux下安装
管理员安装：
wget -q https://raw.githubusercontent.com/dapr/cli/master/install/install.sh -O - | /bin/bash

非管理员安装：
wget -q https://raw.githubusercontent.com/dapr/cli/master/install/install.sh -O - | DAPR_INSTALL_DIR="$HOME/dapr" /bin/bash


Windows下安装：
管理员安装：
powershell -Command "iwr -useb https://raw.githubusercontent.com/dapr/cli/master/install/install.ps1 | iex"

非管理员安装
$script=iwr -useb https://raw.githubusercontent.com/dapr/cli/master/install/install.ps1; $block=[ScriptBlock]::Create($script); invoke-command -ScriptBlock $block -ArgumentList "", "$HOME/dapr"

Mac下安装：
curl -fsSL https://raw.githubusercontent.com/dapr/cli/master/install/install.sh | /bin/bash
softwareupdate --install-rosetta
or用brew安装：
brew install dapr/tap/dapr-cli


验证安装:
dapr

         __
    ____/ /___ _____  _____
   / __  / __ '/ __ \/ ___/
  / /_/ / /_/ / /_/ / /
  \__,_/\__,_/ .___/_/
              /_/

===============================
Distributed Application Runtime

Usage:
  dapr [command]

Available Commands:
  completion     Generates shell completion scripts
  components     List all Dapr components. Supported platforms: Kubernetes
  configurations List all Dapr configurations. Supported platforms: Kubernetes
  dashboard      Start Dapr dashboard. Supported platforms: Kubernetes and self-hosted
  help           Help about any command
  init           Install Dapr on supported hosting platforms. Supported platforms: Kubernetes and self-hosted
  invoke         Invoke a method on a given Dapr application. Supported platforms: Self-hosted
  list           List all Dapr instances. Supported platforms: Kubernetes and self-hosted
  logs           Get Dapr sidecar logs for an application. Supported platforms: Kubernetes
  mtls           Check if mTLS is enabled. Supported platforms: Kubernetes
  publish        Publish a pub-sub event. Supported platforms: Self-hosted
  run            Run Dapr and (optionally) your application side by side. Supported platforms: Self-hosted
  status         Show the health status of Dapr services. Supported platforms: Kubernetes
  stop           Stop Dapr instances and their associated apps. . Supported platforms: Self-hosted
  uninstall      Uninstall Dapr runtime. Supported platforms: Kubernetes and self-hosted
  upgrade        Upgrades a Dapr control plane installation in a cluster. Supported platforms: Kubernetes

Flags:
  -h, --help      help for dapr
  -v, --version   version for dapr

Use "dapr [command] --help" for more information about a command.
```

## 2：初始化Dapr

```yaml
dapr init

dapr --version
	CLI version: 1.5.1
	Runtime version: 1.5.1
```

## 3：验证是否运行：

```yaml
docker ps
```



![image-20211219112113531](C:\Users\hurui\AppData\Roaming\Typora\typora-user-images\image-20211219112113531.png)

## 4：运行dapr

```yaml
dapr run --app-id myapp --dapr-http-port 3500
```

create

```yaml
curl -X POST -H "Content-Type: application/json" -d '[{ "key": "name", "value": "Bruce Wayne"}]' http://localhost:3500/v1.0/state/statestore

or

Invoke-RestMethod -Method Post -ContentType 'application/json' -Body '[{ "key": "name", "value": "Bruce Wayne"}]' -Uri 'http://localhost:3500/v1.0/state/statestore'
```

query

```
curl http://localhost:3500/v1.0/state/statestore/name

or

Invoke-RestMethod -Uri 'http://localhost:3500/v1.0/state/statestore/name'
```



redis中的存储状态

```yaml
docker exec -it dapr_redis redis-cli
keys *

View the state value by running:
	hgetall "myapp||name"
```

## 5：定义一个组件

```yaml
1：创建组件目录
mkdir my-components
2：创建localSecretStore.yaml，内容如下

apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: my-secret-store
  namespace: default
spec:
  type: secretstores.local.file
  version: v1
  metadata:
  - name: secretsFile
    value: <PATH TO SECRETS FILE>/mysecrets.json
  - name: nestedSeparator
    value: ":"
3：运行：
dapr run --app-id myapp --dapr-http-port 3500 --components-path ./my-components

4：调用
curl http://localhost:3500/v1.0/secrets/my-secret-store/my-secret
or
Invoke-RestMethod -Uri 'http://localhost:3500/v1.0/secrets/my-secret-store/my-secret'
```

