Запустить проект:

minikube start --memory 4096 --cpus 4

cd $KUBERNETES_MONITORING_DIR

kubectl create ns prometheus-operator

helm upgrade --install prometheus-operator stable/prometheus-operator --wait -namespace=prometheus-operator -f values.yaml

kubectl port-forward -n prometheus-operator service/prometheus-operator-grafana 8080:80

kubectl appyl -f { <deployment, service, service-monitor> }

OPTIONAL:

kubectl port-forward -n monitoring service/nginx-monitoring 9113:9113

kubectl port-forward -n monitoring service/nginx-monitoring 8888:8888



Проверить работоспособность:

Grafana - http://localhost:8080

Check - http://localhost:8888/basic_status

Check - http://localhost:9113/metrics
