# ACM Observability
## Monitoring additional metrics by ACM observability
To monitor additional metrics by ACM observability you need to do the following
- Create a new configmap to allow additional metrics to be monitored, this config map must be called "observability-metrics-custom-allowlist"
   In the config map you need to allow the needed metrics the following link explains how the format of this of allow list:
https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/2.12/html-single/observability/index#adding-custom-metrics
    Remarks:
    - The configmap must be located in the namespace which has this application (for odf it has to be in openshift-storage)
	- In case you are monitoring metrics available in openshift by default (i.e. odf) the list has to be called metrics_list.yaml where thanos will collect these metrics from the prometheus in openshift-monitoring
	- In case you are monitoring user specific workloads (i.e. ACS), the list has to be called uwl_metrics_list.yaml where thanos will collect these metrics from the prometheus in openshift-user-workload-monitoring
	- Having new allowlist will trigger the restart of metrics-collector-deployment pod or uwl-metrics-collector-deployment pod in open-cluster-management-observability
	In case you want to add metrics available in the spoke clusters, the configmap of the allowlist as to be deployed in open-cluster-management-observability
   So to monitor the odf in the hub cluster you need allowlist configmap in openshift-storage, and to monitor the odf in the spoke cluster you need allowlist configmap in open-cluster-management-observability
- Create a configmap whjch contains the Grafana dashboards in json format in open-cluster-management-observability.


##  Troubleshooting
- To check if the thanos is creating collect the new metrics you can run the following command in any of the grafana pods in open-cluster-management-observability
  curl 'http://rbac-query-proxy.open-cluster-management-observability.svc.cluster.local:8080/api/v1/query?query=new_metric' -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)"
   Example:
   curl 'http://rbac-query-proxy.open-cluster-management-observability.svc.cluster.local:8080/api/v1/query?query=odf_system_latency_seconds' -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)"
