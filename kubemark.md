### kubemark

https://github.com/kubernetes/kubernetes.git

#### 1、下载代码：

```shell
[root@perf-test-cce-centos fxq]# git clone -b release-1.19 https://github.com/kubernetes/kubernetes.git 
Cloning into 'kubernetes'...
remote: Enumerating objects: 1258218, done.
remote: Counting objects: 100% (188/188), done.
remote: Compressing objects: 100% (126/126), done.
remote: Total 1258218 (delta 82), reused 64 (delta 62), pack-reused 1258030
Receiving objects: 100% (1258218/1258218), 773.25 MiB | 5.70 MiB/s, done.
Resolving deltas: 100% (906071/906071), done.
You have new mail in /var/spool/mail/root

安装go
[root@perf-test-cce-centos fxq]# yum install golang

```

#### 2、修改hollow-node.go

````shell
1.	进入kubernetes/cmd/kubemark目录，打开hollow-node.go文件；
2.	在kubelet模块加入如下代码：
hollowKubelet.KubeletFlags.NodeLabels = make(map[string]string)
m2 := make(map[string]string)
m2["node-role.kubernetes.io/edge"] = "edge"
hollowKubelet.KubeletFlags.NodeLabels = m2
hollowKubelet.Run()

````



![image-20210812115629813](C:\Users\f00475147\AppData\Roaming\Typora\typora-user-images\image-20210812115629813.png)

#### 3、安装go

```shell
# yum install golang
# 安装后目录如下：
[root@perf-test-cce-centos golang]# ls
api  bin  doc  favicon.ico  lib  pkg  robots.txt  src  VERSION
[root@perf-test-cce-centos golang]# pwd
/usr/lib/golang
[root@perf-test-cce-centos golang]# 
# 修改配置 vim /etc/profile
export GOROOT=/usr/lib/golang
export GOPATH=/root/go
export PATH=${PATH}:${GOROOT}/bin:$GOPATH/bin
# 执行立即生效
source /etc/profile
```

查看go环境变量

```shell
[root@perf-test-cce-centos bin]# go env
GO111MODULE="on"
GOARCH="amd64"
GOBIN=""
GOCACHE="/root/.cache/go-build"
GOENV="/root/.config/go/env"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="linux"
GOINSECURE=""
GOMODCACHE="/root/go/pkg/mod"
GONOPROXY=""
GONOSUMDB=""
GOOS="linux"
GOPATH="/root/go"
GOPRIVATE=""
GOPROXY="https://goproxy.cn,direct"
GOROOT="/usr/lib/golang"
GOSUMDB="off"
GOTMPDIR=""
GOTOOLDIR="/usr/lib/golang/pkg/tool/linux_amd64"
GCCGO="gccgo"
AR="ar"
CC="gcc"
CXX="g++"
CGO_ENABLED="1"
GOMOD="/dev/null"
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -m64 -pthread -fmessage-length=0 -fdebug-prefix-map=/tmp/go-build854785575=/tmp/go-build -gno-record-gcc-switches"
```

#### 4、源码编译过程

将改造后kubernetes源码拷贝至/root/go/目录下（GOPATH目录）

进入kubernetes/cmd/kubemark/目录，执行：

```shell
# 启用 Go Modules 功能
go env -w GO111MODULE=on

# 配置 GOPROXY 环境变量，以下三选一

# 1. 七牛 CDN
go env -w GOPROXY=https://goproxy.cn,direct
# 编译
go build .
# 编译完成
[root@perf-test-cce-centos kubemark]# ls -al
total 137948
drwxr-xr-x  2 root root      4096 Aug 12 12:15 .
drwxr-xr-x 24 root root      4096 Aug 12 11:51 ..
-rw-r--r--  1 root root      2288 Aug 12 11:51 BUILD
-rw-r--r--  1 root root      9127 Aug 12 11:56 hollow-node.go
-rwxr-xr-x  1 root root 141229984 Aug 12 12:15 kubemark
-rw-r--r--  1 root root       207 Aug 12 11:51 OWNERS
[root@perf-test-cce-centos kubemark]# pwd
/root/go/kubernetes/cmd/kubemark
```

