# architecture-insuretech
## task 1

[Новая технологическая архитектура](Task1/InsureTech_технологическая_архитектура_to-be.drawio)

![Новая технологическая архитектура](Task1/InsureTech_технологическая_архитектура_to-be.jpg)

## task 2
### 2.1.1 Поднять локальный кластер Kubernetes в Minikube
```shell
minikube start
```
### 2.1.2 Активировать metrics-server
```shell
minikube addons enable metrics-server
```
### 2.1.3 Написать манифест развёртывания (Deployment) и примените в кластере. Лимит памяти установить равный “30Mi”
```shell
kubectl apply -f ./task2/scaletestapp-deployment.yaml
```
### 2.1.4 Написать и применить манифест сервиса (Service) для доступа к приложению
```shell
kubectl apply -f ./task2/scaletestapp-service.yaml
```
### 2.1.5 Настроить динамическую маршрутизацию на основании показателей утилизации оперативной памяти с помощью Horizontal Pod Autoscaler
```shell
kubectl apply -f ./task2/scaletestapp-hpa.yaml
```
### 2.1.6 Открыть доступ к приложению из внешнего мира

```shell
minikube service scaletestapp-service --url
```
### 2.1.7 Запустить Locust
```shell
locust -f ./task2/locustfile.py
```
### 2.1.8 Настроить параметры нагрузочного тестирования в Web UI и запустить.
Видно, что кол-вод подов увеличилось с 1 до 3 постепенно с ростом утилизированной памяти.

[Дашборд миникуба](Task2/scaling_dashboard.jpg)
![Дашборд миникуба](Task2/scaling_dashboard.jpg)

[События миникуба](Task2/scaling_log.jpg)
![События миникуба](Task2/scaling_log.jpg)

[Лог HPA](Task2/scaling_describe_hpa.jpg)
![Лог HPA](Task2/scaling_describe_hpa.jpg)

## task2. Дополнительная часть
### 2.2.1 Установить Prometheus в кластере
```shell
helm repo add prometheus-community <https://prometheus-community.github.io/helm-charts>
helm repo update
helm install prometheus-operator prometheus-community/kube-prometheus-stack -f ./Task2/additional/custom_values.yaml
```
### 2.2.2 Применить манифест ServiceMonitor для экспорта метрик из приложения в Prometheus
```shell
kubectl apply -f ./task2/additional/scaletestapp-servicemonitor.yaml
```
### 2.2.3 Открыть доступ к Prometheus из внешнего мира
```shell
minikube service prometheus-operator-kube-p-prometheus --url
```
### 2.2.4 Проверить получение метрик scaletestapp через ServiceMonitor. В таргетах д.б. соответствующий элемент
[Scaletestapp Target в Prometheus](Task2/additional/scaletestapp-app-sm-target.jpg)
![Scaletestapp Target в Prometheus](Task2/additional/scaletestapp-app-sm-target.jpg)
### 2.2.5 Настроить Prometheus Adapter для использования метрик Prometheus в Horizontal Pod Autoscaler
```shell
helm install prometheus-adapter prometheus-community/prometheus-adapter -f ./Task2/additional/custom_metrics.yaml
```
### 2.2.6 Проверить что кастомная метрика добавилась
```shell
get --raw /apis/custom.metrics.k8s.io/v1beta1
```
![Custom metrics в v1beta1](Task2/additional/custom_metrics.jpg)

### 2.2.7 Обновить манифест Horizontal Pod Autoscaler с использованием новой метрики

[Новая версия манифеста HPA](Task2/additional/scaletestapp-hpa-new.yaml)
Применим её
```shell
kubectl apply -f ./task2/additional/scaletestapp-hpa-new.yaml
```
### 2.2.8 Запустить Locust
```shell
locust -f ./task2/locustfile.py
```
### 2.2.9 Настроить параметры нагрузочного тестирования в Web UI и запустить.
Сначала дал rps 100+, затем 200+. Каждый раз поды отскейлились х2

[Дашборд миникуба](Task2/additional/scaling_dashboard.jpg)
![Дашборд миникуба](Task2/additional/scaling_dashboard.jpg)

[События миникуба](Task2/additional/scaling_log.jpg)
![События миникуба](Task2/additional/scaling_log.jpg)

[Лог HPA](Task2/additional/scaling_describe_hpa.jpg)
![Лог HPA](Task2/additional/scaling_describe_hpa.jpg)
## Task3 Переход на Event-Driven архитектуру
