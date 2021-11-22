部署：
```
kubectl apply -f etcd.yaml
```
进入pod测试访问：
```
kubectl exec -it infra-etcd-cluster-0 -- sh

ETCDCTL_API=3 etcdctl --endpoints=http://infra-etcd-cluster-0.infra-etcd-cluster.default:2379,http://infra-etcd-cluster-1.infra-etcd-cluster.default:2379,http://infra-etcd-cluster-2.infra-et
cd-cluster.default:2379 endpoint health --write-out=table
```

也可以通过创建一个service对外暴露：
``` yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    k8s-app: infra-etcd-cluster-client
    app: infra-etcd
  name: infra-etcd-cluster-client
  namespace: default
spec:
  ports:
  - name: infra-etcd-cluster-2379
    port: 2379
    protocol: TCP
    targetPort: 2379
  selector:
    k8s-app: infra-etcd-cluster
    app: infra-etcd
  sessionAffinity: None
  type: NodePort

```
扩容：
``` sh
kubectl scale --replicas=5 statefulset infra-etcd-cluster
```
缩容：
``` sh
kubectl scale --replicas=3 statefulset infra-etcd-cluster
```
