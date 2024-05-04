# Домашнее задание к занятию "`Очереди RabbitMQ`" - `Тен Денис`


### Задание 1. Установка RabbitMQ

Используя Vagrant или VirtualBox, создайте виртуальную машину и установите RabbitMQ.
Добавьте management plug-in и зайдите в веб-интерфейс.

*Итогом выполнения домашнего задания будет приложенный скриншот веб-интерфейса RabbitMQ.*

### Решение Задание 1. Установка RabbitMQ

Установка зависимостей
```bash
apt-get install curl gnupg apt-transport-https -y
```

Установка ключей
```-bash
## Team RabbitMQ's main signing key
curl -1sLf "https://keys.openpgp.org/vks/v1/by-fingerprint/0A9AF2115F4687BD29803A206B73A36E6026DFCA" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/com.rabbitmq.team.gpg > /dev/null
## Community mirror of Cloudsmith: modern Erlang repository
curl -1sLf https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key | sudo gpg --dearmor | sudo tee /usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg > /dev/null
## Community mirror of Cloudsmith: RabbitMQ repository
curl -1sLf https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key | sudo gpg --dearmor | sudo tee /usr/share/keyrings/rabbitmq.9F4587F226208342.gpg > /dev/null
```

Добавление репозиториев

```bash
## Add apt repositories maintained by Team RabbitMQ
sudo tee /etc/apt/sources.list.d/rabbitmq.list <<EOF
## Provides modern Erlang/OTP releases
##
deb [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa1.novemberain.com/rabbitmq/rabbitmq-erlang/deb/ubuntu jammy main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa1.novemberain.com/rabbitmq/rabbitmq-erlang/deb/ubuntu jammy main

# another mirror for redundancy
deb [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa2.novemberain.com/rabbitmq/rabbitmq-erlang/deb/ubuntu jammy main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa2.novemberain.com/rabbitmq/rabbitmq-erlang/deb/ubuntu jammy main

## Provides RabbitMQ
##
deb [signed-by=/usr/share/keyrings/rabbitmq.9F4587F226208342.gpg] https://ppa1.novemberain.com/rabbitmq/rabbitmq-server/deb/ubuntu jammy main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.9F4587F226208342.gpg] https://ppa1.novemberain.com/rabbitmq/rabbitmq-server/deb/ubuntu jammy main

# another mirror for redundancy
deb [signed-by=/usr/share/keyrings/rabbitmq.9F4587F226208342.gpg] https://ppa2.novemberain.com/rabbitmq/rabbitmq-server/deb/ubuntu jammy main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.9F4587F226208342.gpg] https://ppa2.novemberain.com/rabbitmq/rabbitmq-server/deb/ubuntu jammy main
EOF
```

Обновление индексов

```bash
sudo apt-get update -y
```

Установка Erlang

```bash
## Install Erlang packages
sudo apt-get install -y erlang-base \
                        erlang-asn1 erlang-crypto erlang-eldap erlang-ftp erlang-inets \
                        erlang-mnesia erlang-os-mon erlang-parsetools erlang-public-key \
                        erlang-runtime-tools erlang-snmp erlang-ssl \
                        erlang-syntax-tools erlang-tftp erlang-tools erlang-xmerl
```
						
Установка RabbitMQ

```bash
sudo apt-get install rabbitmq-server -y --fix-missing
```

Включение плагина управления RabbitMQ (RabbitMQ Management Plugin).

```bash
rabbitmq-plugins enable rabbitmq_management
```

Добавление пользователя

```bash
rabbitmqctl add_user tenda 123456789
```

Назначение прав администратора и разрешения на выполнение операций

```bash
rabbitmqctl set_user_tags tenda administrator
rabbitmqctl set_permissions -p / tenda ".*" ".*" ".*"
```
Включение флагов функциональности

```bash
rabbitmqctl enable_feature_flag all
systemctl restart rabbitmq-server.service
```

![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/1f00cbae-b43a-4ecd-b40e-a8592850d1a2)


---

### Задание 2. Отправка и получение сообщений

Используя приложенные скрипты, проведите тестовую отправку и получение сообщения.
Для отправки сообщений необходимо запустить скрипт producer.py.

Для работы скриптов вам необходимо установить Python версии 3 и библиотеку Pika.
Также в скриптах нужно указать IP-адрес машины, на которой запущен RabbitMQ, заменив localhost на нужный IP.

```shell script
$ pip install pika
```

Зайдите в веб-интерфейс, найдите очередь под названием hello и сделайте скриншот.
После чего запустите второй скрипт consumer.py и сделайте скриншот результата выполнения скрипта

*В качестве решения домашнего задания приложите оба скриншота, сделанных на этапе выполнения.*

Для закрепления материала можете попробовать модифицировать скрипты, чтобы поменять название очереди и отправляемое сообщение.

### Решение Задание 2. Отправка и получение сообщений

Проверка версии python

![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/bf1078e8-0fd0-4b6c-b86b-04fc053bcfb9)


Установка библиотеки pika

```bash
apt install python3-pip
pip3 install pika
```

