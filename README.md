# How to avoid DPI in linux - обход блокиоровкм сайтов DPI

https://youtu.be/FdDC9R7gL-Y?si=-dwbfxy0jqb_NMIg 

https://github.com/bol-van/zapret 

1. Клонируем репозиторий
```bash
git clone https://github.com/bol-van/zapret.git
``` 

```bash
sudo su
cd /opt
git clone https://github.com/bol-van/zapret.git
cd zapret

./install_bin.sh

./install_prereq.sh
```

- спросит, какой firewall хотим использовать. проверяем какой у нас есть 

```bash
iptables -h
```
если есть iptables, то выбриаем его 

### Установка DNS crypt proxy 

загуглим dnscrypt-proxy arch

https://wiki.archlinux.org/title/Dnscrypt-proxy 

```bash
sudo apt install dnscrypt-proxy


sudo nano /etc/dnscrypt-proxy/dnscrypt-proxy.toml
``` 
- раскомментировать строку server_names = 
- дописать в listen_addresses

listen_addresses = ['127.0.0.1:53', '[::1]:53']

- проверить если ли сервисыб прослуживающие порт 53
```bash
ss -lp 'sport = :domain'
```

- изменить файл  resolv.conf и вписать новые настройки сервера

- комментируем старые данные что есть и вставляем следующие строчки в файл /etc/resolv.conf

```bash
/etc/resolv.conf

nameserver ::1
nameserver 127.0.0.1
options edns0
``` 
- **При каждом перезапуске network manager файл /etc/resolv.conf будет перезаписываться. Чтобы избежать этого, еиу нужно задать атрибут Read only:**

```bash
sudo chattr +i /etc/resolv.conf
```

- запустить сервис dnscrypt-proxy 
```bash
sudo systemctl enable dnscrypt-proxy.service
sudo systemctl start dnscrypt-proxy.service
```

```bash
sudo systemctl enable systemd-resolved.service
sudo systemctl start systemd-resolved.service
``` 

- запускаем blockcheck.sh 
```bash
# ./blockcheck.sh
```
В конце указать повторение 5 раз
выбираем стандартную проверку


он выдаст 5 стратегий - выбрать нужно работающую

- запускаем скрипт install_easy

```bash
# ./install_easy.sh

```

указываем iptables 

выбираем режим - из работаеющей стратегии - возможно будет nfqws  

ключ
```bash
--dpi-desync=fake,split2 --dpi-desync-ttl=3 


```