拷贝编译获得的kubemark二进制源码至/kubernetes/cluster/images/kubemark下

```shell
[root@perf-test-cce-centos kubemark]# cp kubemark /root/go/kubernetes/cluster/images/kubemark/
[root@perf-test-cce-centos kubemark]# cd 
BUILD           hollow-node.go  kubemark        OWNERS          
[root@perf-test-cce-centos kubemark]# cd /root/go/kubernetes/cluster/images/kubemark/
[root@perf-test-cce-centos kubemark]# ls
BUILD  Dockerfile  kubemark  Makefile  OWNERS
```

创建sed-server.sh

```shell
[root@perf-test-cce-centos kubemark]# cat sed-server.sh 
#!/usr/bin/env bash

function prepare_kubeconfig() {
    server=`cat /kubeconfig/kubelet.kubeconfig | grep -o "server:[a-z0-9/.:-\' \']*" |tr -d "\""`
    cp /kubeconfig/kubeproxy.kubeconfig /kubeconfig/kubeproxy-kubeconfig
    sed -i "/name: cluster/i\    ${server}" /kubeconfig/kubeproxy-kubeconfig
}
prepare_kubeconfig
```

修改Dockerfile

```shell
FROM gcr.io/distroless/base@sha256:7fa7445dfbebae4f4b7ab0e6ef99276e96075ae42584af6286ba080750d6dfe5

COPY kubemark /kubemark
COPY sed-server.sh /sed-server.sh
```

执行镜像构建

```shell
docker build -t kubemark-x86 .
```

#### 5、部署hollow-node

```shell
[root@perf-test-cce-centos ~]# kubectl create configmap kubemark-configmap --from-file=/root/.kube/config -nkubemark
configmap/kubemark-configmap created
# 注意configmap的data key值需要命名为kubeconfig
[root@perf-test-cce-centos ~]# kubectl create configmap node-configmap -n kubemark --from-literal=content.type="test-cluster"
configmap/node-configmap created
# 部署
[root@perf-test-cce-centos ~]# kubectl apply -f hollow-node.yaml 
[root@perf-test-cce-centos ~]# kubectl get node
NAME                           STATUS   ROLES    AGE    VERSION
172.17.190.224                 Ready    <none>   27h    v1.19.10-r1.0.0-source-28-g913cae0efd63da
172.17.35.74                   Ready    <none>   28h    v1.19.10-r1.0.0-source-28-g913cae0efd63da
hollow-node-66b697bc54-mcwqd   Ready    edge     105m   v0.0.0-master+a34f1e483104b
[root@perf-test-cce-centos ~]# 
# 相关日志
[root@perf-test-cce-1 ~]# cd /var/paas/sys/log/kubemark/
[root@perf-test-cce-1 kubemark]# ls
kubelet_20210812165028469.zip  kubelet_20210812165828469.zip  kubelet_20210812170628469.zip  kubelet_20210812171428469.zip  kubelet_20210812173128471.zip  kubelet.log  kube-proxy.log
[root@perf-test-cce-1 kubemark]# 
```





#### hollow-node.yaml内容如下

