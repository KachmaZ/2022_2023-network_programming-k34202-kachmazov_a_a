
University: ITMO University

Faculty: FICT

Course: Network programming

Year: 2022

Group: K34202

Author: Kachmazov Arthur Andreevich

Lab: Lab4

Date of create: 24.12.2022

# Тема работы: Базовая 'коммутация' и туннелирование с использованием языка программирования P4

# Цель работы: Изучить синтаксис языка программирования P4 и выполнить 2 задания обучающих задания от Open network foundation для ознакомления на практике с P4.

# Ход работы:

## Подготовка к работе
Перед началом работы были установлены Vagrant и VirtualBox.  
Далее был клонирован репозиторий `https://github.com/p4lang/tutorials`. В папке vm-ubuntu-20.04 была исполнена команда `vagrant up`, запустившая установку виртуальной машины (Рис. 1).  

![image](https://user-images.githubusercontent.com/59313334/209447207-d2352ff7-4bd9-40b1-a7e2-5711164c46b1.png)
Рисунок 1 - Запуск виртуальной машины  

При помощи провизии в виртуальной машине установлен новый графический интерфейс. В итоге была получена рабочая среда (Рис. 2).

<img width="960" alt="image_2022-12-18_22-53-42" src="https://user-images.githubusercontent.com/59313334/209446479-010dda02-e187-4675-9da5-bbd2ee63c678.png">
Рисунок 2 - Рабочая среда  

Работа ведётся в рамках сети следующей топологии (Рис. 3).  

![image](https://user-images.githubusercontent.com/59313334/209447190-00e9c95b-1933-4176-af29-40d92608153a.png)
Рисунок 3 - Схема сети  

## Implementing Basic Forwarding
Произведён запуск эмулятора компьютерной сети Mininet при стартовой конфигурации (Рис. 4).  

![image](https://user-images.githubusercontent.com/59313334/209447331-db05aa40-51d9-407d-b13f-2a153aa8e94e.png)  
Рисунок 4 - Запуск Mininet при начальных данных
  
  При стартовой конфигурации на каждом коммутаторе происходит сборос входящих пакетов, поэтому все взаимные пинги провалены.  

В файле basic.p4 внесены следующие изменения:  
  - В разделе Parser добавлены парсинг ethernet и ipv4 headers (Рис. 5).

<img width="386" alt="bf3" src="https://user-images.githubusercontent.com/59313334/209445260-5c3cb1eb-797f-4d2d-baf8-a56f0354e65a.png">  
Рисунок 5 - Раздел Parser  

   - В разделе Ingress processing добавлен метод, отвечающий за пересылки пакетов. Также он устанавливает выходной порт, обновляет MAC-адреса (исходный и конечный), уменьшает TTL и проверяет корректность заголовка ipv4 (Рис. 6, 7).  

<img width="384" alt="bf5" src="https://user-images.githubusercontent.com/59313334/209445264-abea7e88-6970-403e-954c-0933eee21195.png">  
Рисунок 6 - Раздел Ingress processing  

<img width="386" alt="bf6" src="https://user-images.githubusercontent.com/59313334/209445265-928d2e5e-974a-4d0c-a95d-36bb3e344521.png">  
Рисунок 7 - Раздел Ingress processing  

  - В разделе Deparser был добавлен депарсинг ethernet и ipv4 headers (Рис. 8)

<img width="383" alt="bf8" src="https://user-images.githubusercontent.com/59313334/209480292-463f46b1-5aa6-46a2-b650-3d07584e9038.png">
Рисунок 8 - Раздел Deparser  

## Результаты
  После внесённых изменений был снова запущен Mininet. С новой конфигурацией все пакеты доходят до места назначения (Рис. 9).  
![image](https://user-images.githubusercontent.com/59313334/209447338-980b079a-4baa-4ef6-81eb-df00da745e06.png)  
Рисунок 9 - Запуск Mininet с новыми настройками  

## Implementing Basic Tunneling
  Необходимо дополнить шаблон туннелирования так, чтобы была возможность туннелирования между всеми коммутаторами в сети (Рис. 10).
 
 
![image](https://user-images.githubusercontent.com/59313334/209479874-549cb07a-8b1d-4b8b-8d6a-ff365ead6695.png)  
Риснуок 10 - Целевая схема туннелирования  

  В файле basic_tunnel.p4 внесены следующие изменения:  
  - В разделе Parsers добавлено новое состояние state parse_myTunnel. Оно также добавлено в оператор select в state parse_ethernet (Рис. 11).  

<img width="384" alt="bt3" src="https://user-images.githubusercontent.com/59313334/209446457-fc725972-96b2-48bf-84a4-87d5cb2ef985.png">  
Рисунок 11 - Раздел Parsers  

  - В разделе Ingress processing добавлено новое действие action myTunnel_forward(egressSpec_t port и новая таблица table myTunnel_exact. Также было добавлено условие проверки ip-адреса: если туннель задан некорректно, то пакет отправляется обычным способом, а если верно - то через туннель (Рис. 12, 13).
  
<img width="385" alt="bt5" src="https://user-images.githubusercontent.com/59313334/209446461-79a47f81-1747-4ca4-a91f-aaaef673d74c.png">  
Рисунок 12 - Раздел Ingress processing  

<img width="389" alt="bt6" src="https://user-images.githubusercontent.com/59313334/209446462-ac7f398d-60a7-45ec-9a08-486a703d1e61.png">  
Рисунок 13 - Раздел Ingress processing  

  - В разделе Deparsers добавлено преобразование туннелируемых пакетов (Рис. 14). 

<img width="381" alt="bt8" src="https://user-images.githubusercontent.com/59313334/209446464-be8d6d84-7f08-4a71-b825-c35142ad4eb9.png">  
Рисунок 14 - Раздел Deparsers  


## Результаты

  Выполнен вход в Mininet.  
  Была проведена отправка пакетов без туннелирования (Рис. 15).  
<img width="544" alt="image_2022-12-21_23-03-57 (3)" src="https://user-images.githubusercontent.com/59313334/209446605-6f346d4f-94d3-438d-af7c-aa6cea834717.png">  
Рисунок 15 - Отправка пакетов без туннелирования

  Далее - отправка пакетов с туннелированием (Рис. 16, 17). 

<img width="519" alt="image_2022-12-21_23-03-57 (2)" src="https://user-images.githubusercontent.com/59313334/209446602-81aa2163-17ac-48e8-9095-05e38bb59f45.png">  
Рисунок 16 - Отправка пакетов с туннелированием  

<img width="502" alt="image_2022-12-21_23-03-57" src="https://user-images.githubusercontent.com/59313334/209446606-1e90e903-a5c8-46ba-98b6-21ed1419c4ad.png">  
Рисунок 17 - Отправка пакетов с туннелированием  



# Вывод:
  Были успешно реализованы базовая переадресация и базовое туннелирование с помощью языка P4. В ходе работы был изучен синтаксис языка P4.
