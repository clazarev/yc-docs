---
title: Best practices for using {{ network-load-balancer-full-name }}
description: This page describes best practices for using {{ network-load-balancer-name }}.
---

# Best practices


### Use different availability zones {#place-resources-in-different-zones}

Create cloud resources in multiple availability zones. This way you can maintain the availability of your applications in case one of the zones becomes unavailable.

### Allocate resources across zones uniformly {#distribute-resources}

The same amount of cloud resources should be hosted in each availability zone. If there are three VMs in the `{{ region-id }}-a` availability zone, the `{{ region-id }}-b` and `{{ region-id }}-d` availability zones should also host three VMs each.

### Create cloud resources with redundancy {#create-reserves}

If one of the VMs in a certain availability zone fails, that availability zone will continue receiving the same volume of traffic. This will increase the load on the remaining healthy VMs. To prevent all the VMs from failing, we recommend allocating extra resources in each availability zone in addition to the resources required for supporting the design load.

### Assign a dedicated balancer to each app {#one-balancer-one-app}

When deploying multiple applications in {{ yandex-cloud }}, create an independent load balancer for each application.

### Set up a multi-layer infrastructure {#multi-level-infrastructure}

In some cases, such as organizing an [MK8S](../../managed-kubernetes/index.yaml) multi-cluster, where a separate zonal cluster is deployed in each availability zone, you may need multi-level traffic distribution. At input, the traffic will be received by an L7 load balancer, which will distribute it to one of the zonal clusters. Next, at the zonal cluster level, the traffic will be received by a [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer) type service (built around an L3 load balancer), and from there distributed among the relevant cluster pods for processing.   

You can use the [Application Load Balancer](../../network-load-balancer/index.yaml) as an L7 load balancer.

You can use the [Network Load Balancer](../../application-load-balancer/index.yaml) as L3 load balancers.

The multi-level balancing schemes, where an L3 load balancer distributes traffic to L7 load balancers downstream of it, are not supported and are not recommended.

## Use cases {#examples}

* [{#T}](../tutorials/web-service.md)
* [{#T}](../tutorials/route-switcher.md)
* [{#T}](../tutorials/vm-autoscale/index.md)
* [{#T}](../tutorials/migration-from-nlb-to-alb/index.md)
