# RHACM Central Observability
## Introduction
RHACM Central Observability is a feature available in RHACM where it provides a grafana dashboard to monitor centrally all managed cluster.
The central observability provides out of the box ready to use grafana dashboards, however it is possible to add custom metrics and create additional dashboards
## Monitoring additional metrics by ACM observability
To monitor additional metrics you need to do the following:
- Create a new configmap to allow additional metrics to be monitored, this config map must be called "observability-metrics-custom-allowlist"
   In the config map you need to allow the needed metrics the following link explains how the format of this of allow list:
https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/2.12/html-single/observability/index#adding-custom-metrics
    Remarks:
       - The configmap must be located in the namespace which has this application (for odf it has to be in openshift-storage)
        - In case you are monitoring metrics available in openshift by default (i.e. odf) the list has to be called metrics_list.yaml where Thanos will collect these metrics from the Prometheus in the namespace "openshift-monitoring"
        - In case you are monitoring user specific workloads (i.e. ACS), the list has to be called uwl_metrics_list.yaml where Thanos will collect these metrics from the prometheus in the namespace "openshift-user-workload-monitoring"
        - Having new allowlist will trigger the restart of metrics-collector-deployment pod and/or uwl-metrics-collector-deployment pod in the namespace "open-cluster-management-observability"
		- It is not allowed to used wildcard to add metrics in the allowlist because it can onboard too much data that cannot be adequately digested and stored at the central hub location please the link below for more information.
		  https://access.redhat.com/solutions/7049583
        - In case you want to add metrics available in the managed clusters, the configmap of the allowlist has to be deployed in the namespace "open-cluster-management-observability", so to monitor the odf in the hub cluster only you need allowlist configmap to be created in the namespace "openshift-storage", and to monitor the odf in all the managed cluster (hub cluster and spoke clusters) you need allowlist configmap in open-cluster-management-observability
		- In this Kustomization charts here the allowlist configmap for both the metrics_list and uwl_metrics_list are placed in the namespace "open-cluster-management-observability" manage this list from one configmap
- Create a configmap whjch contains the Grafana dashboards in json format in open-cluster-management-observability.


##  Troubleshooting
- Adding new metrics will trigger a restart of metrics-collector-deployment pod and/or uwl-metrics-collector-deployment pod in the namespace "open-cluster-management-observability" to checks these pods on the hub cluster to make sure that new metrics are onboarded successfully
  `#oc get pods -n open-cluster-management-observability`
- To check if the thanos is creating collect the new metrics you can run the following command in any of the grafana pods in open-cluster-management-observability
`  curl 'http://rbac-query-proxy.open-cluster-management-observability.svc.cluster.local:8080/api/v1/query?query=<new_metric>' -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)"`

   Example:
   `curl 'http://rbac-query-proxy.open-cluster-management-observability.svc.cluster.local:8080/api/v1/query?query=odf_system_latency_seconds' -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)"`
