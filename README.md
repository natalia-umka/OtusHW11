# Ansible

## HomeWork

Первые шаги с Ansible

Подготовить стенд на Vagrant как минимум с одним сервером. На этом сервере используя Ansible необходимо развернуть nginx со следующими условиями:

- необходимо использовать модуль yum/apt
- конфигурационные файлы должны быть взяты из шаблона jinja2 с перемененными
- после установки nginx должен быть в режиме enabled в systemd
- должен быть использован notify для старта nginx после установки
- сайт должен слушать на нестандартном порту - 8080, для этого использовать переменные в Ansible

* Сделать все это с использованием Ansible роли

Приложен Vagrantfile с одним сервером, где, используя Ansible, развернут nginx с вышеизложенными требованиями к дз

## **Инструкция по работе**

Сначала поднимаем управляемый хост командой vagrant up и проверяем что все успешно прошло и есть доступ по ssh:

[root@admin OtusHW11]# vagrant ssh-config

Host nginx

  HostName 127.0.0.1

  User vagrant

  Port 2222

  UserKnownHostsFile /dev/null

  StrictHostKeyChecking no

  PasswordAuthentication no

  IdentityFile /home/admin/OtusHW11/.vagrant/machines/nginx/virtualbox/private_key

  IdentitiesOnly yes

  LogLevel FATAL


Далее создаеми inventory файл - host. Чтобы каждый раз явно не указывать наш инвентори файл и не вписывать в него много информация, используем 
ansible.cfg файл.

Убедимся, что Ansible может управлять нашим хостом. Сделать это можно с помощью команды: 

[root@admin OtusHW11]# ansible nginx -m ping

nginx | SUCCESS => {

    "changed": false, 

    "ping": "pong"

}

В Playbook входит установка NGINX, шаблон для конфига NGINX и модуль, который будет копировать этот шаблон на хост, переменная, чтобы NGINX слушал на порту 8080, handler и добавим notify к копированию шаблона, чтобы каждый раз когда конфиг будет изменяться - сервис перезагружался. 

Запустим его:

[root@admin OtusHW11]# ansible-playbook nginx.yml 

PLAY [NGINX | Install and configure NGINX] ***********************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************

ok: [nginx]

TASK [NGINX | Install EPEL Repo package from standart repo] ******************************************************************************************

ok: [nginx]

TASK [NGINX | Install NGINX package from EPEL Repo] **************************************************************************************************

ok: [nginx]

TASK [NGINX | Create NGINX config file from template] ************************************************************************************************

changed: [nginx]

RUNNING HANDLER [reload nginx] ***********************************************************************************************************************

changed: [nginx]

PLAY RECAP *******************************************************************************************************************************************

nginx                      : ok=5    changed=2    unreachable=0    failed=0   


При переходе в браузере по адресу http://192.168.11.150:8080:

![alt-текст] (https://github.com/natali1701/OtusHW11/blob/master/Screenshot%20from%202019-04-23%2010-59-00.png)

Из консоли можем увидеть это же, выполнив команду:

[root@admin OtusHW11]# curl localhost:8080

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">

    <head>

        <title>Test Page for the Nginx HTTP Server on Fedora</title>

        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />

        <style type="text/css">

            /*<![CDATA[*/

            body {

                background-color: #fff;

                color: #000;

                font-size: 0.9em;

                font-family: sans-serif,helvetica;

                margin: 0;

                padding: 0;

            }

            :link {

                color: #c00;

            }

            :visited {

                color: #c00; 

...
