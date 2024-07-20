## Как "задеплоить код”. На примере образа NGINX 

Получите доступ к кластеру. В качестве примера будет рассмотрен пример кластера в Яндекс облака
Необходимо получить свой namespace. К нему будет выдан полный доступ: можно создавать конфиги и секреты, запускать поды — делать всё, что потребуется для настройки и запуска веб-сервиса
Вам будет выделен домен и создан роутер. Он распределит входящие сетевые запросы на разные NodePort кластера K8s.
Пример
1. Создание тома постоянного хранилища данных для postgres размером 3Gb
```sh
#Имя файла - yc-sirius/edu-reverent-mestorf/Nginx test/Deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-nginx
  namespace: edu-reverent-mestorf
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-deployment
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
      - image: nginx
        name: nginx-l2-devman
        ports:
        - containerPort: 80
          protocol: TCP
      restartPolicy: Always
```
```sh
#Имя файла - yc-sirius/edu-reverent-mestorf/Nginx test/Service.yaml
apiVersion: v1
kind: Service
metadata:
  name: deployment-nginx
  namespace: edu-reverent-mestorf
spec:
  selector:
    app: nginx-deployment
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30291
  type: NodePort
```

