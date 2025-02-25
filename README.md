English | [简体中文](README-zh_CN.md)

**This script can renew kubernetes cluster certificates that have expired or are about to expire**.

**This script can renew any version's k8s cluster certificate (clusters initialized with kubeadm)**

The certificates that generated by kubeadm are valid for only 1 year. This script can extend that duration to 10 years.

This script only handles master node's certificates. Kubelet certificates of worker nodes will be renewed automatically.

# 1. Usage

- Only to renew master nodes' certificate if etcd doesn't has certificate. [See this](/other.md#1-update-master-nodes-certificate-only) (etcd doesn't use TLS encrypted connection by default if the k8s version is less than `v1.9.x`)

- Use the following steps to renew otherwise:

## 1.1 Download the script

```
git clone https://github.com/yuyicai/update-kube-cert.git
cd update-kubeadm-cert
chmod 755 update-kubeadm-cert.sh
```

## 1.2 Renew the certificate

**If you use `containerd` as CRI runtime, use `update-kubeadm-cert-crictl.sh` instead of `update-kubeadm-cert.sh`**

Use `./update-kubeadm-cert.sh all` or `bash update-kubeadm-cert.sh all` to execute it. Please do not use `sh update-kubeadm-cert.sh all`，Because some of Linux distributions doesn't link sh to bash. it may cause the problem of compatibility.

**Execute on every master node if the cluster has more than one**

```
./update-kubeadm-cert.sh all
```

The output should be like this:

```
CERTIFICATE                                       EXPIRES
/etc/kubernetes/controller-manager.config         Sep 12 08:38:56 2022 GMT
/etc/kubernetes/scheduler.config                  Sep 12 08:38:56 2022 GMT
/etc/kubernetes/admin.config                      Sep 12 08:38:56 2022 GMT
/etc/kubernetes/pki/ca.crt                        Sep 11 08:38:53 2031 GMT
/etc/kubernetes/pki/apiserver.crt                 Sep 12 08:38:54 2022 GMT
/etc/kubernetes/pki/apiserver-kubelet-client.crt  Sep 12 08:38:54 2022 GMT
/etc/kubernetes/pki/front-proxy-ca.crt            Sep 11 08:38:54 2031 GMT
/etc/kubernetes/pki/front-proxy-client.crt        Sep 12 08:38:54 2022 GMT
/etc/kubernetes/pki/etcd/ca.crt                   Sep 11 08:38:55 2031 GMT
/etc/kubernetes/pki/etcd/server.crt               Sep 12 08:38:55 2022 GMT
/etc/kubernetes/pki/etcd/peer.crt                 Sep 12 08:38:55 2022 GMT
/etc/kubernetes/pki/etcd/healthcheck-client.crt   Sep 12 08:38:55 2022 GMT
/etc/kubernetes/pki/apiserver-etcd-client.crt     Sep 12 08:38:56 2022 GMT
[2021-09-12T16:41:25.93+0800][INFO] backup /etc/kubernetes to /etc/kubernetes.old-20210912
[2021-09-12T16:41:25.93+0800][INFO] updating...
[2021-09-12T16:41:25.99+0800][INFO] updated /etc/kubernetes/pki/etcd/server.conf
[2021-09-12T16:41:26.04+0800][INFO] updated /etc/kubernetes/pki/etcd/peer.conf
[2021-09-12T16:41:26.07+0800][INFO] updated /etc/kubernetes/pki/etcd/healthcheck-client.conf
[2021-09-12T16:41:26.11+0800][INFO] updated /etc/kubernetes/pki/apiserver-etcd-client.conf
[2021-09-12T16:41:26.54+0800][INFO] restarted etcd
[2021-09-12T16:41:26.60+0800][INFO] updated /etc/kubernetes/pki/apiserver.crt
[2021-09-12T16:41:26.64+0800][INFO] updated /etc/kubernetes/pki/apiserver-kubelet-client.crt
[2021-09-12T16:41:26.69+0800][INFO] updated /etc/kubernetes/controller-manager.conf
[2021-09-12T16:41:26.74+0800][INFO] updated /etc/kubernetes/scheduler.conf
[2021-09-12T16:41:26.79+0800][INFO] updated /etc/kubernetes/admin.conf
[2021-09-12T16:41:26.79+0800][INFO] backup /root/.kube/config to /root/.kube/config.old-20210912
[2021-09-12T16:41:26.80+0800][INFO] copy the admin.conf to /root/.kube/config
[2021-09-12T16:41:26.85+0800][INFO] updated /etc/kubernetes/kubelet.conf
[2021-09-12T16:41:26.88+0800][INFO] updated /etc/kubernetes/pki/front-proxy-client.crt
[2021-09-12T16:41:28.70+0800][INFO] restarted apiserver
[2021-09-12T16:41:29.17+0800][INFO] restarted controller-manager
[2021-09-12T16:41:30.07+0800][INFO] restarted scheduler
[2021-09-12T16:41:30.13+0800][INFO] restarted kubelet
[2021-09-12T16:41:30.14+0800][INFO] done!!!
CERTIFICATE                                       EXPIRES
/etc/kubernetes/controller-manager.config         Sep 11 08:41:26 2031 GMT
/etc/kubernetes/scheduler.config                  Sep 11 08:41:26 2031 GMT
/etc/kubernetes/admin.config                      Sep 11 08:41:26 2031 GMT
/etc/kubernetes/pki/ca.crt                        Sep 11 08:38:53 2031 GMT
/etc/kubernetes/pki/apiserver.crt                 Sep 11 08:41:26 2031 GMT
/etc/kubernetes/pki/apiserver-kubelet-client.crt  Sep 11 08:41:26 2031 GMT
/etc/kubernetes/pki/front-proxy-ca.crt            Sep 11 08:38:54 2031 GMT
/etc/kubernetes/pki/front-proxy-client.crt        Sep 11 08:41:26 2031 GMT
/etc/kubernetes/pki/etcd/ca.crt                   Sep 11 08:38:55 2031 GMT
/etc/kubernetes/pki/etcd/server.crt               Sep 11 08:41:25 2031 GMT
/etc/kubernetes/pki/etcd/peer.crt                 Sep 11 08:41:26 2031 GMT
/etc/kubernetes/pki/etcd/healthcheck-client.crt   Sep 11 08:41:26 2031 GMT
/etc/kubernetes/pki/apiserver-etcd-client.crt     Sep 11 08:41:26 2031 GMT
```

The following certificates and kubeconfig files will be modified:

```
/etc/kubernetes
├── admin.conf
├── controller-manager.conf
├── scheduler.conf
├── kubelet.conf
└── pki
    ├── apiserver.crt
    ├── apiserver-etcd-client.crt
    ├── apiserver-kubelet-client.crt
    ├── front-proxy-client.crt
    └── etcd
        ├── healthcheck-client.crt
        ├── peer.crt
        └── server.crt
```

**[More info](/other.md)**

# 2. Rollback if failed to renew

The script will back up the `/etc/kubernetes` directory into `/etc/kubernetes.old-$(date +%Y%m%d)` (for example: `kubernetes.old-20200325`)

If the the script is failed to be executed, use the backup directory to overide the `/etc/kubernetes` directory.

# 3. Other things

For the clusters of version `v1.15.x` or higher, there is a command `kubeadm alpha certs renew <cert_name>` that can renew the certificate. Each time you run this command, the certificate will be extended by 1 year.

**Note:** For clutsers of version `v1.15.x` and `v1.16.x`, there is a bug on `kubeadm alpha certs renew <cert_name>` command. You need to handle this mannually. See [this](/other.md#4-handle-kubeadm-command-bug-manually)

This script can handle this so you don't need to worry about that bug.
