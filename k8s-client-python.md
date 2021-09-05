### 1、官方支持的 Kubernetes 客户端库



| Python | [github.com/kubernetes-client/python/](https://github.com/kubernetes-client/python/) | [浏览](https://github.com/kubernetes-client/python/tree/master/examples) |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |

### 2、社区维护的客户端库

| Python | [github.com/mnubo/kubernetes-py](https://github.com/mnubo/kubernetes-py) |
| ------ | ------------------------------------------------------------ |



### 3、安装模块

```shell
#pip install kubernetes
#pip install kubernetes-py
Looking in indexes: http://cmc-cd-mirror.rnd.huawei.com/pypi/simple/
Collecting kubernetes-py
  Downloading http://cmc-cd-mirror.rnd.huawei.com/pypi/packages/76/61/eace72fb588238e328d27333d54a0b09082ba81d1a1d9d853f7c64ed306c/kubernetes_py-1.10.14-py3-none-any.whl (200 kB)
Requirement already satisfied: six>=1.10.0 in c:\users\f00475147\appdata\roaming\python\python37\site-packages (from kubernetes-py) (1.15.0)
Requirement already satisfied: PyYAML>=3.13 in c:\users\f00475147\appdata\roaming\python\python37\site-packages (from kubernetes-py) (5.3.1)
Requirement already satisfied: requests>=2.10.0 in c:\users\f00475147\appdata\roaming\python\python37\site-packages (from kubernetes-py) (2.24.0)
Collecting python-dateutil>=2.6.0
  Downloading http://cmc-cd-mirror.rnd.huawei.com/pypi/packages/36/7a/87837f39d0296e723bb9b62bbb257d0355c7f6128853c78955f57342a56d/python_dateutil-2.8.2-py2.py3-none-any.whl (247 kB)
Requirement already satisfied: six>=1.10.0 in c:\users\f00475147\appdata\roaming\python\python37\site-packages (from kubernetes-py) (1.15.0)
Requirement already satisfied: chardet<4,>=3.0.2 in c:\users\f00475147\appdata\roaming\python\python37\site-packages (from requests>=2.10.0->kubernetes-py) (3.0.4)
Requirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in c:\users\f00475147\appdata\roaming\python\python37\site-packages (from requests>=2.10.0->kubernetes-py) (1.25.11)
Requirement already satisfied: certifi>=2017.4.17 in c:\users\f00475147\appdata\roaming\python\python37\site-packages (from requests>=2.10.0->kubernetes-py) (2020.12.5)
Requirement already satisfied: idna<3,>=2.5 in c:\users\f00475147\appdata\roaming\python\python37\site-packages (from requests>=2.10.0->kubernetes-py) (2.10)
Collecting uuid>=1.30
  Downloading http://cmc-cd-mirror.rnd.huawei.com/pypi/packages/ce/63/f42f5aa951ebf2c8dac81f77a8edcc1c218640a2a35a03b9ff2d4aa64c3d/uuid-1.30.tar.gz (5.8 kB)
Using legacy 'setup.py install' for uuid, since package 'wheel' is not installed.
Installing collected packages: uuid, python-dateutil, kubernetes-py
    Running setup.py install for uuid: started
    Running setup.py install for uuid: finished with status 'done'
Successfully installed kubernetes-py-1.10.14 python-dateutil-2.8.2 uuid-1.30
WARNING: You are using pip version 20.3.1; however, version 21.2.1 is available.
You should consider upgrading via the 'd:\program files (x86)\python37\python.exe -m pip install --upgrade pip' command.

```

### 4、token方式

#### （1）获取API cluster URL与token

```shell
# 获取Cluster URL地址
[root@test1511-x86-13449 ~]# kubectl config view --minify | grep server | cut -f 2-  -d":" | tr -d " "
https://10.12.2.30:5443
```

```yaml
# 创建k8s admin-token.yaml
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: admin
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: admin
  namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin
  namespace: kube-system
  labels:
    kubernetes.io/cluster-service: "true"
    addonmanager.kubernetes.io/mode: Reconcile

```

执行yaml文件

```shell
# 执行yaml文件
kubectl apply -f admin-token.yaml
# 获取token值
[root@test1511-x86-13449 ~]# kubectl get secret -nkube-system |grep admin |awk '{print $1}' 
admin-token-fpbrw
[root@test1511-x86-13449 ~]# kubectl describe secret admin-token-fpbrw  -nkube-system | grep token: | awk '{print $2}'
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi10b2tlbi1mcGJydyIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJhZG1pbiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImE1Zjg5OTRiLWY3MWItNDgxNS05ZDk1LTk4YjQzZDc4YjE5ZiIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlLXN5c3RlbTphZG1pbiJ9.WeiE1QpGYktsIYHzSH37NiMnK6o3t-8z16S5X3JvlRJPYpKEQMvzwB5kese9ujyHRZDROcBoIviZLMo69G38fl32uVYeNFG06HJCRbeBz2GqM4zuJ7xtRu-tae1niKC9006kMLD0A9592oiFrQhPkK1UckjupZae9JqaMSWgYq3ndLPSPOSA4GFhmJr--O7Qw3Si0Gg21DDllF1CbHXV3caFWIR0IvdETFf4qECgxMf1qaa9pBxAIifkr5OhRGeZWBSMcpoE0IpR937Csi2EKwyKoSUmj5-N-9Ts9ixy80N6YOgsClgHvZogdLcNGwCwbVXQ0z1iMcROdBb18K098g

# 命令整合
kubectl describe secret/$(kubectl get secret -nkube-system |grep admin|awk '{print $1}') -nkube-system|grep token: | awk '{print $2}'
```

kubectl get secret -nkube-system admin-token-fpbrw -oyaml
可以看到base64解密后和describe获取到的一致

#### （2）创建目录结构

```shell
# mkdir -p /kube/auth
# cd /kube/auth
# vim token.txt
```

将刚才获取的Token字符串复制到该文件，比如：eyJhbGciOiJSUzI1NiIsImtxxxx

这里我们获取的token会引入到我们的脚本下, 作为bearer authorization的api key与远程k8s API建立认证连接.

#### （3）编写python client脚本

```python
#! /usr/bin/python3
# coding: utf-8

import os
from kubernetes import client, config
import yaml

class kubernetes_tools(object):
    def __init__(self, k8s_url, token_file):
        self.k8s_url = k8s_url
        self.token_file = token_file

    def get_token(self):
        """
        从文件读取token
        :return:
        """
        with open(self.token_file, 'r') as file:
            token = file.read().strip("\n")
            return token

    def get_api_instance(self):
        """
        获取API的CoreV1Api版本对象
        :return:
        """
        configuration = client.Configuration()
        configuration.host = self.k8s_url
        configuration.verify_ssl = False
        configuration.api_key = {"authorization": "Bearer " + self.get_token()}
        client1 = client.api_client.ApiClient(configuration=configuration)
        api_instance = client.CoreV1Api(client1)
        return api_instance

    def get_namespace_list(self):
        """
        获取命名空间列表
        :return:
        """
        api_instance = self.get_api_instance()
        namespace_list = []
        for ns in api_instance.list_namespace().items:
            print(ns.metadata.name)
            namespace_list.append(ns.metadata.name)
        return namespace_list


if __name__ == '__main__':
    k8s_url = 'https://100.85.219.65:5443'
    token_file = os.getcwd() + "\\1511-token.txt"
    namespace_list = kubernetes_tools(k8s_url, token_file).get_namespace_list()
    print(namespace_list)

```



### 5、kube-config文件方式

（1）获取kube-config文件

（2）编写python client代码

```python
# coding:utf-8
from kubernetes import config

if __name__ == '__main__':
    config_file = input("请输入集群kube-config文件路径：").strip()
    config.kube_config.load_kube_config(config_file=config_file)
```



### 6、get client函数

方法一：

```python
def get_client(token_file, k8s_url):
    """
    获取client配置对象
    :param token_file:
    :param k8s_url:
    :return: client1
    """
    with open(token_file, 'r') as file:
        token = file.read().strip("\n")
    configuration = Configuration()
    configuration.host = k8s_url
    configuration.verify_ssl = False
    configuration.api_key = {"authorization": "Bearer " + token}
    client1 = api_client.ApiClient(configuration=configuration)
    return client1

k8s_url = 'https://100.85.219.65:5443'
token_file = "1511-token.txt"
client1 = get_client(token_file, k8s_url)
api_instance_CoreV1 = client.CoreV1Api(client1)
```

方法二：

```python
config_file = "config.yaml"
config.load_kube_config(config_file=config_file)
api_instance_CoreV1 = client.CoreV1Api()
```



### 7、获取默认角色的token---无权限list

```shell
root@trunkport-f00475147-1:~# kubectl describe secret default-token-mz5st -nkube-system | grep token: | awk '{print $2}'
eyJhbGciOiJSUzI1NiIsImtpZCI6IllhaW1zYXd4OVF6blNod25zTjFFd1JFUU50RzVnWEp2VXBORUI5Zm9XZmMifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZWZhdWx0LXRva2VuLW16NXN0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImRlZmF1bHQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIzNDhhNmVhNy0xOTRmLTQ1MDEtYWJlNy0xZWRlODE2YjMxNWYiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06ZGVmYXVsdCJ9.gA7iifJM0ALw6GQ44nT38XXBXdJ6RcnBHZGtOxzcbdk54JqOGtEaAEa6OpdFLbsrdjy4qvUgiV8TQQeyW2aRJ1YA6GeDCVLc-w-v9R1od4aOEfYY3HMz4M5P24aToYqcqiX7kxN7HZjSUGJpgxlnXXkHflVJFRRAOkm_S_EqCe_k_yWPQ_f5a4SRCCvsj9VEBLd5BgAmWZ3oKB7PcoVcyr26AJyjZTcQE2CcvXKz9VBkD81DOV0la5MkwlKN1pJ7yh4UgwJgw93z0YIYeyDSe3VOqrsJ2mj65YlqP4o9WQithd09jvDU3I5M3fxSn6U9gfoUie4x-uFfNSXz5vmxuQ

root@trunkport-f00475147-1:~# kubectl get serviceaccount -nkube-system default
NAME      SECRETS   AGE
default   1         17d
root@trunkport-f00475147-1:~# kubectl get serviceaccount -nkube-system default -o yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: "2021-07-22T13:34:47Z"
  name: default
  namespace: kube-system
  resourceVersion: "318"
  selfLink: /api/v1/namespaces/kube-system/serviceaccounts/default
  uid: 348a6ea7-194f-4501-abe7-1ede816b315f
secrets:
- name: default-token-mz5st
root@trunkport-f00475147-1:~# 

  
root@trunkport-f00475147-1:~# kubectl get secret -nkube-system | grep default
default-secret                                   kubernetes.io/dockerconfigjson        1      17d
default-token-mz5st                              kubernetes.io/service-account-token   3      17d


```


