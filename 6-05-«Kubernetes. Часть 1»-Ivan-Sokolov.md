# Домашнее задание к занятию «Kubernetes. Часть 1»

Это задание для самостоятельной отработки навыков и не предполагает обратной связи от преподавателя. Его выполнение не влияет на завершение модуля. Но мы рекомендуем его выполнить, чтобы закрепить полученные знания.

### Задание 1

**Выполните действия:**

1. Запустите Kubernetes локально, используя k3s или minikube на свой выбор.
   ```
   sudo snap install kubectl --classic
   curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
   sudo install minikube-linux-amd64 /usr/local/bin/minikube
   sudo apt install apt-transport-https ca-certificates curl software-properties-common
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   echo "deb [signed-by=/usr/share/keyrings/docker-archive-keyring.gpg]  \
   https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   sudo apt update
   sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   sudo usermod -aG docker ${USER}
   ```
   *Перезагружаемся*
   
1. Добейтесь стабильной работы всех системных контейнеров.
   ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/2649b748-27e8-4aed-8ee3-482bac1ab5d2)


### Задание 2

Есть файл с деплоем:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: redis
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: master
        image: bitnami/redis
        env:
         - name: REDIS_PASSWORD
           value: password123
        ports:
        - containerPort: 6379
```


**Выполните действия:**

1. Измените файл с учётом условий:

 * redis должен запускаться без пароля;  
 * создайте Service, который будет направлять трафик на этот Deployment;
 * версия образа redis должна быть зафиксирована на 6.0.13.

   *sudo nano redis-deployment.yaml*

   ```YAML
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: redis
   spec:
     selector:
       matchLabels:
         app: redis
     replicas: 1
     template:
       metadata:
         labels:
           app: redis
       spec:
         containers:
         - name: master
           image: bitnami/redis:6.0.13 # Версия образа зафиксирована
           env: # закомментили (удалили) пароль
            #- name: REDIS_PASSWORD
              #value: password123
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
           ports:
           - containerPort: 6379
   ```

  *sudo nano redis-service.yaml*

   ```YAML
   apiVersion: v1
   kind: Service
   metadata:
     name: redis-service
   spec:
     selector:
       app: redis
     ports:
       - protocol: TCP
         port: 6379
         targetPort: 6379
   ```


2. Запустите Deployment в своём кластере и добейтесь его стабильной работы.

   *kubectl apply -f redis-deployment.yaml*
   *kubectl apply -f redis-service.yaml*

   ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/b965eafd-ad3b-4b44-b5c9-f4df2e9645d3)
   


### Задание 3

**Выполните действия:**

1. Напишите команды kubectl для контейнера из предыдущего задания:

 - выполнения команды ps aux внутри контейнера;
   *kubectl exec -it redis-66cdfcbd-dmlk8 -- ps aux*
   
 - просмотра логов контейнера за последние 5 минут;
   *kubectl logs --since=5m redis-66cdfcbd-dmlk8*
 - удаления контейнера;
   *kubectl delete pod redis-66cdfcbd-dmlk8*
   
 - проброса порта локальной машины в контейнер для отладки.
   *kubectl port-forward redis-66cdfcbd-dmlk8 8080:80*

### Задание 4

Есть конфигурация nginx:

```
location / {
    add_header Content-Type text/plain;
    return 200 'Hello from k8s';
```

**Выполните действия:**

Напишите yaml-файлы для развёртки nginx, в которых будут присутствовать:

 - ConfigMap с конфигом nginx;
 - Deployment, который бы подключал этот configmap;
 - Ingress, который будет направлять запросы по префиксу /test на наш сервис.

