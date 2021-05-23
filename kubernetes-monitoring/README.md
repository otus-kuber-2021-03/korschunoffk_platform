Мониторинг приложения в кластере

1. Был собран контейнер с nginx на базе alpine отдающий метрики по адресу /basic_status
, korschunoffk/kuberhomework1:v3
2. Создан namespace monitoring: kubectl apply -f monitoring-ns.yaml
3. Установлен prometheus при помощи helm3
 - helm repo add stable https://charts.helm.sh/stable
- Helm repo update
- Helm install monitoring-hw stable/prometheus -n monitoring
Проверяем - kubectl get pods -n monitoring
Пробросим порт на localhost для теста 
Kubectl --namespace monitoring port-forward PODNAME(prometheus-server) 9090 

Prometheus должен быть доступен по http://127.0.0.1:9090/

4. Создаем deployment web-deployment.yaml
5. Создаем service.   web-service.yaml
6. создаем servicemonitor. servicemonitor.yaml
Применяем все вышеописанные манифесты, при применении servicemonitor вылезет ошибка, отсутствие kind ServiceMonitor в текущей версии API monitoring.coreos.com/va - применяем манифест  
kubectl apply -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/release-0.43/example/prometheus-operator-crd/monitoring.coreos.com_servicemonitors.yaml

7. Для теста выбросим наружу какой либо под из deployment:
 kubectl port-forward  web-79b776cb84-hpxr8 9113 
И проверим что отдает : curl http://127.0.0.1:9113/metrics

——————————
```
curl http://127.0.0.1:9113/metrics
<!DOCTYPE html>
                        <title>NGINX Exporter</title>
                        <h1>NGINX Exporter</h1>
                        <p><a href="/metrics">Metrics</a></p># HELP nginx_connec                          tions_accepted Accepted client connections
# TYPE nginx_connections_accepted counter
nginx_connections_accepted 5
# HELP nginx_connections_active Active client connections
# TYPE nginx_connections_active gauge
nginx_connections_active 1
# HELP nginx_connections_handled Handled client connections
# TYPE nginx_connections_handled counter
nginx_connections_handled 5
# HELP nginx_connections_reading Connections where NGINX is reading the request                           header
# TYPE nginx_connections_reading gauge
nginx_connections_reading 0
# HELP nginx_connections_waiting Idle client connections
# TYPE nginx_connections_waiting gauge
nginx_connections_waiting 0
# HELP nginx_connections_writing Connections where NGINX is writing the response                           back to the client
# TYPE nginx_connections_writing gauge
nginx_connections_writing 1
# HELP nginx_http_requests_total Total http requests
# TYPE nginx_http_requests_total counter
nginx_http_requests_total 6
# HELP nginx_up Status of the last metric scrape
# TYPE nginx_up gauge
nginx_up 1
# HELP nginxexporter_build_info Exporter build information
# TYPE nginxexporter_build_info gauge
nginxexporter_build_info{commit="5f88afbd906baae02edfbab4f5715e06d88538a0",date=                          "2021-03-22T20:16:09Z",version="0.9.0"} 1

——————————
```