![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/86f3c359-2a14-43f0-8379-2526a4bcf6e5)


```python
#!/usr/bin/env python3 # coding=utf-8
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()
channel.queue_declare(queue='hello')
channel.basic_publish(exchange='', routing_key='hello', body='Hello World!')
connection.close()
~
```

Проверка запуска скрипта producer.py

![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/f7afce53-2663-4be9-a771-1a64701172c8)

![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/91696fd0-d91d-4a90-bc0b-27639c2c3a3d)


Проверка запуска скрипта consumer.py

```python
#!/usr/bin/env python # coding=utf-8
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()
channel.queue_declare(queue='hello')

def callback(ch, method, properties, body):
    print(" [x] Received %r" % body)

channel.basic_consume(queue='hello', on_message_callback=callback,auto_ack=False)
channel.start_consuming()
```

Проверка
![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/bfe64c00-3c03-4312-9c0e-7463d4c285a7)

![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/c207d843-8dfc-4fa6-ac2a-2a53c2786953)

![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/9df7b843-3ac3-48a2-8db5-05c485b01e2c)



---

### Задание 3. Подготовка HA кластера

Используя Vagrant или VirtualBox, создайте вторую виртуальную машину и установите RabbitMQ.
Добавьте в файл hosts название и IP-адрес каждой машины, чтобы машины могли видеть друг друга по имени.

Пример содержимого hosts файла:
```shell script
$ cat /etc/hosts
192.168.0.10 rmq01
192.168.0.11 rmq02
```
После этого ваши машины могут пинговаться по имени.

Затем объедините две машины в кластер и создайте политику ha-all на все очереди.

*В качестве решения домашнего задания приложите скриншоты из веб-интерфейса с информацией о доступных нодах в кластере и включённой политикой.*

Также приложите вывод команды с двух нод:

```shell script
$ rabbitmqctl cluster_status
```

Для закрепления материала снова запустите скрипт producer.py и приложите скриншот выполнения команды на каждой из нод:

```shell script
$ rabbitmqadmin get queue='hello'
```

После чего попробуйте отключить одну из нод, желательно ту, к которой подключались из скрипта, затем поправьте параметры подключения в скрипте consumer.py на вторую ноду и запустите его.

*Приложите скриншот результата работы второго скрипта.*

### Решение Задание 3. Подготовка HA кластера

Установка по инструкции из пункта 1 RabbitMQ на 2й сервер.

Добавление в файл /etc/hosts - запись

```
10.159.86.98 ubuntu22-client
10.159.86.95 ubuntu22-server
```
Копирование cookie-файла /var/lib/rabbitmq/.erlang.cookie сервера ubuntu22-server на сервер ubuntu22-client (необходимо для аутентификации нод)

```bash
scp  /var/lib/rabbitmq/.erlang.cookie denis@10.159.86.98:/home/denis/
```
Выставление прав доступа на cookie-файл на сервере ubuntu22-client

```bash
cp /home/denis/.erlang.cookie /var/lib/rabbitmq/
chmod 400 /var/lib/rabbitmq/.erlang.cookie
```
Добавление 2й ноды в кластер

```bash
rabbitmqctl stop_app
rabbitmqctl join_cluster rabbit@ubuntu22-server
rabbitmqctl start_app
rabbitmqctl cluster_status
```
![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/a8c459a0-7155-4934-bb18-0720d4112cfb)


![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/2de1ec6b-9e14-4543-9eb8-b082b39c1077)



Создание политики репликации

```
rabbitmqctl set_policy ha-all "" '{"ha-mode":"all","ha-sync-mode":"automatic"}'
```
Проверка применения политики

![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/dd80bab3-ff14-4c6f-bb6f-e4308284169e)

Вопрос: Как можно определить какая функция Deprecated ?

Проверка состояния кластера

```bash
rabbitmqctl cluster_status
```

