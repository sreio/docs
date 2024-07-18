## 资源查看命令
这些命令用于查看 Kubernetes 集群中的资源状态和信息：

- `kubectl get nodes`：查看所有节点的状态和信息。例如节点的名称、IP 地址、状态、版本等。
- `kubectl get pods`：查看所有 pod 的状态和信息。例如 pod 的名称、所在节点、状态、IP 地址、容器状态等。
- `kubectl get services`：查看所有服务的状态和信息。例如服务的名称、类型、IP 地址、端口等。
- `kubectl get deployments`：查看所有部署的状态和信息。例如部署的名称、所在命名空间、副本数、可用副本数等。
- `kubectl get replicasets`：查看所有副本集的状态和信息。例如副本集的名称、所在命名空间、副本数、可用副本数等。
- `kubectl get persistentvolumes`：查看所有持久卷的状态和信息。例如持久卷的名称、类型、容量、状态等。
- `kubectl get persistentvolumeclaims`：查看所有持久卷声明的状态和信息。例如持久卷声明的名称、所在命名空间、状态、绑定的持久卷名称等。
- `kubectl get namespaces`：查看所有命名空间的状态和信息。例如命名空间的名称、状态、创建时间等。
- `kubectl get configmaps`：查看所有配置地图的状态和信息。例如配置地图的名称、所在命名空间、数据等。
- `kubectl get secrets`：查看所有密钥的状态和信息。例如密钥的名称、所在命名空间、类型、数据等。
- `kubectl get events`：查看所有事件的状态和信息。例如事件的类型、对象、原因、消息等。
- `kubectl top nodes`：查看节点的资源使用情况。例如 CPU 和内存的使用率、使用量等。
- `kubectl top pods`：查看 pod 的资源使用情况。例如 CPU 和内存的使用率、使用量等。

## 资源详细信息命令

这些命令用于查看 Kubernetes 集群中特定资源的详细信息：

- `kubectl describe pod <pod-name>`：查看特定 pod 的详细信息。例如 pod 的状态、容器状态、事件等。
- `kubectl describe service <service-name>`：查看特定服务的详细信息。例如服务的类型、IP 地址、端口、关联的 pod 等。
- `kubectl describe node <node-name>`：查看特定节点的详细信息。例如节点的状态、标签、容量、使用情况等。
- `kubectl describe deployment <deployment-name>`：查看特定部署的详细信息。例如部署的状态、副本数、可用副本数、关联的 pod 等。
- `kubectl describe replicasets <replicaset-name>`：查看特定副本集的详细信息。例如副本集的状态、副本数、可用副本数、关联的 pod 等。
- `kubectl describe persistentvolume <persistentvolume-name>`：查看特定持久卷的详细信息。例如持久卷的状态、类型、容量、访问模式等。
- `kubectl describe persistentvolumeclaims <persistentvolumeclaim-name>`：查看特定持久卷声明的详细信息。例如持久卷声明的状态、绑定的持久卷名称、访问模式等。
- `kubectl describe namespace <namespace-name>`：查看特定命名空间的详细信息。例如命名空间的状态、标签、创建时间等。
- `kubectl describe configmap <configmap-name>`：查看特定配置地图的详细信息。例如配置地图的数据、创建时间等。
- `kubectl describe secret <secret-name>`：查看特定密钥的详细信息。例如密钥的类型、数据、创建时间等。
- `kubectl describe event <event-name>`：查看特定事件的详细信息。例如事件的类型、对象、原因、消息等。

## 日志相关命令

这些命令用于查看 Kubernetes 集群中特定 pod 的日志：