```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: hollow-node
  namespace: kubemark
  selfLink: /apis/apps/v1/namespaces/kubemark/deployments/hollow-node
  labels:
    name: hollow-node
  annotations:
    {}
spec:
  replicas: 1
  selector:
    matchLabels:
      name: hollow-node
  template:
    metadata:
      labels:
        name: hollow-node
    spec:
      volumes:
        - name: hollow-configmap
          configMap:
            name: kubemark-configmap
            defaultMode: 420
        - name: logfile
          hostPath:
            path: /var/paas/sys/log/kubemark/
            type: ''
        - name: paas-crypto-path
          hostPath:
            path: /var/paas/srv/kubernetes/
            type: ''
        - name: pki
          hostPath:
            path: /opt/cloud/cce/kubernetes/kubelet/pki/
            type: ''
      containers:
        - name: hollow-kubelet
          image: 'swr.cn-south-1.myhuaweicloud.com/fxq/kubemark-x86-1.15:v2'
          command:
            - /bin/sh
          args:
            - '-c'
            - /kubemark --morph=kubelet --name=$(NODE_NAME) --kubeconfig=/kubeconfig/kubelet.kubeconfig/kubeconfig $(CONTENT_TYPE) --alsologtostderr --v=2 1>>/var/log/kubemark/kubelet.log 2>&1;while true; do echo hello; sleep 10; done
          ports:
            - containerPort: 4194
              protocol: TCP
            - containerPort: 10250
              protocol: TCP
            - containerPort: 10255
              protocol: TCP
          env:
            - name: PAAS_CRYPTO_PATH
              value: /var/paas/srv/kubernetes/
            - name: CONTENT_TYPE
              valueFrom:
                configMapKeyRef:
                  name: node-configmap
                  key: content.type
            - name: NODE_NAME
              valueFrom:
                fieldRef:
                  apiVersion: v1
                  fieldPath: metadata.name
          resources: {}
          volumeMounts:
            - name: pki
              mountPath: /opt/cloud/cce/kubernetes/kubelet/pki/
            - name: hollow-configmap
              mountPath: /kubeconfig/kubeproxy.kubeconfig
            - name: hollow-configmap
              mountPath: /kubeconfig/kubelet.kubeconfig
            - name: paas-crypto-path
              mountPath: /var/paas/srv/kubernetes/
            - name: logfile
              mountPath: /var/log/kubemark
              policy:
                logs:
                  rotate: Daily
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          imagePullPolicy: IfNotPresent
          securityContext:
            privileged: true
        - name: hollow-proxy
          image: 'swr.cn-south-1.myhuaweicloud.com/fxq/kubemark-x86-1.15:v2'
          command:
            - /bin/sh
          args:
            - '-c'
            - bash sed-server.sh; /kubemark --morph=proxy --name=$(NODE_NAME) --kubeconfig=/kubeconfig/kubeproxy.kubeconfig/kubeconfig $(CONTENT_TYPE) --alsologtostderr --v=2 1>>/var/log/kubemark/kube-proxy.log 2>&1;while true; do echo hello; sleep 10; done
          env:
            - name: PAAS_CRYPTO_PATH
              value: /var/paas/srv/kubernetes/
            - name: CONTENT_TYPE
              valueFrom:
                configMapKeyRef:
                  name: node-configmap
                  key: content.type
            - name: NODE_NAME
              valueFrom:
                fieldRef:
                  apiVersion: v1
                  fieldPath: metadata.name
          resources: {}
          volumeMounts:
            - name: paas-crypto-path
              mountPath: /var/paas/srv/kubernetes/
            - name: hollow-configmap
              mountPath: /kubeconfig/kubelet.kubeconfig
            - name: logfile
              mountPath: /var/log/kubemark
              policy:
                logs:
                  rotate: Daily
            - name: pki
              mountPath: /opt/cloud/cce/kubernetes/kubelet/pki/
            - name: hollow-configmap
              mountPath: /kubeconfig/kubeproxy.kubeconfig
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          imagePullPolicy: IfNotPresent
          securityContext:
            privileged: true
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      dnsPolicy: ClusterFirst
      automountServiceAccountToken: false
      securityContext: {}
      schedulerName: default-scheduler
      dnsConfig:
        options:
          - name: single-request-reopen
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  revisionHistoryLimit: 10
  progressDeadlineSeconds: 600
```





#### 带有容忍和标签的yaml

