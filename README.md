# Distributed tracing training

1. Установка jaeger
```bash
kubectl create -f https://raw.githubusercontent.com/jaegertracing/jaeger-kubernetes/master/all-in-one/jaeger-all-in-one-template.yml
```

2. Развертывание приложений
```bash
kubectl apply -f manifests.yaml
```

3. Убеждаемся, что всё корректно разложилось:
```bash
kubectl get po --all-namespaces
```

4. Определяем IP сервиса api-gateway и делаем тестовые запросы:

Получение автомобилей:
```bash
minikube ssh -- curl -i $(kubectl get po -n api-gateway -o jsonpath='{.items[*].status.podIP}'):8090/cars
```

Покупка:
```bash
minikube ssh -- "curl -i -d '{\"name\": \"chevy s-10\"}' $(kubectl get po -n api-gateway -o jsonpath='{.items[*].status.podIP}'):8090/process"
```

5. Откроем интерфейс jaeger

```bash
minikube service jaeger-query
```

6. Получите информацию:
- Как происходит запрос /cars? Какие сервисы затрагивает и в какой последовательности?
- Как происходит запрос /process? Какие сервисы затрагивает? Есть ли параллельные вызовы?

7. Задеплойте вторую версию:
```bash
kubectl apply -f manifestsv2.yaml
```

8. Дождитесь Running:
```bash
kubectl get po --all-namespaces
``` 

9. Выполните запросы из пункта 4 (несколько раз до получения 500 ошибки).

10. С помощью jaeger UI получите информацию в чем root cause ошибки.
