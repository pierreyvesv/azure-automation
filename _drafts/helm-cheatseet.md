
helm repo add chart_name repo_url
helm show values chart_name
helm show values chart_name > values.yaml

helm install release_name charts -f values.yaml
helm history
