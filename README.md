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
## Как подготовить dev окружение
Для взаимодействия с БД необходимо получить и использовать файл сертификата.
Подключение к базе данных в кластере PostgreSQL. 
PostgreSQL-хосты с публичным доступом поддерживают только шифрованные соединения. Чтобы использовать их, получите SSL-сертификат:
```sh
mkdir -p ~/.postgresql && \
wget "https://storage.yandexcloud.net/cloud-certs/CA.pem" \
     --output-document ~/.postgresql/root.crt && \
chmod 0600 ~/.postgresql/root.crt
```
Сертификат будет сохранен в файле ~/.postgresql/root.crt.
Используем полученный файл `root.crt` при создании раздела secret
#Имя файла - yc-sirius/edu-reverent-mestorf/Nginx test with secret/secret.yaml
![img.png](img.png)

Для удобства можно использовать [сервис](https://base64.guru/converter/encode/file)

Сам файл для теста
#Имя файла - yc-sirius/edu-reverent-mestorf/Nginx test with secret/test_pod.yaml
