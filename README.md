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

И сам [файл](1/hsrp_advanced_hw.pkt) для программы Cisco Packet Tracer

------
### Задание 2
- Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного [файла](1/keepalived-simple.conf).
- Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах
- Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
- Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script
- На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html



------
### Выполнение 2
Что я делал:
1. На имеющиеся две ВМ (Ubuntu и Debian) я установил сервера NGINX. Изменил название индекс-файла на index.html
---
![Установака NGINX](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/nginx-inst-deb.png) ![Установака NGINX](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/nginx-inst-ubu.png)

---

2. Создал скрипт проверяющий доступность порта и существование файла index.html
---

```bash
#!/bin/bash
if [[ $(ss -lntup | grep LISTEN | grep :80) ]] && [[ -f /var/www/html/index.html ]]; then
        exit 0
else
        sudo systemctl stop keepalived
fi
```
---

3. Добавил конфигурационный файл в сервис keepalived
---
![Создание конфига](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/keep-conf-deb.png) ![Создание конфига](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/keep-conf-ubu.png)

---
4. Делаем рестарт сервиса:

```bash
sudo systemctl restart keepalived

```
---
5. Проверяем в браузере и видим, что первый сервер `192.168.0.103` откликается по плавающему IP-адресу `192.168.0.200`.

![Проверка плав.IP](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/200-103.png) 

Видим, что к интерфейсу `enp0s3` был добавлен плавающий IP-дрес `192.168.0.200`

![Проверка плав.IP](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/ip-a-1s.png)

Так же видим, что на втором сервере осутсвует дополнительный IP-адрес и есть только один `192.168.0.106`

![Проверка плав.IP на BACKUP Server](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/ip-2s.png)

6. Теперь попробуем отключить сервис `keepalived` на первом сервере и проверим переключение между серверами.
Вводим команду
```bash
sudo systemctl stop keepalived
```
и видим, что по IP-адресу `192.168.0.200` на первом сервере отображается начальная страница второго (BACKUP) сервера.

![Переключение на 106](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/switch-to-106.png)

Так же видим, что второй сервер получил плавающий IP-адрес и стал MASTER сервером
![200-106](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/200-106.png) ![master](https://github.com/Lexacbr/Keepalived-hw/blob/master/img/master.png)


------