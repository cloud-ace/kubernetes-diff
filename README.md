# kubernetes-diff

`kubernetes-diff` は [Kubernetes](https://github.com/kubernetes/kubernetes) の差分を追い、バージョンアップにおけるワークロードへの影響を調査することを目的としています。
差分を追う対象となるのは、以下2つのOSSについてです。

- [Kubernetes](https://github.com/kubernetes/kubernetes)
- [ingress-gcs](https://github.com/kubernetes/ingress-gce)

## HowTo
まとめる差分は `GKEで運用する際に影響するもの` に絞ります。
例えば `kubeadm` や `kubelet` への変更はスルーしますが、 `Deployment` リソースのデフォルト定義値が変わったことはまとめます。

### 見るところ
それぞれの `CHANGELOG` を全て読む。
