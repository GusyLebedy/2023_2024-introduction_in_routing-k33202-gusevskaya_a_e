<p>University: [ITMO University](https://itmo.ru/ru/)</p>
<p></p>Faculty: [FICT](https://fict.itmo.ru)</p>
<p>Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)</p>
<p>Year: 2023/2024</p>
<p>Group: K33202</p>
<p>Author: Gusevskaya Arina Eduardovna</p>
<p>Lab: Lab1</p>
<p>Date of create: 01.10.2023</p>
<p>Date of finished: ..2023</p>

<p align="center"> <h2> Отчёт по лабораторной работе №1 "Установка ContainerLab и развертывание тестовой сети связи"</h2> </p>
<p><b>Цель:</b> Ознакомиться с инструментом ContainerLab и методами работы с ним, изучить работу VLAN, IP адресации и т.д.
<p><b>Ход работы:</b>
<p>1. На подготовительном этапе лабораторной работы нами были выполнены следующие шаги.
  <p>• Установлен Docker на рабочий компьютер
  <p>• Установлен make и склонирован hellt/vrnetlab
  <p> • В проект hellt/vrnetlab в папку routeros загружен chr-6.47.9.vmdk и с помощью make docker-image собран образ.
  <p>•	Установлен ContainerLab 
<p>2. Основная часть работы
<h3>Топологии</h3>
<p>Для лучшего понимания разберемся, что значит каждый элемент топологии
<p>name (имя) - используется для отличия одной топологии от другой, чтобы разрешить развертывание нескольких топологий на одном хосте без конфликтов.
<p>topology (топология) - основной элемент файла (тело), который включает в себя строительные блоки nodes, kinds, defaults и links.
<p>nodes (узлы) - с помощью них мы определяем характеристики и конфигурацию элементов, которые хотим запустить.
<p>links (связи) - служит для определения связи между элементами.
<p>kinds (виды) - определяют поведение и природу узла, это говорит о том, является ли узел конкретной операционной системой контейнерной сети, виртуализированным маршрутизатором или чем-то еще.
<p>С поддердиваемыми kinds я ознакомилась в источнике https://containerlab.dev/manual/kinds/ и для лабораторной работы выбрала vr-ros(роутер и свичи) и linux (ПК)
<h3>Выполнение</h3>
<p>Создадим трехуровневую сеть связи классического предприятия изображенную на рисунке 1 (из задания) в ContainerLab. Для этого пропишем топологию в файле lab1.yaml.
```
name: lab1

mgmt:
  network: statics
  ipv4-subnet: 172.20.15.0/24

topology: 

  nodes:
    R01.TEST: 
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.15.12

    SW01.L3.01.TEST:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.15.13

    SW02.L3.01.TEST:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.15.14

    SW02.L3.02.TEST:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.15.15

    PC1:
      kind: linux
      image: alpine:latest
      mgmt-ipv4: 172.20.15.16

    PC2:
      kind: linux
      image: ubuntu:latest
      mgmt-ipv4: 172.20.15.17

  links: 
    - endpoints: ["R01.TEST:eth1","SW01.L3.01.TEST:eth1"] 
    - endpoints: ["SW01.L3.01.TEST:eth2","SW02.L3.01.TEST:eth1"] 
    - endpoints: ["SW01.L3.01.TEST:eth3","SW02.L3.02.TEST:eth1"] 
    - endpoints: ["SW02.L3.01.TEST:eth2","PC1:eth1"] 
    - endpoints: ["SW02.L3.02.TEST:eth2","PC2:eth1"] 
```
<p> С помощью команды `clab deploy --topo lab1.yaml` развернем лабораторию. На выходе получим 6 контейнеров.
<p><img src="pictures/1.jpg">
<p>Создадим схему связи, используя drawio</p>
<p><img src="Network.drawio.png">

