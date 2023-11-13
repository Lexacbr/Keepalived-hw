# Домашнее задание к занятию "`«Disaster recovery и Keepalived»`" - `Савельев Алексей - SYS-25`
------
### Задание 1
- Дана [схема](1/hsrp_advanced.pkt) для Cisco Packet Tracer, рассматриваемая в лекции.
- На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
- Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
- Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.
- На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.
------
### Выполнение 1
Что я делал:
1. В даном задании необходимо было произвести данную настройку роутера:
```
Router0>en
Router0#conf t
Router0(config)#interface gigabitEthernet 0/1
Router0(config-if)#standby 1 track gigabitEthernet 0/0
Router0(config-if)#standby preempt
Router0(config-if)#exit
Router0(config)#exit
Router0# 
``` 
![CPT](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/cpt1.png)
![CPT2](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/cpt2.png)
![confr](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/confr.png)
![showr](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/show-r.png)

И сам [файл](1/hsrp_advanced_hw.pkt)для программы Cisco Packet Tracer

------
### Задание 2
- Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного [файла](1/keepalived-simple.conf).
- Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах
- Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
- Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script
- На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html

![Название скриншота 2](ссылка на скриншот 2)`

------
### Выполнение 2
