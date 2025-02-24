---
sidebar_position: 3
---

# API Examples

:::note
Throughout our API documentation, we use `localhost:9003` as the default OpenCost API URL, but your OpenCost instance may be exposed by a service or ingress. To reach the OpenCost API at port 9003, run: `kubectl -n opencost port-forward deployment/opencost 9003`. You may also expose the OpenCost UI on port 9090 with the command `kubectl -n opencost port-forward deployment/opencost 9003 9090`.
:::

## `/allocation` Examples

The OpenCost `/allocation` API has query parameters for `window`, `aggregate`, `step`, and `resolution`. Below are several examples of queries using this API. Please refer to the [OpenCost API](api#allocation) for the full API specifics.

### Default OpenCost UI Allocation request

Here is an example of an OpenCost API query for the last 7 days of namespaces every minute:

```sh
curl -G http://localhost:9003/allocation \
  -d window=7d \
  -d aggregate=namespace \
  -d resolution=1m
```

This is the default query for the OpenCost UI and produces [output similar to this](/allocation-example-output.json).

### Allocation data for the last 60m, in steps of 10m, with resolution 1m, aggregated by namespace

```sh
curl -G http://localhost:9003/allocation \
  -d window=60m \
  -d step=10m \
  -d resolution=1m \
  -d aggregate=namespace
```

```json
{
  "code": 200,
  "data": [
    {
      "kube-system": { ... },
      "default": { ... },
      "cost-model": { ... }
    }
  ]
}
```

### Allocation data for the last 9d, in steps of 3d, with a 10m resolution, aggregated by namespace

```sh
curl -G http://localhost:9003/allocation/compute \
  -d window=9d \
  -d step=3d \
  -d resolution=10m \
  -d aggregate=namespace
```

```json
{
  "code": 200,
  "data": [
    {
      "default": { ... },
      "open-cost": { ... },
      "kube-system": { ... }
    },
    {
      "default": { ... },
      "open-cost": { ... },
      "kube-system": { ... }
    },
    {
      "default": { ... },
      "open-cost": { ... },
      "kube-system": { ... }
    }
  ]
}
```

### Allocation data with distributed idle costs

Here's an example of an OpenCost API query that distributes cluster idle costs across all allocations in the cluster:

```sh
curl -G http://localhost:9003/allocation \
  -d window=1d \
  -d shareIdle=true
```

Here's the same query, but distributing the idle costs of each *node* across all allocations on that node:

```sh
curl -G http://localhost:9003/allocation \
  -d window=1d \
  -d shareIdle=true \
  -d idleByNode=true
```

## `/assets` Example

The OpenCost `/assets` API has the `window` query parameter for retrieving the underlying asset data associated with the Kubernetes cluster.

### Last 7 days of Assets request

```sh
curl -G http://localhost:9003/assets -d window=7d
```

```json
{
  "code": 200,
  "status": "success",
  "data": {
    "GCP/__undefined__/testuser-opencost/Compute/gke-opencost/Node/Kubernetes/gke-opencost-1-default-pool-a30d4801-81dm/gke-opencost-1-default-pool-a30d4801-81dm": {
      "type": "Node",
      "properties": {
        "category": "Compute",
        "provider": "GCP",
        "project": "testuser-opencost",
        "service": "Kubernetes",
        "cluster": "gke-opencost",
        "name": "gke-opencost-1-default-pool-a30d4801-81dm",
        "providerID": "gke-opencost-1-default-pool-a30d4801-81dm"
      },
      "labels": {
        "beta_kubernetes_io_arch": "amd64",
        "beta_kubernetes_io_instance_type": "e2-small",
        "beta_kubernetes_io_os": "linux",
        "cloud_google_com_gke_boot_disk": "pd-balanced",
        "cloud_google_com_gke_container_runtime": "containerd",
        "cloud_google_com_gke_cpu_scaling_level": "2",
        "cloud_google_com_gke_logging_variant": "DEFAULT",
        "cloud_google_com_gke_max_pods_per_node": "110",
        "cloud_google_com_gke_nodepool": "default-pool",
        "cloud_google_com_gke_os_distribution": "cos",
        "cloud_google_com_gke_provisioning": "spot",
        "cloud_google_com_gke_spot": "true",
        "cloud_google_com_gke_stack_type": "IPV4",
        "cloud_google_com_machine_family": "e2",
        "cloud_google_com_private_node": "false",
        "failure_domain_beta_kubernetes_io_region": "australia-southeast1",
        "failure_domain_beta_kubernetes_io_zone": "australia-southeast1-c",
        "kubernetes_io_arch": "amd64",
        "kubernetes_io_hostname": "gke-opencost-1-default-pool-a30d4801-81dm",
        "kubernetes_io_os": "linux",
        "node_kubernetes_io_instance_type": "e2-small",
        "providerID": "gce://testuser-opencost/australia-southeast1-c/gke-opencost-1-default-pool-a30d4801-81dm",
        "topology_gke_io_zone": "australia-southeast1-c",
        "topology_kubernetes_io_region": "australia-southeast1",
        "topology_kubernetes_io_zone": "australia-southeast1-c"
      },
      "window": {
        "start": "2024-06-22T00:00:00Z",
        "end": "2024-06-29T00:00:00Z"
      },
      "start": "2024-06-22T00:00:00Z",
      "end": "2024-06-24T18:10:00Z",
      "minutes": 3970.000000,
      "nodeType": "e2-small",
      "pool": "default-pool",
      "cpuCores": 0.500000,
      "ramBytes": 2072743936.000000,
      "cpuCoreHours": 33.083333,
      "ramByteHours": 137146557098.666672,
      "GPUHours": 0.000000,
      "cpuBreakdown": {
        "idle": 1,
        "other": 0,
        "system": 0,
        "user": 0
      },
      "ramBreakdown": {
        "idle": 1,
        "other": 0,
        "system": 0,
        "user": 0
      },
      "preemptible": 1.000000,
      "discount": 0.000000,
      "cpuCost": 0.307185,
      "gpuCost": 0.000000,
      "gpuCount": 0.000000,
      "ramCost": 0.158929,
      "adjustment": 0.000000,
      "overhead": {
        "CpuOverheadFraction": 0.53,
        "RamOverheadFraction": 0.30960337205878574,
        "OverheadCostFraction": 0.45485239048108356
      },
      "totalCost": 0.466114
    },
...
    "GCP/__undefined__/testuser-opencost/Storage/gke-opencost/Disk/Kubernetes/pvc-95211d76-7842-4182-bdb6-de4133f46b78/pvc-95211d76-7842-4182-bdb6-de4133f46b78": {
      "type": "Disk",
      "properties": {
        "category": "Storage",
        "provider": "GCP",
        "project": "testuser-opencost",
        "service": "Kubernetes",
        "cluster": "gke-opencost",
        "name": "pvc-95211d76-7842-4182-bdb6-de4133f46b78",
        "providerID": "pvc-95211d76-7842-4182-bdb6-de4133f46b78"
      },
      "labels": {},
      "window": {
        "start": "2024-06-22T00:00:00Z",
        "end": "2024-06-29T00:00:00Z"
      },
      "start": "2024-06-22T00:00:00Z",
      "end": "2024-06-28T06:30:00Z",
      "minutes": 9030.000000,
      "byteHours": 1292785156096.000000,
      "bytes": 8589934592.000000,
      "byteHoursUsed": 260162444443.716248,
      "byteUsageMax": 2246320128.000000,
      "breakdown": {
        "idle": 1,
        "other": 0,
        "system": 0,
        "user": 0
      },
      "adjustment": 0.000000,
      "totalCost": 0.065973,
      "storageClass": "standard-rwo",
      "volumeName": "pvc-95211d76-7842-4182-bdb6-de4133f46b78",
      "claimName": "prometheus-server",
      "claimNamespace": "prometheus-system"
    }
  }
}
```

## `/cloudCost` Examples

The OpenCost `/cloudCost` API has query parameters for `window`, `aggregate`, and `filter`. Below are several examples of queries using this API. Please refer to the [OpenCost API](api#cloudcost) for the full API specifics.

### Default OpenCost UI Cloud Costs request

Amortized Net Cost per Provider for the last 7 days

```sh
curl -G http://localhost:9003/cloudCost \
  -d window=7d \
  -d aggregate=provider
```

This is the default query for the OpenCost UI Cloud Costs and produces [output similar to this](/cloudCost-example-output.json).

### List Cost per Provider and Service for the past 14 days

This example shows list cost multi-aggregation by Provider and Service.

```sh
curl -G http://localhost:9003/cloudCost \
  -d window=14d \
  -d aggregate=provider,service
```

```json
{
  "code": 200,
  "data": {
    "sets": [
      {
        "cloudCosts": {
          "AWS/AWSBackup": { ... },
          "AWS/AWSCloudTrail": {
            "properties": {
              "provider": "AWS",
              "invoiceEntityID": "297945954695",
              "service": "AWSCloudTrail",
              "category": "Other"
            },
            "window": {
              "start": "2023-10-28T00:00:00Z",
              "end": "2023-10-29T00:00:00Z"
            },
            "listCost": {
              "cost": 19.800393,
              "kubernetesPercent": 0
            },
            "netCost": {
              "cost": 19.800393,
              "kubernetesPercent": 0
            },
            "amortizedNetCost": {
              "cost": 19.800393,
              "kubernetesPercent": 0
            },
            "invoicedCost": {
              "cost": 19.800393,
              "kubernetesPercent": 0
            },
            "amortizedCost": {
              "cost": 19.800393,
              "kubernetesPercent": 0
            }
          },
          "AWS/AWSCostExplorer": { ... },
          "AWS/AWSELB": { ... },
          "AWS/AWSGlobalAccelerator": { ... },
          ...
          "GCP/Secret Manager": { ... }
        },
        "aggregationProperties": [
          "provider",
          "service"
        ]
      }
    ],
    "window": {
      "start": "null",
      "end": "null"
    }
  }
}
```

### All billing items for a period of 3 days

A warning, this may return many megabytes of data depending on the cloud provider usage. Example output was heavily pared down from 17 megabytes.

```sh
curl -G http://localhost:9003/cloudCost \
  -d window=2023-10-28T00:00:00Z,2023-10-31T00:00:00Z
```

```json
{
  "code": 200,
  "data": {
    "sets": [
      {
        "cloudCosts": {
          ...
          "HDEAD-B6F51B-690123/testuser-opencost/GCP/__unallocated__/Management/Kubernetes Engine": {
            "properties": {
              "provider": "GCP",
              "accountID": "testuser-opencost",
              "invoiceEntityID": "HDEAD-B6F51B-690123",
              "service": "Kubernetes Engine",
              "category": "Management",
              "labels": {
                "goog-fleet-project": "",
                "goog-k8s-cluster-location": "australia-southeast1-b",
                "goog-k8s-cluster-name": "opencost-3",
                "longlived": "opencost"
              }
            },
            "window": {
              "start": "2023-10-28T00:00:00Z",
              "end": "2023-10-29T00:00:00Z"
            },
            "listCost": {
              "cost": 2.399978,
              "kubernetesPercent": 1
            },
            "netCost": {
              "cost": 2.399978,
              "kubernetesPercent": 1
            },
            "amortizedNetCost": {
              "cost": 2.399978,
              "kubernetesPercent": 1
            },
            "invoicedCost": {
              "cost": 2.399978,
              "kubernetesPercent": 1
            },
            "amortizedCost": {
              "cost": 2.399978,
              "kubernetesPercent": 1
            }
          },
          "HDEAD-B6F51B-690123/testuser-opencost/GCP/__unallocated__/Network/Networking": {
            "properties": {
              "provider": "GCP",
              "accountID": "testuser-opencost",
              "invoiceEntityID": "HDEAD-B6F51B-690123",
              "service": "Networking",
              "category": "Network"
            },
            "window": {
              "start": "2023-10-28T00:00:00Z",
              "end": "2023-10-29T00:00:00Z"
            },
            "listCost": {
              "cost": 0.46231399999999995,
              "kubernetesPercent": 0
            },
            "netCost": {
              "cost": 0.006336000000000003,
              "kubernetesPercent": 0
            },
            "amortizedNetCost": {
              "cost": 0.006336000000000003,
              "kubernetesPercent": 0
            },
            "invoicedCost": {
              "cost": 0.006336000000000003,
              "kubernetesPercent": 0
            },
            "amortizedCost": {
              "cost": 0.006336000000000003,
              "kubernetesPercent": 0
            }
          },
          ...
          "HDEAD-B6F51B-690123/testuser-opencost/GCP/gke-opencost-3-default-pool-123456f4-kbl5/Other/Compute Engine": {
            "properties": {
              "providerID": "gke-opencost-3-default-pool-123456f4-kbl5",
              "provider": "GCP",
              "accountID": "testuser-opencost",
              "invoiceEntityID": "HDEAD-B6F51B-690123",
              "service": "Compute Engine",
              "category": "Other",
              "labels": {
                "goog-gke-node": "",
                "goog-k8s-cluster-location": "australia-southeast1-b",
                "goog-k8s-cluster-name": "opencost-3",
                "goog-k8s-node-pool-name": "default-pool",
                "longlived": "opencost"
              }
            },
            "window": {
              "start": "2023-10-28T00:00:00Z",
              "end": "2023-10-29T00:00:00Z"
            },
            "listCost": {
              "cost": 0,
              "kubernetesPercent": 0
            },
            "netCost": {
              "cost": 0,
              "kubernetesPercent": 0
            },
            "amortizedNetCost": {
              "cost": 0,
              "kubernetesPercent": 0
            },
            "invoicedCost": {
              "cost": 0,
              "kubernetesPercent": 0
            },
            "amortizedCost": {
              "cost": 0,
              "kubernetesPercent": 0
            }
          },
          ...
        "window": {
          "start": "2023-10-30T00:00:00Z",
          "end": "2023-10-31T00:00:00Z"
        },
        "aggregationProperties": [
          "invoiceEntityID",
          "accountID",
          "provider",
          "providerID",
          "category",
          "service"
        ]
      }
    ],
    "window": {
      "start": "null",
      "end": "null"
    }
  }
}
```

## `/customCost` Datadog External Costs Examples

Please refer to the [OpenCost API](api#external-costs-api) for the full API specifics.

### Totals request, aggregating by domain over the past 7 days:

https://example.opencost.io/model/customCost/total?window=7d&aggregate=domain

Response:
```json
{
  "code": 200,
  "data": {
    "window": {
      "start": "2024-03-14T00:00:00Z",
      "end": "2024-03-21T00:00:00Z"
    },
    "totalBilledCost": 147.37999,
    "totalListCost": 186.98547,
    "customCosts": [
      {
        "id": "",
        "zone": "us",
        "account_name": "Kubecost",
        "charge_category": "",
        "description": "",
        "resource_name": "",
        "resource_type": "",
        "provider_id": "",
        "billedCost": 147.37999,
        "listCost": 186.98547,
        "list_unit_price": 0.082197905,
        "usage_quantity": 120,
        "usage_unit": "",
        "domain": "datadog",
        "cost_source": "observability",
        "aggregate": "datadog"
      }
    ]
  }
}
```

### Totals request, aggregating by providerId over the past 7 days:

https://example.opencost.io/model/customCost/total?window=7d&aggregate=providerId

Response:
```json
{
  "code": 200,
  "data": {
    "window": {
      "start": "2024-03-14T00:00:00Z",
      "end": "2024-03-21T00:00:00Z"
    },
    "totalBilledCost": 147.37999,
    "totalListCost": 186.98546,
    "customCosts": [
      {
        "id": "",
        "zone": "us",
        "account_name": "Kubecost",
        "charge_category": "usage",
        "description": "350+ integrations, alerting, custom metrics & unlimited user accounts",
        "resource_name": "agent_host_count",
        "resource_type": "infra_hosts",
        "provider_id": "42c0ac62-8d80-11ed-96f3-da7ad0900005/agent_host_count",
        "billedCost": 0,
        "listCost": 8.876712,
        "list_unit_price": 0.073972605,
        "usage_quantity": 360,
        "usage_unit": "Infra Host - hours",
        "domain": "datadog",
        "cost_source": "observability",
        "aggregate": "42c0ac62-8d80-11ed-96f3-da7ad0900005/agent_host_count"
      },
      {
        "id": "",
        "zone": "us",
        "account_name": "Kubecost",
        "charge_category": "usage",
        "description": "Centralize your monitoring of systems and services (Per Container)",
        "resource_name": "container_count_excl_agent",
        "resource_type": "infra_hosts",
        "provider_id": "42c0ac62-8d80-11ed-96f3-da7ad0900005/container_count_excl_agent",
        "billedCost": 0,
        "listCost": 19.80274,
        "list_unit_price": 0.0041095894,
        "usage_quantity": 14456,
        "usage_unit": "Container - hours",
        "domain": "datadog",
        "cost_source": "observability",
        "aggregate": "42c0ac62-8d80-11ed-96f3-da7ad0900005/container_count_excl_agent"
      }
...
    ]
  }
}
```

## Postman

A collection of OpenCost Postman queries: [opencost.postman_collection.json](https://raw.githubusercontent.com/opencost/opencost/develop/docs/opencost.postman_collection.json)

> **Note**: Change the hostname in the Collection>Edit>Variables
