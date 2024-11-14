# ACM-Observability
 # To troubleshoot metrics are collected by Thanos
To troubleshoot if the custom metrics are colloected by Thanos you have 2 methods:
- From the Grafana of ACM Obervability you can create a test panel and check in the metrics if the new custom metrics is created or not.
- You can run the following command in any of the Grafana pods in namespace open-cluster-management-observability
  curl 'http://rbac-query-proxy.open-cluster-management-observability.svc.cluster.local:8080/api/v1/query?query=<metric>' -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)"
  example:
  curl 'http://rbac-query-proxy.open-cluster-management-observability.svc.cluster.local:8080/api/v1/query?query=rox_central_info' -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)"
  You should be able to retrive the metric back
