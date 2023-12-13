<p>University: [ITMO University](https://itmo.ru/ru/)</p>
<p></p>Faculty: [FICT](https://fict.itmo.ru)</p>
<p>Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)</p>
<p>Year: 2023/2024</p>
<p>Group: K33202</p>
<p>Author: Gusevskaya Arina Eduardovna</p>
<p>Lab: Lab1</p>
<p>Date of create: 14.11.2023</p>
<p>Date of finished: 11.12.2023</p>

<p align="center"> <h2> Отчёт по лабораторной работе №3 "Эмуляция распределенной корпоративной сети связи, настройка OSPF и MPLS, организация первого EoMPLS"</h2> </p>
<p><b>Цель:</b> Изучить протоколы OSPF и MPLS, механизмы организации EoMPLS.
<p><b>Ход работы:</b>
<p>1. В ходе предыдущих лабораторных работ были выполнены все подготовительные шаги, ничего устанавливать не нужно</p>
<p>2. Чтобы не образовалось конфликта сетей, были остановлены и удалены контейнеры из предыдущей лабораторной работы</p>
<p align="center">
 <img width="500px" src="pictures/terminate.png" alt="qr"/>
</p>
<p>3. Были прописаны те же топологии, но изменены устройства</p>
<h3>Выполнение</h3>
<p>1. Создадим сеть связи изображенную на рисунке 1 (из задания) в ContainerLab. Для этого пропишем топологию в файле lab2.yaml.
  
```
name: lab3

mgmt:
  network: statics
  ipv4-subnet: 172.20.15.0/24

topology:

  nodes:
    R01.NY:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.15.12

    R01.LND:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.15.13

    R01.LBN:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.15.14
      
    R01.HKI:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.15.15
      
    R01.MSK:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.15.16
      
    R01.SPB:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.15.17

    SGI_Prism:
      kind: linux
      image: alpine:latest
      mgmt-ipv4: 172.20.15.18

    PC1:
      kind: linux
      image: alpine:latest
      mgmt-ipv4: 172.20.15.19

  links:
    - endpoints: ["R01.NY:eth3", "R01.LND:eth2"]
    - endpoints: ["R01.NY:eth4", "R01.LBN:eth2"]
    - endpoints: ["R01.LND:eth3", "R01.HKI:eth2"]
    - endpoints: ["R01.LBN:eth3", "R01.MSK:eth2"]
    - endpoints: ["R01.LBN:eth4", "R01.HKI:eth4"]
    - endpoints: ["R01.HKI:eth3", "R01.SPB:eth3"]
    - endpoints: ["R01.MSK:eth3", "R01.SPB:eth2"]
    - endpoints: ["R01.SPB:eth4", "PC1:eth2"]
    - endpoints: ["R01.NY:eth2", "SGI_Prism:eth2"]
```
<p>2. С помощью команды ```clab deploy --topo lab3.yaml``` развернем лабораторию. На выходе получим 8 контейнеров.
<p align="center">
 <img width="500px" src="pictures/container.png" alt="qr"/>
</p> 
<p>3. Создадим схему связи, используя drawio
<p align="center">
 <img width="500px" src="Network.drawio.png" alt="qr"/>
</p>
<p>4. Перейдем к настройке оборудования
<p>Основные технологии:
<p><b>MPLS</b>  - применяется для ускорения передачи данных в сетях с помощью добавления labels(меток) к пакетам данных. При использовании происходит меньшая задержка и повышается пропускная способность.
<p><b>VPLS</b> - позволяет создавать виртуальные локальные сети VLANs на основе MPLS-сети. 
<p><b>EoMPLS</b> - используется для объединения локальных сетей в одну, что позволяет управлять трафиком более эффективно.
<p><b>MPLS LDP</b> - протокол, распределяющий labels.
<p><b>OSPF</b> - протокол маршрутизации, который используется для определения наиболее короткого пути между узлами в IP-сети.
  
<h5>R01.MSK</h5>

```