- `kubectl logs <pod-name>`：查看特定 pod 的日志。使用该命令可以快速查看 pod 的日志信息，以便定位问题。例如，要查看名为 my-pod 的 pod 的日志，可以使用以下命令：
```
kubectl logs my-pod
```
- `kubectl logs -f <pod-name>`：实时查看特定 pod 的日志。使用该命令可以实时查看 pod 的日志信息，以便快速发现问题。例如，要实时查看名为 my-pod 的 pod 的日志，可以使用以下命令：
```
kubectl logs -f my-pod
```
- `kubectl logs --tail=<n> <pod-name>`：查看特定 pod 的最后 n 行日志。使用该命令可以快速查看 pod 的最后 n 行日志信息，以便定位问题。例如，要查看名为 my-pod 的 pod 的最后 100 行日志，可以使用以下命令：
```
kubectl logs --tail=100 my-pod
```
- `kubectl logs --since=<time> <pod-name>`：查看特定 pod 自指定时间以来的日志。使用该命令可以查看 pod 在指定时间之后的日志信息，以便定位问题。例如，要查看名为 my-pod 的 pod 在 2023 年 5 月 20 日 10 点之后的日志，可以使用以下命令：
```
kubectl logs --since=2023-05-20T10:00:00 my-pod
```

## 资源创建和删除命令

这些命令用于创建和删除 Kubernetes 集群中的资源：

- `kubectl create -f <filename>`：从 YAML 或 JSON 文件中创建资源。使用场景包括：在 Kubernetes 集群中创建新的 pod、服务、部署等资源。示例：
```
kubectl create -f nginx.yaml
```
- `kubectl apply -f <filename>`：从 YAML 或 JSON 文件中创建或更新资源。使用场景包括：在 Kubernetes 集群中创建新的资源或更新已有的资源。示例：
```
kubectl apply -f nginx.yaml
```
- `kubectl delete <resource-type> <resource-name>`：删除指定类型和名称的资源。使用场景包括：在 Kubernetes 集群中删除不再需要的 pod、服务、部署等资源。示例：
```
kubectl delete pod nginx
```
- `kubectl delete -f <filename>`：从 YAML 或 JSON 文件中删除资源。使用场景包括：在 Kubernetes 集群中删除不再需要的资源。示例：
```
kubectl delete -f nginx.yaml
```

## 资源更新和扩缩容命令

这些命令用于更新和扩缩容 Kubernetes 集群中的资源：

- `kubectl edit <resource-type> <resource-name>`：编辑指定类型和名称的资源。使用场景包括：在 Kubernetes 集群中修改已有的 pod、服务、部署等资源的配置。示例：
```
kubectl edit deployment nginx
```
- `kubectl scale <resource-type> <resource-name> --replicas=<n>`：扩缩容指定类型和名称的资源。使用场景包括：在 Kubernetes 集群中增加或减少 pod、服务、部署等资源的副本数。示例：
```
kubectl scale deployment nginx --replicas=3
```
- `kubectl rollout status <resource-type> <resource-name>`：查看指定类型和名称的资源的滚动更新状态。使用场景包括：在 Kubernetes 集群中查看部署的滚动更新状态。示例：
```
kubectl rollout status deployment nginx
```
- `kubectl rollout history <resource-type> <resource-name>`：查看指定类型和名称的资源的滚动更新历史。使用场景包括：在 Kubernetes 集群中查看部署的滚动更新历史。示例：
```
kubectl rollout history deployment nginx
```
- `kubectl rollout undo <resource-type> <resource-name>`：回滚指定类型和名称的资源的滚动更新。使用场景包括：在 Kubernetes 集群中回滚部署的滚动更新。示例：
```
kubectl rollout undo deployment nginx
```

## 其他命令

这些命令用于其他操作：

`kubectl version`：查看 Kubernetes 集群和客户端的版本信息。使用场景包括：在 Kubernetes 集群中查看版本信息。
`kubectl config use-context <context-name>`：切换当前使用的上下文。使用场景包括：在 Kubernetes 集群中切换上下文。示例：
```
kubectl config use-context my-context
```
`kubectl exec -it <pod-name> -- <command>`：在指定 pod 中执行命令。使用场景包括：在 Kubernetes 集群中在指定 pod 中执行命令。示例：
```
kubectl exec -it nginx -- /bin/bash
```
`kubectl port-forward <pod-name> <local-port>:<pod-port>`：将指定 pod 的端口转发到本地端口。使用场景包括：在 Kubernetes 集群中将 pod 的端口转发到本地进行调试。示例：
```
kubectl port-forward nginx 8080:80
```
`kubectl proxy`：启动 Kubernetes API 代理服务器。使用场景包括：在 Kubernetes 集群中访问 API 服务器。