```
Cluster status of node rabbit@ubuntu22-server ...
Basics

Cluster name: rabbit@ubuntu22-server
Total CPU cores available cluster-wide: 16

Disk Nodes

rabbit@ubuntu22-client
rabbit@ubuntu22-server

Running Nodes

rabbit@ubuntu22-client
rabbit@ubuntu22-server

Versions

rabbit@ubuntu22-server: RabbitMQ 3.13.2 on Erlang 26.2.4
rabbit@ubuntu22-client: RabbitMQ 3.13.2 on Erlang 26.2.4

CPU Cores

Node: rabbit@ubuntu22-server, available CPU cores: 8
Node: rabbit@ubuntu22-client, available CPU cores: 8

Maintenance status

Node: rabbit@ubuntu22-server, status: not under maintenance
Node: rabbit@ubuntu22-client, status: not under maintenance

Alarms

(none)

Network Partitions

(none)

Listeners

Node: rabbit@ubuntu22-server, interface: [::], port: 15672, protocol: http, purpose: HTTP API
Node: rabbit@ubuntu22-server, interface: [::], port: 25672, protocol: clustering, purpose: inter-node and CLI tool communication
Node: rabbit@ubuntu22-server, interface: [::], port: 5672, protocol: amqp, purpose: AMQP 0-9-1 and AMQP 1.0
Node: rabbit@ubuntu22-client, interface: [::], port: 15672, protocol: http, purpose: HTTP API
Node: rabbit@ubuntu22-client, interface: [::], port: 25672, protocol: clustering, purpose: inter-node and CLI tool communication
Node: rabbit@ubuntu22-client, interface: [::], port: 5672, protocol: amqp, purpose: AMQP 0-9-1 and AMQP 1.0

Feature flags

Flag: classic_mirrored_queue_version, state: enabled
Flag: classic_queue_type_delivery_support, state: enabled
Flag: detailed_queues_endpoint, state: enabled
Flag: direct_exchange_routing_v2, state: enabled
Flag: drop_unroutable_metric, state: enabled
Flag: empty_basic_get_metric, state: enabled
Flag: feature_flags_v2, state: enabled
Flag: implicit_default_bindings, state: enabled
Flag: khepri_db, state: disabled
Flag: listener_records_in_ets, state: enabled
Flag: maintenance_mode_status, state: enabled
Flag: message_containers, state: enabled
Flag: quorum_queue, state: enabled
Flag: quorum_queue_non_voters, state: enabled
Flag: restart_streams, state: enabled
Flag: stream_filtering, state: enabled
Flag: stream_queue, state: enabled
Flag: stream_sac_coordinator_unblock_group, state: enabled
Flag: stream_single_active_consumer, state: enabled
Flag: stream_update_config_command, state: enabled
Flag: tracking_records_in_ets, state: enabled
Flag: user_limits, state: enabled
Flag: virtual_host_metadata, state: enabled
```





## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### * Задание 4. Ansible playbook

Напишите плейбук, который будет производить установку RabbitMQ на любое количество нод и объединять их в кластер.
При этом будет автоматически создавать политику ha-all.

*Готовый плейбук разместите в своём репозитории.*

### * Решение Задание 4. Ansible playbook

Установка Ansible на сервер управления rocky8-server

```bash
yum install ansible
```
Проверка

```bash
ansible --version
```
![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/022c5f96-4396-4883-a7dc-a8c2894d6a09)

Подключение сервера управления rocky8-server к серверам RabbitMQ ubuntu22-server и ubuntu22-client

Генерируем пару ключей (закрытый id_rsa и открытый id_rsa.pub) SSH типа RSA на сервере rocky8-server

```bash
ssh-keygen -t rsa
```
![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/d33d1ab9-971d-4f6f-8432-05700d37b180)

Добавляем записи в файл /etc/hosts на всех серверах

```bash
echo -e "10.159.86.95 ubuntu22-server\n10.159.86.98 ubuntu22-client\n10.159.86.79 rocky8-server" >> /etc/hosts
```
Копируем открытый ключ SSH с сервера rocky8-server на сервера ubuntu22-server и ubuntu22-client

```bash
ssh-copy-id denis@ubuntu22-server
ssh-copy-id denis@ubuntu22-client
```
![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/18caaad4-3e2d-422d-8f29-43d9765e0b5a)

На серверах ubuntu22-server и ubuntu22-client копируем открый ключ SSH из домашеней директории пользователя denis в директорию пользователя root

```bash
cp /home/denis/.ssh/authorized_keys /root/.ssh/
```
На сервере rocky8-server настраиваем Ansible

```bash
mkdir ansible
cd ansible
```
Создаем inventory файл inventory.ini

```
[servers]
ubuntu22-server ansible_host=10.159.86.95
ubuntu22-client ansible_host=10.159.86.98
```
На сервере rocky8-server правим конфигурационный файл /etc/ansible/ansible.cfg

```bash
[defaults]
inventory = /root/ansible/inventory.ini
```
Проверяем доступ серверовh
```bash
ansible servers -m ping
```

![image](https://github.com/killakazzak/11-04-rabbitmq-hw/assets/32342205/57cac1ad-bd70-4113-bb41-61d0a9f285a2)

Создаем Ansible playbook rabbitmq.yml

```yaml
---
- name: Установка и настройка RabbitMQ
  hosts: servers
  become: true
  vars:
    rabbitmq_cluster_nodes: "{{ groups['servers'] }}"
    rabbitmq_ha_policy: "ha-all"
  tasks:
    - name: Установка пакетов RabbitMQ
      apt:
        name: rabbitmq-server
        state: present

    - name: Запуск службы RabbitMQ
      service:
        name: rabbitmq-server
        state: started
        enabled: true

    - name: Добавление узлов кластера RabbitMQ
      rabbitmq_cluster:
        rabbitmqctl: /usr/sbin/rabbitmqctl
        node: "{{ inventory_hostname }}"
        cluster_nodes: "{{ rabbitmq_cluster_nodes }}"
        state: present

    - name: Создание политики ha-all
      rabbitmq_policy:
        rabbitmqctl: /usr/sbin/rabbitmqctl
        name: "{{ rabbitmq_ha_policy }}"
        vhost: /
        pattern: ".*"
        definition: '{"ha-mode":"all"}'
        state: present
```





