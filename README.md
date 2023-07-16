# intro
在k8s或k3s上搭建一套基础服务，既可以作为home lab，也可以用于生产环境。

整体流程：
- 步骤零：openstack + terraform
- 步骤一：平台选择，在k3s和k8s中选择一个, 在安装时，选择离线安装。 使用ansible
- 步骤二：搭建私有镜像仓库
- 步骤三：搭建各种基础软件


# Step 0 
- openstack是有必要的，它可以帮助我们更好的管理硬件，完成IAAS的职能
- terraform也是有必要的，它抽象了公有云和私有云资源管理，使得可以用统一的声明式代码的方式管理资源，即IaC的职能
- 步骤零如果省略则需要手动准备好主机


# Step 1
- 选择k8s或k3s
- 推荐安装时使用离线方式安装，确保可复现，避免网络问题

## 1.1 k8s
选用kubespray(ansible) + k8s离线包的方式安装k8s 

## 1.2 k3s
使用ansible + k3s 离线包的方式安装k3s

下载K3s二进制文件和镜像文件到本地主机（ansible controller）指定文件夹，然后由此复制给各个k3s主机。执行以下命令：
```
cd paas 
curl -L https://github.com/k3s-io/k3s/releases/download/v1.26.4%2Bk3s1/k3s -o ./roles/base/k3s-worker/file/k3s-v1.26.4+k3s1 
curl -L https://github.com/k3s-io/k3s/releases/download/v1.26.4%2Bk3s1/k3s-airgap-images-amd64.tar.gz -o ./roles/base/k3s-worker/file/k3s-airgap-images-amd64.tar.gz
```


# Step 2
- 使用registry或者harbor搭建私有镜像库
- 最小可用版本推荐使用registry，配置镜像代理，当镜像不存在时拉取远端仓库。
- harbor需要的内存和磁盘资源比较多，需要注意


# Step 3
提供两种方式安装基础软件：
- Ansible
- GitOps的方式（利用 tekton 和 argocd）


安装顺序(从上到下)：
- k3s
- metallb
- traefik 
- registry
- dashboard
- argocd 
- tekton 
- 其他软件

提供了两个版本：
- 最小可运行版本
- 三高版本

## 3.1 最小可用版本（单体）
|软件|版本|职能|说明|是否完成|
|--|--|--|--|--|
|traefik||ingress controller/反向代理||是|
|registry||私有镜像库||是|
|ArgoCD||CD||是|
|Tekton||CI||是|
|MySQL||SQL数据库||是|
|MongoDB||NoSQL数据库||是|
|redis||缓存||
|ES + filebeat + kibana||搜索/日志处理|
|opentracing/jaeger||trace组件|
|grafana||metric组件|
|alibaba sentinel||限流|
|apisix||API网关|
|kafka||消息队列|
|dtm||分布式事务|

全部指定主机使用本地存储，部分使用statefulset


## 3.2 三高版本（高可用、高性能、高并发、高可扩展）
|软件|版本|职能|说明|
|--|--|--|--|
|MySQL MGR||SQL数据库| todo |
|redis Cluster||缓存| todo |
|harbor||私有镜像库| todo | 
|gitlab||私有代码库| todo | 


## 其他
- 搭建homelab时，需要手动修改hosts文件（可能需要配置浏览器）
- 搭建homelab时使用cert-manager自签发证书的方式
- playbooks使用方法参看目录下的README.md