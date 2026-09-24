# Wazuh Лаборатория
![Windows](https://img.shields.io/badge/Windows-11-0078D4?logo=windows11&logoColor=white)
![VirtualBox](https://img.shields.io/badge/VirtualBox-7.x-2F61B4?logo=virtualbox&logoColor=white)
![Wazuh](https://img.shields.io/badge/Wazuh-4.14-026EF8)
![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04-E95420?logo=ubuntu&logoColor=white)



## Содержание
- [Введение](#введение)
- [1. Установка Linux Ubuntu](#1-установка-linux-ubuntu)
- [2. Установка Wazuh](#2-установка-wazuh)
- [3. Доступ к Wazuh Dashboard](#3-доступ-к-wazuh-dashboard)
- [4. Установка и регистрация Wazuh Agent](#4-установка-и-регистрация-wazuh-agent)
- [5. File Integrity Monitoring](#step-5-file-integrity-monitoring)
- [Summary](#summary)



## Введение
### Что это?
В этом проекте описано развёртывание базовой домашней лаборатории **Wazuh** для отработки навыков работы в SOC. Лаборатория состоит из сервера Wazuh, работающего на виртуальной машине под управлением Ubuntu, и хоста под управлением Windows 11, выступающего в роли конечной точки с установленным агентом Wazuh. Цель этого проекта — продемонстрировать процесс развертывания, регистрацию агента и базовые функции мониторинга целостности файлов (FIM).

### Архитектура
| Компонент     | Хост                   | Роль                                                           |
| ------------- | ---------------------- | -------------------------------------------------------------  |
| Wazuh Manager | Ubuntu (VM)            | Собирает, анализирует и хранит данные, поступающие от агентов. |
| Wazuh Agent   | Windows (Host machine) | Отправляет логи и системные события Wazuh-менеджеру.           |

#### Конфигурация сети
Я использовал адаптер **Сетевой мост** в VirtualBox, чтобы поместить Ubuntu в одну сеть с хостом. Это обеспечивает прямое взаимодействие между Windows хостом и виртуальной машиной.

### Требования
- Гипервизор (я использовал VirtualBox)
- Linux Ubuntu VM (я использовал Ubuntu Server 24.04)
- Права администратора на Windows хосте
- Хотя бы 8 ГБ ОЗУ



## 1. Установка Linux Ubuntu
Я установил Ubuntu Server 24.04, чтобы избежать возможных проблем с совместимостью с Wazuh. Этот скриншот был сделан во время моей первой установки системы. Столкнувшись с некоторыми проблемами, я решил полностью удалить ОС и переустановить её (поэтому в дальнейшем её IP-адрес будет другим):

![Линукс Ubuntu установлена](/img/ubuntu-installed.png)

## 2. Установка Wazuh
Сначала я установил Wazuh GPG ключ для проверки всех пакетов после установки:

![GPG-ключ](/img/wazuh-gpg.png)

Затем я запустил команду для загрузки установочного скрипта и выполнил его с флагом `-a`, который устанавливает все необходимые компоненты сервера Wazuh (Indexer, Filebeat, Manager и Dashboard):

![Wazuh установлен](/img/wazuh-installed.png)

На этом этапе я столкнулся с проблемой: конфигурации не генерировались, поскольку по какой-то причине IP-адрес компьютера не добавился в конфигурационный файл. Из-за этого мне пришлось сделать это вручную и заново сгенерировать конфигурации (я не сделал скриншот этой части).



## 3. Доступ к Wazuh Dashboard
Wazuh Сервер наконец-то установлен. Теперь можно получить доступ к дашборду, введя IP-адрес машины Linux в свой браузер и войдя в систему, используя учётные данные, предоставленные в конце установки:

![Wazuh дашборд](/img/wazuh-loaded.png)



## 4. Установка и регистрация Wazuh Agent
### Установка
Затем я установил Wazuh Agent на эндпоинт, с которого надо собирать информацию. В моём случае этим устройством был мой компьютер с Windows 11. Установка проста: нужно просто скачать и запустить установщик, а затем следовать инструкции.

Вот так выглядит установленный Wazuh Agent:

![Wazuh агент](/img/wazuh-agent.jpg)

### Регистрация

Затем я зарегистрировал агента на сервере Wazuh. Для этого я запустил утилиту управления агентами на сервере, добавил нового агента, скопировал его ключ API и вставил его в Wazuh Agent. Также ввёл IP-адрес менеджера:

![API ключ](/img/api-key.png)

После перезагрузки Wazuh Agent, эндпоинт появился в дашборде:

![Agent добавлен](/img/agent-added.png)



## Step 5. File Integrity Monitoring
### Enabling
Wazuh supports real-time monitoring of file and folder changes using Syscheck.

To enable it, I edited the file located at the following path: `C:\Program Files (x86)\ossec-agent\ossec.conf`.

Specifically, I inserted the line `<directories realtime="yes">C:\Users\abc\Test</directories>` between the opening and closing **Directory** tags (replacing the example path with the directory I wanted to monitor):

![File Integrity Monitoring](/screens/file-integrity-monitoring.png)

### Example of how it works
If everything is configured correctly, all file changes are logged in the **Integrity Monitoring** section of the dashboard:

![FIM example](/screens/fim-example1.png)

![FIM example](/screens/fim-example2.png)

![FIM example](/screens/fim-file-deleted.png)



## Summary

In this lab I successfully deployed a basic Wazuh environment using Ubuntu Server and a Windows 11 endpoint. I installed and configured the Wazuh Server, connected a Windows agent, and verified communication between both systems. Finally, I enabled File Integrity Monitoring (FIM) and confirmed that file system changes were successfully detected and displayed in the Wazuh Dashboard. This project demonstrates the fundamental deployment and configuration steps required to build a simple SIEM environment for SOC learning and security monitoring.
