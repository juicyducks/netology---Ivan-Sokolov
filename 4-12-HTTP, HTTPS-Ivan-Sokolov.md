# Домашнее задание к занятию "HTTP/HTTPS"

Эти задания обязательные к выполнению. Пожалуйста, присылайте на проверку всю задачу сразу. Любые вопросы по решению задач задавайте в чате учебной группы. 

### Цели задания

- Научиться создавать и настраивать локальный веб-сервер
- Обработка запросов по HTTP/HTTPS

Данная практика закрепляет знания о создании веб-серверов и изменении его конфигурации. Эти навыки пригодятся для понимания принципов создания сайтов и взаимодействия клиента с веб-сервером через протоколы HTTP/HTTPS.

### Инструкция к заданию

1. Сделайте копию [Шаблона для домашнего задания](https://docs.google.com/document/d/1youKpKm_JrC0UzDyUslIZW2E2bIv5OVlm_TQDvH5Pvs/edit) себе на Google Диск.
2. В названии файла введите корректное название лекции и вашу фамилию и имя.
3. Зайдите в “Настройки доступа” и выберите доступ “Просматривать могут все в Интернете, у кого есть ссылка”. Ссылка на инструкцию [Как предоставить доступ к файлам и папкам на Google Диске](https://support.google.com/docs/answer/2494822?hl=ru&co=GENIE.Platform%3DDesktop)
4. Скопируйте текст задания в свой  Google Документ.
5. Выполните домашнее задание, запишите ответы и приложите необходимые скриншоты и код в свой Google Документ.
6. Для проверки домашнего задания преподавателем отправьте ссылку на ваш Google документ в личном кабинете.
7. Любые вопросы по решению задач задавайте в чате учебной группы.
------

### Задание 1.

#### Описание задания
Перед вами стоит задача создать и настроить Nginx веб-сервер.

#### Требование к результату
- Вы должны отправить скриншот с выполненным заданием.
- К выполненной задаче добавьте скриншот выполненной переадресации.
- В ответе пришлите скриншоты работающей страницы https://localhost и страницы с 301 ответом.

#### Процесс выполнения

1. Запустите программу VirtualBox
2. В программе VirtualBox загрузите операционную систему Ubuntu, если она у вас не установлена в качестве основной системы.
3. Установите Nginx:

```
sudo apt-get install nginx
```
4. Сгенерируйте сертификат для него указав localhost в качестве CN  

```
sudo openssl req -x509 -nodes -newkey rsa:4096 -keyout /etc/nginx/cert.key -out /etc/nginx/cert.pem -days 365
```


5. Замените блок http {  } в файле /etc/nginx/nginx.conf на:

```
http {
    gzip on;
    server {
        listen 80 default_server;
        root   /var/www/public;
        listen  443 ssl http2 default_server;
        server_name  localhost;
        ssl_certificate  /etc/nginx/cert.pem;
        ssl_certificate_key /etc/nginx/cert.key;
        ssl_protocols   TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers   HIGH:!aNULL:!MD5;
        location / {
            index index.html;
        }
    }
}
```

6. Создайте файл /var/www/public/index.html c содержимым.
```
<h1>It works</h1>
```
7. Зайдите на страницу в браузере, пропустив сообщение о неработающем сертификате.
8. Пришлите скриншот работающей страницы https://localhost.

   *Предварительно необходимо перезапустить nginx*

    ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/91a0533d-90a1-4bda-ae27-fa7c7bc65dbb)

   *Через ранее настроенный dns-сервер bind9 так же работает с другой виртуальной машины в локальной сети*

   ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/911471c7-870e-4a28-8547-f2e35d3d82d2)

10. Измените конфигурацию сервера добавив переадресацию c Вашего сервера на сайт netology.ru.
```
location / {
  return 301 https://netology.ru;
}
```
10. Используя curl, сделайте запрос к своему серверу.

    *Переадресация работает, но по https ругается на сертификат, по http возвращается 301, хотя отдельно http не прописывали*

   ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/bda9ca57-8da2-4910-8927-b24fe814fb15)



### Задание 2.

#### Описание задания
Перед вами стоит задача создать и настроить Apache2 веб-сервер.

#### Требование к результату
- Вы должны отправить скриншоты с выполненным заданием
- К выполненной задаче добавьте результат получившейся конфигурации.

#### Процесс выполнения
1. Запустите программу VirtualBox
2. В программе VirtualBox загрузите вторую виртуальную машину с  операционной системой Ubuntu. 
3. Используя документацию [https://httpd.apache.org/docs/current/](), установите apache2 веб-сервер. 

    ```
    sudo apt install apache2
    sudo systemctl start apache2
    ```

    ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/8ba2697a-fe5c-4682-8062-0f8369234a56)
    
4. Выполните аналогичные действия как и задании 1, добившись аналогичной работы сервера.

    *создаём сертификат* `sudo openssl rea -x509 -nodes -newkey rsa:4096 - keyout /etc/apache2/apa_cert.key - out /etc/apache2.apa_cert.pem - days 365`
    *заменяем содержимое /var/www/html/index.html текстом `<h1>IT'S ALIVE</h1>`*
    *создаём конфиг файл /etc/apache2/sites-available/localhost-ssl.conf*

    ```
    <VirtualHost *:443>
        ServerAdmin webmaster@localhost
        ServerName localhost

        DocumentRoot /var/www/html

        SSLEngine on
        SSLCertificateFile /etc/apache2/apa_cert.pem
        SSLCertificateKeyFile /etc/apache2/apa_cert.key

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    ```

    *Активируем виртуальный хост, модуль ssl и перезапускаем сервис*

    ```
    sudo a2ensite localhost-ssl
    sudo a2enmode ssl
    sudo systemctl restart apache2
    ```

    ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/7a6547c5-f487-4b9e-a6c1-38a5a5297361)

    *Для перенаправления на netology.ru правим /etc/apache2/sites-available/localhost-ssl.conf*

   ```
   <VirtualHost *:80>
        ServerName localhost
        Redirect permanent / https://netology.ru/
    </VirtualHost>

    <VirtualHost *:443>
        ServerName localhost
        Redirect permanent / https://netology.ru/

        SSLEngine on
        SSLCertificateFile /etc/apache2/apa_cert.pem
        SSLCertificateKeyFile /etc/apache2/apa_cert.key   
    </VirtualHost>
   ```

    *перезапускаем Apache и отправляем запросы с помощью curl*

    ![image](https://github.com/juicyducks/netology---Ivan-Sokolov/assets/142479225/93fb8404-fe2a-4eb1-8f56-33866bf81d7a)
   


    
### Правила приема работы
1. В личном кабинете отправлена ссылка на ваш Google документ, в котором прописан код каждого скрипта и скриншоты, демонстрирующие корректную работу скрипта
2. В документе настроены права доступа “Просматривать могут все в Интернете, у кого есть ссылка”
3. Название документа содержит название лекции и ваши фамилию и имя

### Общие критерии оценки
Задание считается выполненным при соблюдении следующих условий:
1. Выполнено оба задания
2. К заданию прикреплены скриншоты конфигураций и выполненных запросов по итогам каждого задания.


