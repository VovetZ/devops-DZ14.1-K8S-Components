# devops-DZ14.1-K8S-Components

# Домашнее задание к занятию «Компоненты Kubernetes»

### Цель задания

Рассчитать требования к кластеру под проект

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

- [Considerations for large clusters](https://kubernetes.io/docs/setup/best-practices/cluster-large/),
- [Architecting Kubernetes clusters — choosing a worker node size](https://learnk8s.io/kubernetes-node-size).

------

### Задание. Необходимо определить требуемые ресурсы

Известно, что проекту нужны база данных, система кеширования, а само приложение состоит из Backendа и Frontendа. Опишите, какие ресурсы нужны, если известно:

1. Необходимо упаковать приложение в чарт для деплоя в разные окружения.
2. База данных должна быть отказоустойчивой. Потребляет 4 Gb ОЗУ в работе, 1 ядро. 3 копии.
3. Кеш должен быть отказоустойчивый. Потребляет 4 Gb ОЗУ в работе, 1 ядро. 3 копии.
4. Frontend обрабатывает внешние запросы быстро, отдавая статику. Потребляет не более 50 МБ ОЗУ на каждый экземпляр, 0.2 ядра. 5 копий.
5. Backend потребляет 600 МБ ОЗУ и по 1 ядру на копию. 10 копий.

----

### Правила приёма работы

1. Домашняя работа оформляется в своем Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Сначала сделайте расчёт всех необходимых ресурсов.
3. Затем прикиньте количество рабочих нод, которые справятся с такой нагрузкой.
4. Добавьте к полученным цифрам запас, который учитывает выход из строя как минимум одной ноды.
5. Добавьте служебные ресурсы к нодам. Помните, что для разных типов нод требовния к ресурсам разные.
6. В результате должно быть указано количество нод и их параметры.

------

## Ответ

- Ресурсы для каждого компонента

  1. **DBMS**
    1 CPU core, 4 Gb RAM; 3 копии. Итого 4х3 = 12 Gb RAM, 1x3 = 3 CPU cores
  2. **Cache**
    1 CPU core, 4 Gb RAM; 3 копии. Итого 4х3 = 12 Gb RAM, 1x3 = 3 CPU cores
  3. **Frontend**
    0.2 CPU core, 50 Mb RAM; 5 копий. Итого 50х5 = 250 Mb RAM, 0.2x5 = 1 CPU cores
  4. **Backend**
    1 CPU core, 600 Mb RAM; 10 копий. Итого 600х10 = 6 Gb RAM, 1x10 = 10 CPU cores

**Итого (с округлением в большую сторону): 31 Mb RAM, 17 CPU cores**

- Расчет рабочих нод для компонент

Рассмотрим три варианта распределения по нодам

- ВАРИАНТ 1.Один компонент - одна нода

    1. **DBMS**
  - 3 копии на 3 нодах
  - +15 % на запас ресурсов на сервисные службы
  - +1 нода на выход из строя ноды
  - В сумме 4 ноды по 1.15 CPU core, 4.6 Gb RAM

    2. **Кеш**
  - 3 копии на 3 нодах
  - +15 % на запас ресурсов на сервисные службы
  - +1 нода на выход из строя ноды
  - В сумме 4 ноды по 1.15 CPU core, 4.6 Gb RAM

    3. **Frontend**
  - 5 копий на 5 нодах
  - +15 % на запас ресурсов на сервисные службы
  - +1 нода на выход из строя ноды
  - В сумме 6 нод по 0.23 CPU core, 575 Mb RAM

    4. **Backend**
  - 10 копий на 10 нодах
  - +15 % на запас ресурсов на сервисные службы
  - +1 нода на выход из строя ноды
  - В сумме 11 нод по 1.15 CPU core, 690 Mb RAM

    5. **Master nodes**
  - 1 CPU core, 4 Gb RAM, 3 ноды

**Итого (с округлением в большую сторону): 28 нод, 27 CPU core, 57 Gb RAM**

- ВАРИАНТ 2. DBMS+Cache на своих нодах, Frontend+Backend на своих нодах

  1. **DBMS+Cache**
  - 3 копиии на 3 нодах для баз данных и кеш
  - +15 % на запас ресурсов на сервисные службы
  - +1 нода на выход из строя ноды
  - Итого: 4 ноды по 2.3 CPU core, 9.2 Gb RAM

  2. **Frontend+Backend**
  - 5 копий Frontend и 10 копий Backend на 10 нодах
  - +15 % на запас ресурсов на сервисные службы
  - +1 нода на выход из строя ноды
  - Итого: 11 нод по 1.38 CPU core, 748 Mb RAM

  3. **Master nodes**
  - 1 CPU core, 4 Gb RAM, 3 ноды

**Итого (с округлением в большую сторону): 18 нод, 28 CPU core, 57 Gb RAM**

- ВАРИАНТ 3.Все компоненты на общих нодах
  
  1. **Worker nodes**
  - 3 копиии для баз данных и кеш, 5 копий Frontend и 10 копий Backend на 10 нодах
  - +15 % на запас ресурсов на сервисные службы
  - +1 нода на выход из строя ноды
  - Итого: 11 нод по 3.68 CPU core, 9.9475 Gb RAM
   
  2. **Master nodes**
  - 1 CPU core, 4 Gb RAM, 3 ноды
  
**Итого (с округлением в большую сторону): 14 нод, 44 CPU core, 122 Gb RAM**
