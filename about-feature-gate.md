# About Feature Gate
Feature Gate とは、k8sにおけるわりかし新機能(Alpha/Beta)を使う場合に、有効化/無効化するための機能

## Feature Gate のリファレンス

https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/

上記にON/OFFできる機能と、バージョン毎のデフォルト値が一覧化されている

## GKE でどのFeature Gateが有効/無効になっているかを確認するには

### Master

以下のコマンドで、APIサーバの起動オプションが見れるのでそこで確認できるはず

```bash
kubectl cluster-info dump
```

ただし、Feature GateのOn/Off一覧を確認するコマンドはなさそう

参考: https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/

### Node

NodeにSSHログインして以下のファイルを参照する

* /etc/default/kubelet
  * kubelet の起動オプション
  * https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/
* /home/kubernetes/kubelet-config.yaml
  * kubelet のコンフィグファイル
  * https://kubernetes.io/docs/tasks/administer-cluster/kubelet-config-file/

#### /etc/default/kubelet

GKE v1.11.7-gke.12 の内容

```text
KUBELET_OPTS="--v=2 --cloud-provider=gce --experimental-mounter-path=/home/kubernetes/containerized_mounter/mounter --experimental-check-node-capabilities-before-mount=true --cert-dir=/var/lib/kubelet/pki/ --cni-bin-dir=/home/kubernetes/bin --allow-privileged=true --kubeconfig=/var/lib/kubelet/kubeconfig --experimental-kernel-memcg-notification=true --max-pods=110 --network-plugin=kubenet --node-labels=beta.kubernetes.io/fluentd-ds-ready=true,cloud.google.com/gke-nodepool=default-pool,cloud.google.com/gke-os-distribution=cos,cloud.google.com/gke-preemptible=true --volume-plugin-dir=/home/kubernetes/flexvolume --registry-qps=10 --registry-burst=20 --bootstrap-kubeconfig=/var/lib/kubelet/bootstrap-kubeconfig --node-status-max-images=25 --config /home/kubernetes/kubelet-config.yaml"
KUBE_COVERAGE_FILE="/var/log/kubelet.cov"
```

#### /home/kubernetes/kubelet-config.yaml

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    enabled: false
  x509:
    clientCAFile: /etc/srv/kubernetes/pki/ca-certificates.crt
authorization:
  mode: Webhook
cgroupRoot: /
clusterDNS:
- 10.166.8.10
clusterDomain: cluster.local
enableDebuggingHandlers: true
evictionHard:
  memory.available: 100Mi
  nodefs.available: 10%
  nodefs.inodesFree: 5%
featureGates:
  DynamicKubeletConfig: false
  ExperimentalCriticalPodAnnotation: true
kind: KubeletConfiguration
kubeReserved:
  cpu: 70m
  ephemeral-storage: 23Gi
  memory: 1736Mi
readOnlyPort: 10255
staticPodPath: /etc/kubernetes/manifests
```

## その他・Feature Gateの設定

GKE では、Master/Nodede
一貫して `DynamicKubeletConfig: false` となっている

しかしこの機能のOn/Offによる影響はよくわからなかった
