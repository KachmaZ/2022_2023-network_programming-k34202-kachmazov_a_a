University: ITMO University

Faculty: FICT

Course: Network programming

Year: 2022

Group: K34202

Author: Kachmazov Arthur Andreevich

Lab: Lab3

Date of create: 06.12.2022

Date of finished: 27.12.2022

# Тема работы: 
Развертывание Netbox, сеть связи как источник правды в системе технического учета Netbox

# Цель работы: 
С помощью Ansible и Netbox собрать всю возможную информацию об устройствах и сохранить их в отдельном файле.

# Ход работы:
## 1) Поднять Netbox на дополнительной VM.  
  На облачной виртуальной машине был развёрнут Netbox - веб-приложение с открытым кодом, предназначенное для управления компьютерными сетями и их документирования.
  Согласно инструкции (https://www.vultr.com/docs/install-and-configure-netbox-on-ubuntu-20-04/), для настройки Netbox требуется:
  - PostgreSQL
  - Redis
  - Netbox
  - Gunicorn
  - Systemd
  - Nginx Web Server
  
  ### PostgreSQL
  Требуется установить и настроить базу данных (Рис. 1).
  sudo apt install postgresql  
  ![image](https://user-images.githubusercontent.com/59313334/209576342-1f1966dd-a940-4fc5-81a8-d76ded4e7a46.png)  
  Риснуок 1 - Установка БД  

  Создана нужная база данных и привелегированный пользователь (Рис. 2).    
  ![image](https://user-images.githubusercontent.com/59313334/209576442-cd7eda44-7e27-47a7-9d59-6e54221786e7.png)  
  Рисунок 2 - Создание пользователя  
  
  ### Redis и Netbox
  Установлен и настроен Redis - хранилище значений ключей в памяти (Рис. 3).  
  ![image](https://user-images.githubusercontent.com/59313334/209576761-7747a419-9c1d-4ce7-b380-c92aca08016a.png)  
  Риснуок 3 - Проверка работы Redis 

  Далее был установлен и запущен Netbox (Рис. 4).
  ![image](https://user-images.githubusercontent.com/59313334/209576806-9afce3c6-bf80-4ced-97fa-5050a88cfa22.png)  
  Рисунок 4 - Запуск Netbox  
      
  ### Gunicorn
  Настроен Gunicorn - application-сервер, предназначенный для демонизации web-приложений, написанных на Python.  
  Для настройка gunicorn скопирован файл  
  `sudo cp /opt/netbox/contrib/gunicorn.py /opt/netbox/gunicorn.py`  
  
  ### Systemd
  
  Скопированы файлы в каталог
  `sudo cp /opt/netbox/contrib/*.service /etc/systemd/system/`

  Перезагружен демон, чтобы включить изменения Systemd
  `sudo systemctl daemon-reload`

  Запущены службы netbox и .netbox-rq
  `sudo systemctl start netbox netbox-rq`

  Включен запуск служб во время загрузки.
  `sudo systemctl enable netbox netbox-rq`
  
  ### Nginx Web Server
  
  Был установлен и сконфигурирован веб-сервер Nginx (Рис. 5).  
  ![207989501-0314fdd9-dd8c-4079-80e8-cbd693186df8](https://user-images.githubusercontent.com/59313334/209625405-26fe4207-4a5b-4ce4-bb49-ca6c300daf81.png)  
  Рисунок 5 - Конфигурация веб-сервера

  По итогу был получен рабочий сетевой сервис Netbox (Рис. 6).
  ![image](https://user-images.githubusercontent.com/59313334/209577969-895e9b0a-2bf3-4acc-87cc-14a789878c28.png)  
  Рисунок 6 - Интерфейс Netbox
  
## 2) Заполнить всю возможную информацию о ваших CHR в Netbox.
  Через интерфейс Netbox была внесена вся доступная информация (Рис. 7).  
  ![image](https://user-images.githubusercontent.com/59313334/209578036-ebd36f2a-d829-4339-8567-c890ed285ca1.png)  
  Риснуок 7 - Информация о роутерах в Netbox  
    
## 3) Используя Ansible и роли для Netbox в тестовом режиме сохранить все данные из Netbox в отдельный файл.
  Из Netbox был скачен csv-файл с данными устройств (Рис. 8).  
  ![image](https://user-images.githubusercontent.com/59313334/209578181-f2f60b00-6e8d-463d-a795-7d1c8b341787.png)  
  Рисунок 8 - Скаченный файл в системе   
  
## 4) Написать сценарий, при котором на основе данных из Netbox можно настроить 2 CHR, изменить имя устройства, добавить IP адрес на устройство.
  Файл csv перенесен на сервер с anible.  
  Создан файл playbook1.yml. Основная цель разделена на две подзадачи. Первая - спарсить csv файл и записать нужную информацию о роутере в отдельный файл. В данном случае за ключ взят UID роутера, так как это значение не будет изменяться (Рис. 9).    
  ![207989501-0314fdd9-dd8c-4079-80e8-cbd693186df8](https://user-images.githubusercontent.com/59313334/209579028-e5d2b546-6d08-4d1e-a381-c595dec988ff.png)  
  Рисунок 9 - Файл playbook1.yml
  
  Запущен playbook1, для проверки правильности вывода (Рис. 10).  
  ![207990316-08c16dab-5ea5-4694-b915-31ae97149747](https://user-images.githubusercontent.com/59313334/209579108-38706c96-3460-4b54-85d3-1f830895d070.jpg)  
  Рисунок 10 - Проверка сценария
  
  В playbook1.yml добавлена часть, которая отвечает за установку названия роутера (Рис. 11).  
  ![image](https://user-images.githubusercontent.com/59313334/209579129-1a82797b-8f97-4e97-a1a1-9ff12edb8ebe.png)  
  Рисунок 11 - Доработка сценария
  
  После доработки проедена проверка сценария (Рис. 12)  
  
  ![image](https://user-images.githubusercontent.com/59313334/209579176-94c1b722-0197-4500-9d56-62f417bb8c95.png)  
  Рисунок 12 - Проверка сценария
  
## 5) Написать сценарий, позволяющий собрать серийный номер устройства и вносящий серийный номер в Netbox.
  Был создан сценарий для получения серийного номера сетевого устройства и создания устройства в NetBox (Рис. 13).  
  
  ![image](https://user-images.githubusercontent.com/59313334/209579556-602a3591-297a-4e24-a14a-f9d2fffc41b8.png)  
  Рисунок 13 - Файл playbook2.yml
  
  Сценарий был запущен (Рис. 14, 15).  
  ![image](https://user-images.githubusercontent.com/59313334/209579566-75d97d1a-8217-4ae5-a6ad-747613d4b4f1.png)  
  Рисунок 14 - Вывод в консоли сервера
  
  ![image](https://user-images.githubusercontent.com/59313334/209579582-cb64fbb9-693f-43c1-9d29-b0b6c5cace48.png)  
  Рисунок 15 - Результат в Netbox
  
  # Вывод
  Таким образом, в процессе выполнения лабораторной работы ознакомились с инструментом NetBox, были созданы сценарии Ansible для работы с NetBox. Также была выполнена проверка локальной связности между роутерами и NetBox.
  Составлена диаграмма сети (Рис. 16)

![image](https://user-images.githubusercontent.com/59313334/209583589-b3a34861-1a22-47ff-8012-88f58e583d38.png)  
Рисунок 16 - Диаграмма сети

