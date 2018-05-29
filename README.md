Overview
--------

`cniguru` is a tool that can be used to troubleshoot containers networking.
It provides information about node interfaces used by docker and kubernetes containers:
- the name, MAC address and MTU of the host interfaces used by containers
- the bridge the interfaces are connected to

License
-------

Licensed under either of

* Apache License, Version 2.0, (LICENSE-APACHE or http://www.apache.org/licenses/LICENSE-2.0)
* MIT license (LICENSE-MIT or http://opensource.org/licenses/MIT)

at your option.

Examples
--------

* List the interfaces for a kubernetes pod in a human readable format:

```bash
[root@kh1 ~]# KUBECONFIG=/etc/kubernetes/admin.conf kubectl get pod
NAME                              READY     STATUS    RESTARTS   AGE
netshoot-6d994df756-v9rgf         1/1       Running   0          2m
serve-hostname-86bc9d96dc-8cb49   1/1       Running   0          2m
[root@kh1 ~]# 
[root@kh1 ~]# ./cniguru pod netshoot-6d994df756-v9rgf

CONTAINER_ID  NODE  INTERFACE     MTU   MAC_ADDRESS        BRIDGE
7180b102b955  kh1   vethe8c8302a  1460  a6:eb:27:39:dd:dd  cni0
7180b102b955  kh1   veth593daf68  1500  e2:31:16:b3:66:40  br_dc_test
```

* Present the output in JSON format:

```bash
[root@kh1 ~]# ./cniguru pod serve-hostname-86bc9d96dc-8cb49 -o json
[{"container":{"id":"4eab7e156c75d46bfcc925f9a01e6cdb238a351b4952f5e20074e2cad0d08b65","node_name":"kh1","runtime":"Docker"},"interfaces":[{"name":"veth0cac8bed","mtu":1500,"mac_address":"42:56:c3:1e:7e:35","bridge":"cni0"}]}]
```

Installation
------------

### Cargo

If you have a rust toolchain setup you can install `cniguru` via cargo:

```
cargo install cniguru
```

### Build from sources

* Make sure you have a newer Rust compiler installed. Run

```
rustup override set stable
rustup update stable
```


* Clone the source code:

```
git clone https://github.com/maximih/cniguru
cd cniguru
```

* Build `cniguru`

```
cargo build --release
```

### Download `x86_64` binary

A statically linked binary for linux `x86_64` is provided [here](https://github.com/maximih/cniguru/releases/download/0.1.0/cniguru_x86_64_0.1.0.tar.gz)

Configuration
-------------

The path to the Kubernetes config can be set via `$KUBECONFIG` env variable.
If `$KUBECONFIG` is not set, `cniguru` will try to use `$HOME/.kube/config` or `/etc/kubernetes/admin.conf`.

Docker related info is fetched using `docker` cli so `cniguru` must be run with an user that has rights to execute docker commands.

Future plans
------------

Some features that might be added in the future:

* run `cniguru` as a kubernetes `daemonset` (server side) and access it via a REST API (client side)
* add possibility to trace packets on different interfaces and different nodes
* add possibility to enable packet logging
