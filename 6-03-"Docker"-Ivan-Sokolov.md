# Домашнее задание к занятию «Docker. Часть 1»

Это задание для самостоятельной отработки навыков и не предполагает обратной связи от преподавателя. Его выполнение не влияет на завершение модуля. Но мы рекомендуем его выполнить, чтобы закрепить полученные знания.

### Задание 1  

1. Установите [Docker](https://www.docker.com/).

   ```
   #Добавляем gpg-ключ репозитория
   sudo apt-get update
   sudo apt install apt-transport-https ca-certificates curl software-properties-common
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

   #Устанавливаем репозиторий в систему
   echo "deb [signed-by=/usr/share/keyrings/docker-archive-keyring.gpg]  \
   https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
   #обновляем индекс пакетов и устанавливаем docker и плагины
   sudo apt update
   sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

   #чтобы не печатать каждый раз sudo, требует дальнейшей перезагрузки
   sudo usermod -aG docker ${USER}
   ```

2. Запустите образ hello-world.

    ```
    #запуск не скачанного образа автоматический запускает pull с docker hub
    docker run hello-world

    #обновить скачанный образ (или скачать новый)
    docker pull hello-world
    ```

3. Удалите образ hello-world.
  
    ```
    #просмотр списка контейнеров и удаление, можно использовать неполные ID
    docker container ls -a
    docker container rm <ID>

    #просмотр списка образов и удаление, можно использовать неполные ID
    docker image ls
    docker image rm <ID>

### Задание 2

1. Найдите в Docker Hub образ Apache и запустите его на 80 порту вашей ВМ.

   ```
   docker pull httpd
   docker run -d -p 80:80 --name apache2 httpd:latest
   ```
   
2. Откройте страницу http://localhost и убедитесь, что видите приветвенную страницу Apache.
   ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/3b56ad43-974b-4437-967c-ca2caef276a7)

   

### Задание 3

1. Создайте свой Docker образ с Apache и подмените стандартную страницу index.html на страницу, содержащую ваши ФИО.

   *Для удобства создал /dock/projects/ для нового проекта создал там папку apache_new_index с файлами Dockerfile и new_index.html*

    ```html
    #sudo nano /dock/projects/apache_new_index/new_index.html
    <h1>Sokolov Ivan Dmitrievich</h1>
    ```

    ```
    #sudo nano /dock/projects/apache_new_index/Dockerfile
    FROM httpd:latest
    MAINTAINER Ivan Sokolov
    COPY new_index.html /usr/local/apache2/htdocs/index.html
    ```

   ```
   #Собираем образ, предварительно перейдя в папку, где лежит Dockerfile
   docker build -t apache_new_index .
   ```
   
2. Запустите ваш образ, откройте страницу http://localhost и убедитесь, что страница изменилась.

   ```
   docker run -d -p 80:80 apache_new_index
   ```
   ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/9970cb80-a579-415f-a556-d7ca34a65f72)

   ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/4e4ec0c6-1ade-4bdc-85c0-ed6447e75af5)


