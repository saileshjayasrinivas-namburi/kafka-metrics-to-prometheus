
# Kafka Metrics to Prometheus

## Kafka Metrics to Prometheus facilitates monitoring of Apache Kafka clusters via Prometheus, enabling collection and visualization of various metrics for performance analysis and troubleshooting.

 

## Configuration Steps

1. BootstrapServerPath of AWS managed Kafka
2. Get BootstrapServerPath 
3. In the PrometheusSecret.json file, add connection strings in the target sections for both JMX and node job. (find PrometheusSecret.json File in Repo )
4. For JMX, port should be 11001, and for node, it should be 11002


## Configure Prometheus.yaml

1. [Download prometheus.yaml (values.yaml )](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/values.yaml) 

2. Check the extraSecretMounts section in Prometheus.yaml. If it is commented, uncomment it.

```
extraSecretMounts:
    - name: targets-file
      mountPath: /etc/secrets
      #subPath: ""
      secretName: targets
      readOnly: true
```

3. Add Below Job in scrape_configs Section

```
- job_name: 'Kafkabroker'
  scrape_interval: 15s
  file_sd_configs:
    - files:
      - '/etc/secrets/PrometheusSecret.json'
```
 
4. Change retention value to "30d" # change days as per your Requirement 

5. Update the value of size: 30Gi under the persistentvolume section. # change size as per your Requirement

6. Create Secret (Create a secret with the PrometheusSecret.json file)

```
kubectl create secret generic targets --from-file=".\PrometheusSecret.json" -n <namespace> 
```

## Apply Prometheus Configuration

```
helm repo add Prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install  --values Prometheus.yml prometheus prometheus-community/prometheus --namespace <namespace>

```

