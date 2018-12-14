Kubernetes prometheus
=========

This role installs the [prometheus operator](https://github.com/coreos/prometheus-operator) of the core os team.
It also installs a prometheus cluster to monitor the kubernetes cluster.
For this it installs the following:

* [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics)
* [node-exporter](https://github.com/prometheus/node_exporter)
* Some kubernetes services to expose the controller manager and the scheduler
* grafana instance with some default dashboard
* prometheus cluster
* alert manager


Requirements
------------

A working kubernetes cluster, starting from 1.6.* RBAC enabled.

Role Variables
--------------

* cluster_context: the cluster context you want to use
* cluster_namespace: the namespace to deploy in
* prometheus_memory: The memory you want to give the prometheus instances, standard it is 3Gi see [documentation](https://prometheus.io/docs/operating/storage/)
* prometheus_storage: The storage you want to give the prometheus instances
* prometheus_storage_class: The storage class to use for prometheus storage, please use at least an ssd as this is a time series database
* prometheus_retention: a retention period to keep the data specified in hours
* alertmanagerconfig: A yaml configuration that is stored in the alert managers

Adding new rules
------------
TO add new rules we can add new configmaps in the same namespace. These configmaps should have the labels:
```
role: prometheus-rulefiles
prometheus: k8s
```
The new alerts will be picked up automatically when having these labels.

Dependencies
------------
* Postgres: if you choose to use Grafana with database backend.


How to add a grafana dashboard
------------------------------

To add a grafana dashboard create your dashboard in grafana, export it and copy it in a json file. Add the following block on the bottom:
```
,
  "inputs": [
    {
      "name": "DS_PROMETHEUS",
      "pluginId": "prometheus",
      "type": "datasource",
      "value": "prometheus"
    }
  ],
  "overwrite": true
}
```

This is to set the input of the dashboard to prometheus.
Then add the dashboard json file to the grafana.yml in tasks.


Author Information
------------------
Stijn De Haes <<devops@rombit.be>>
