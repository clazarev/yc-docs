#### Why does my cluster have N nodes and is not scaling down? {#not-scaling-down}

[Autoscaling](../../managed-kubernetes/concepts/autoscale.md) does not stop nodes with [pods](../../managed-kubernetes/concepts/index.md#pod) that cannot be evicted. The scaling barriers include:
* Pods whose eviction is limited with [PodDisruptionBudget](../../managed-kubernetes/concepts/node-group/node-drain.md).
* Pods in the `kube-system` [namespace](../../managed-kubernetes/concepts/index.md#namespace):
  * Those not created under the [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) controller.
  * Those without `PodDisruptionBudget` installed or those whose eviction is limited with `PodDisruptionBudget`.
* Pods that were not created under a replication controller ([ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/), [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/), or [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)).
* Pods with `local-storage`.
* Pods that cannot be evicted anywhere due to limitations. For example, due to lack of resources or lack of nodes matching the [affinity or anti-affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity) selectors.
* Pods with an annotation that prohibits eviction: `"cluster-autoscaler.kubernetes.io/safe-to-evict": "false"`.

{% note info %}

You can evict `kube-system` pods, pods with `local-storage`, and pods without a replication controller. To do this, set the `"safe-to-evict": "true"` annotation:

```bash
kubectl annotate pod <pod_name> cluster-autoscaler.kubernetes.io/safe-to-evict=true
```

{% endnote %}

Other possible causes include:
* The [node group](../../managed-kubernetes/concepts/index.md#node-group) has already reached its minimum size.
* The node is idle for less than 10 minutes.
* During the last 10 minutes, the node group has been scaled up.
* During the last 3 minutes, there was an unsuccessful attempt to scale down the node group.
* There was an unsuccessful attempt to stop a certain node. In this case, the next attempt occurs in 5 minutes.
* The node has an annotation that prohibits stopping it when scaling it down: `"cluster-autoscaler.kubernetes.io/scale-down-disabled": "true"`. You can add or remove an annotation using `kubectl`.

  Check for annotation on the node:

  ```bash
  kubectl describe node <node_name> | grep scale-down-disabled
  ```

  Result:

  ```bash
  Annotations:        cluster-autoscaler.kubernetes.io/scale-down-disabled: true
  ```

  Set the annotation:

  ```bash
  kubectl annotate node <node_name> cluster-autoscaler.kubernetes.io/scale-down-disabled=true
  ```

  You can remove an annotation by running the `kubectl` command with `-`:

  ```bash
  kubectl annotate node <node_name> cluster-autoscaler.kubernetes.io/scale-down-disabled-
  ```
  
#### In an autoscaling group, the number of nodes never scales down to one, even when there is no load {#autoscaler-one-node}

In a {{ managed-k8s-name }} cluster, the `kube-dns-autoscaler` app decides on the number of CoreDNS replicas. If the `preventSinglePointFailure` parameter in the `kube-dns-autoscaler` configuration is set to `true` and there is more than one node in the group, the minimum number of CoreDNS replicas is two. In this case, the Cluster Autoscaler cannot scale down the number of nodes in the cluster below that of CoreDNS pods.

Learn more about DNS scaling based on the cluster size [here](../../tutorials/container-infrastructure/dns-autoscaler.md).

**Solution**:

1. Disable the protection setting that limits the minimum number of CoreDNS replicas to two. To do this, set the `preventSinglePointFailure` parameter to `false` in the `kube-dns-autoscaler` [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/).
1. Enable the `kube-dns-autoscaler` pod eviction by adding the `save-to-evict` annotation to [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/):

    ```bash
    kubectl patch deployment kube-dns-autoscaler -n kube-system \
      --type merge \
      -p '{"spec":{"template":{"metadata":{"annotations":{"cluster-autoscaler.kubernetes.io/safe-to-evict":"true"}}}}}'
    ```

#### Why does the node group fail to scale down after a pod deletion? {#not-scaling-pod}

If the node is underloaded, it is removed in 10 minutes.

#### Why does autoscaling fail to trigger even though the number of nodes is below the minimum or exeeds the maximum? {#beyond-limits}

Autoscaling will not violate the preset limits, but {{ managed-k8s-name }} does not explicitly control the limits. Upscaling will only happen if there are pods in an `unschedulable` status.

#### Why do _Terminated_ pods remain in my cluster? {#terminated-pod}

This happens because the [Pod garbage collector (PodGC)](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#pod-garbage-collection) fails to delete these pods during autoscaling. For more information, see [Deleting Terminated pods](../../managed-kubernetes/operations/autoscale.md#delete-terminated).

To get answers to other questions about autoscaling, see the [{{ k8s }} documentation](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#table-of-contents).

#### Is Horizontal Pod Autoscaler supported? {#horizontal-pod-autoscaler}

Yes, {{ managed-k8s-name }} supports [horizontal pod autoscaling](../../managed-kubernetes/concepts/autoscale.md#hpa).
