# Troubleshooting the EZD chart


## Helm install returns 'Error: rendered manifests contain a resource that already exists. Unable to continue with install: CustomResourceDefinition "" in namespace "" exists and cannot be imported into the current release: invalid ownership metadata; annotation validation error: key "" must equal "": current value is ""'

After runing:

```bash
helm upgrade --install ezd-crd -n default linuxpolska/ezd-crd --wait=true

```

You might see an errors similar to:

```bash

Error: rendered manifests contain a resource that already exists. Unable to continue with install: CustomResourceDefinition "backups.postgresql.cnpg.io" in namespace "" exists and cannot be imported into the current release: invalid ownership metadata; annotation validation error: key "meta.helm.sh/release-namespace" must equal "default": current value is "xyz"

Error: rendered manifests contain a resource that already exists. Unable to continue with install: CustomResourceDefinition "rabbitmqclusters.rabbitmq.com" in namespace "" exists and cannot be imported into the current release: invalid ownership metadata; annotation validation error: key "meta.helm.sh/release-namespace" must equal "default": current value is "xyz"

```

This means that you have already installed crds and operators in your k8s cluster. If you remove CRDs you lost data!!! 
  

Solution:

```bash

kubectl get crd -o name | grep -E "(rabbitmqclusters.rabbitmq.com|postgresql.cnpg.io)" | xargs -I {} kubectl patch {} -p '
{
    "metadata": {
        "annotations": {
            "meta.helm.sh/release-name": "ezd-crd",
            "meta.helm.sh/release-namespace": "default"
        }
    }
}
'

```




## kubectl get po returns 'Terminating status'


After runing:

```bash

kubectl get po

```

You might see pods remain in status Terminating similar to:

```bash

NAME                READY   STATUS        RESTARTS   AGE
rabbitmq-server-0   1/1     Terminating   0          24m


```

There are many reasons of this error:
- removed ezd-crd chart before ezd-backend chart 
- problems with CSI plugin
- problems with k8s worker nodes
  

Solution:

```bash

kubectl delete --force --grace-period=0 po/rabbitmq-server-0

```












[qwiatu@qwiatu-lp ezd]$ kubectl get po
[qwiatu@qwiatu-lp ezd]$ kubectl delete po rabbitmq-server-0 
pod "rabbitmq-server-0" deleted
^C
[qwiatu@qwiatu-lp ezd]$ kubectl delete po rabbitmq-server-0 --force --grace-period-0
Error: unknown flag: --grace-period-0
See 'kubectl delete --help' for usage.
warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
pod "rabbitmq-server-0" force deleted

