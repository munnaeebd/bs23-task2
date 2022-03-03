# bs23-task2

## LXD Installation
```
sudo snap install lxd
sudo lxd init
```
## container profile create
```
lxc profile create kubernetes
lxc profile edit kubernetes

# Insert the below configuration

config:
  boot.autostart: "true"
  linux.kernel_modules: ip_vs,ip_vs_rr,ip_vs_wrr,ip_vs_sh,ip_tables,ip6_tables,netlink_diag,nf_nat,overlay,br_netfilter
  raw.lxc: |
    lxc.apparmor.profile=unconfined
    lxc.mount.auto=proc:rw sys:rw cgroup:rw
    lxc.cgroup.devices.allow=a
    lxc.cap.drop=
  security.nesting: "true"
  security.privileged: "true"
description: ""
devices:
  aadisable:
    path: /sys/module/nf_conntrack/parameters/hashsize
    source: /sys/module/nf_conntrack/parameters/hashsize
    type: disk
  aadisable1:
    path: /sys/module/apparmor/parameters/enabled
    source: /dev/null
    type: disk
  aadisable3:
    path: /dev/kmsg
    source: /dev/kmsg
    type: disk
name: kubernetes
used_by: []
```
## Container Create

```
lxc launch --profile default --profile kubernetes ubuntu:20.04 master
lxc launch --profile default --profile kubernetes ubuntu:20.04 worker1
lxc launch --profile default --profile kubernetes ubuntu:20.04 worker2

# login to the container to set password.
 lxc exec master bash
 ..
 ..
```

## Creating Kubernetes cluster using Ansible
```
ansible-playbook users.yml
ansible-playbook install-k8s.yaml
ansible-playbook master.yaml
ansible-playbook join-workers.yaml
```

## Kubernetes Dashboard and metric server
```
kubectl create -f dashboard-admin-user.yaml

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.5.0/aio/deploy/recommended.yaml
kubectl patch -n kubernetes-dashboard svc kubernetes-dashboard --type='json' -p '[{"op":"replace","path":"/spec/type","value":"NodePort"}]'

kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```


