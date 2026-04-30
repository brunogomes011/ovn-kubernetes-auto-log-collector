This project collects OVN-Kubernetes logs automatically during machine config rollouts and Openshift upgrades. 

# How to apply it 

1. Install the machine config

~~~
$ curl -O https://raw.githubusercontent.com/brunogomes011/ovn-kubernetes-auto-log-collector/refs/heads/main/auto-log-collector-machine-config.yaml
$ oc create -f auto-log-collector-machine-config.yaml <--- Change the labels or any desired information before applying the config
~~~

2. Wait the machine config rollout. Take the produced logs and save them in a directory

~~~
$ D="ovn-auto-log-collector-all-nodes"; mkdir -p $D; for n in $(oc get nodes --no-headers | awk '{print $1}'); \
do mkdir -p $D/$n; oc debug node/$n --quiet=true -- chroot /host sh -c 'cd /var/tmp && tar -czf - ovn-auto-log-collector-* 2>/dev/null' | \
tar -xzf - -C $D/$n 2>/dev/null; done; tar -czf $D.tar.gz $D && rm -rf $D
~~~

3. Once the tests are finished, delete the machine config and the produced logs:

~~~
$ oc delete -f auto-log-collector-machine-config.yaml
$ oc debug node/<node-name>
$ chroot /host
$ rm -rf /var/tmp/ovn-auto-log-collector-*
~~~

Attention: This is not officially supported tool and the owner is not responsible for its use. It is still under testing.