```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: hollow-node
  namespace: kubemark
  selfLink: /apis/apps/v1/namespaces/kubemark/deployments/hollow-node
  labels:
    name: hollow-node
  annotations:
    {}
spec:
  replicas: 1
  selector:
    matchLabels:
      name: hollow-node
  template:
    metadata:
      labels:
        name: hollow-node
    spec:
      volumes:
        - name: hollow-configmap
          configMap:
            name: kubemark-configmap
            defaultMode: 420
        - name: logfile
          hostPath:
            path: /var/paas/sys/log/kubemark/
            type: ''
        - name: paas-crypto-path
          hostPath:
            path: /var/paas/srv/kubernetes/
            type: ''
        - name: pki
          hostPath:
            path: /opt/cloud/cce/kubernetes/kubelet/pki/
            type: ''
      containers:
        - name: hollow-kubelet
          image: 'swr.cn-south-1.myhuaweicloud.com/fxq/kubemark-x86-1.15:v2'
          command:
            - /bin/sh
          args:
            - '-c'
            - /kubemark --morph=kubelet --name=$(NODE_NAME) --kubeconfig=/kubeconfig/kubelet.kubeconfig/kubeconfig $(CONTENT_TYPE) --alsologtostderr --v=2 1>>/var/log/kubemark/kubelet.log 2>&1;while true; do echo hello; sleep 10; done
          ports:
            - containerPort: 4194
              protocol: TCP
            - containerPort: 10250
              protocol: TCP
            - containerPort: 10255
              protocol: TCP
          env:
            - name: PAAS_CRYPTO_PATH
              value: /var/paas/srv/kubernetes/
            - name: CONTENT_TYPE
              valueFrom:
                configMapKeyRef:
                  name: node-configmap
                  key: content.type
            - name: NODE_NAME
              valueFrom:
                fieldRef:
                  apiVersion: v1
                  fieldPath: metadata.name
          resources: {}
          volumeMounts:
            - name: pki
              mountPath: /opt/cloud/cce/kubernetes/kubelet/pki/
            - name: hollow-configmap
              mountPath: /kubeconfig/kubeproxy.kubeconfig
            - name: hollow-configmap
              mountPath: /kubeconfig/kubelet.kubeconfig
            - name: paas-crypto-path
              mountPath: /var/paas/srv/kubernetes/
            - name: logfile
              mountPath: /var/log/kubemark
              policy:
                logs:
                  rotate: Daily
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          imagePullPolicy: IfNotPresent
          securityContext:
            privileged: true
        - name: hollow-proxy
          image: 'swr.cn-south-1.myhuaweicloud.com/fxq/kubemark-x86-1.15:v2'
          command:
            - /bin/sh
          args:
            - '-c'
            - bash sed-server.sh; /kubemark --morph=proxy --name=$(NODE_NAME) --kubeconfig=/kubeconfig/kubeproxy.kubeconfig/kubeconfig $(CONTENT_TYPE) --alsologtostderr --v=2 1>>/var/log/kubemark/kube-proxy.log 2>&1;while true; do echo hello; sleep 10; done
          env:
            - name: PAAS_CRYPTO_PATH
              value: /var/paas/srv/kubernetes/
            - name: CONTENT_TYPE
              valueFrom:
                configMapKeyRef:
                  name: node-configmap
                  key: content.type
            - name: NODE_NAME
              valueFrom:
                fieldRef:
                  apiVersion: v1
                  fieldPath: metadata.name
          resources: {}
          volumeMounts:
            - name: paas-crypto-path
              mountPath: /var/paas/srv/kubernetes/
            - name: hollow-configmap
              mountPath: /kubeconfig/kubelet.kubeconfig
            - name: logfile
              mountPath: /var/log/kubemark
              policy:
                logs:
                  rotate: Daily
            - name: pki
              mountPath: /opt/cloud/cce/kubernetes/kubelet/pki/
            - name: hollow-configmap
              mountPath: /kubeconfig/kubeproxy.kubeconfig
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          imagePullPolicy: IfNotPresent
          securityContext:
            privileged: true
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      dnsPolicy: ClusterFirst
      automountServiceAccountToken: false
      securityContext: {}
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: kubemark
                    operator: In
                    values:
                      - kubemark
      tolerations:
        - key: key1
          operator: Equal
          value: value1
          effect: NoExecute
          tolerationSeconds: 300
      priorityClassName: high-priority
      schedulerName: default-scheduler
      dnsConfig:
        options:
          - name: single-request-reopen
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  revisionHistoryLimit: 10
  progressDeadlineSeconds: 600
```


