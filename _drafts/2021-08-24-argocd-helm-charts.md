```yaml
project: project-name
source:
  repoURL: source-repo
  path: app-path
  targetRevision: dev
  helm:
    valueFiles:
      - values.yaml
      - ../values2.yaml
destination:
  server: 'https://kubernetes.default.svc'
  namespace: namespace
ignoreDifferences:
- group: apps.openshift.io
  kind: DeploymentConfig
  name: app-name
  namespace: namespace
  jsonPointers:
    - /spec/template/spec/containers/0/image
```


resources:
<https://blog.argoproj.io/getting-started-with-applicationsets-9c961611bcf0>
<https://rtfm.co.ua/en/argocd-a-helm-chart-deployment-and-working-with-helm-secrets-via-aws-kms/>

<https://cloud.redhat.com/blog/continuous-delivery-with-helm-and-argo-cd>

<https://utkuozdemir.org/blog/argocd-helm-secrets/>


```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: filebeat
spec:
  destination:
    namespace: default
    server: https://kubernetes.default.svc
  project: default
  source:
    helm:
      parameters:
      - name: daemonset.filebeatConfig.filebeat\.yml
        value: |
          filebeat.inputs:
          - type: container
            paths:
              - /var/log/containers/*.log
            processors:
            - add_kubernetes_metadata:
                host: ${NODE_NAME}
                matchers:
                - logs_path:
                    logs_path: "/var/log/containers/"

          output.elasticsearch:
            host: '${NODE_NAME}'
            hosts: '${ELASTICSEARCH_HOSTS:elasticsearch-master:9200}'

    repoURL: https://helm.elastic.co
    targetRevision: 7.14.0
    chart: filebeat
```

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: filebeat
spec:
  destination:
    namespace: default
    server: https://kubernetes.default.svc
  project: default
  source:
    helm:
      parameters:
      - name: values.yml
        value: |
          filebeat.inputs:
          - type: container
            paths:
              - /var/log/containers/*.log
            processors:
            - add_kubernetes_metadata:
                host: ${NODE_NAME}
                matchers:
                - logs_path:
                    logs_path: "/var/log/containers/"

          output.elasticsearch:
            host: '${NODE_NAME}'
            hosts: '${ELASTICSEARCH_HOSTS:elasticsearch-master:9200}'

    repoURL: https://prometheus-community.github.io/helm-charts
    targetRevision: 18.0.3
    chart: filebeat
```