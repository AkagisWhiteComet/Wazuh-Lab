# Wazuh Лаборатория
![Windows](https://img.shields.io/badge/Windows-11-0078D4?logo=windows11&logoColor=white)
![VirtualBox](https://img.shields.io/badge/VirtualBox-7.x-2F61B4?logo=virtualbox&logoColor=white)
![Wazuh](https://img.shields.io/badge/Wazuh-4.14-026EF8)
![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04-E95420?logo=ubuntu&logoColor=white)



## Содержание
- [Введение](#введение)
- [1. Установка Linux Ubuntu](#1-установка-linux-ubuntu)
- [2. Установка Wazuh](#2-установка-wazuh)
- [3. Accessing the Wazuh Dashboard](#step-3-accessing-the-wazuh-dashboard)
- [4. Wazuh Agent installation and registering](#step-4-wazuh-agent-installation-and-registering)
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



## Step 3. Accessing the Wazuh Dashboard
The Wazuh Server was finally installed. I could now access the dashboard by entering the Linux machine's IP address into my browser and logging in using the credentials provided at the end of the installation:

![Wazuh Dashboard](/screens/wazuh-loaded.png)



## Step 4. Wazuh Agent installation and registering
### Installation
Next, I installed the Wazuh Agent on the endpoint device from which I wanted to collect information. In my case, that device was my Windows 11 computer. The installation is straightforward: simply download and run the installer, then follow the installation wizard.

Here's what the installed Wazuh Agent looks like:

![Wazuh Agent](/screens/wazuh-agent.jpg)

### Registering

Next, I registered the agent with the Wazuh Server. To do this, I launched the agent management utility on the server, added a new agent, copied its API key, and pasted it into the Wazuh Agent. I also entered the manager's IP address:

![API key](/screens/api-key.png)

After restarting the Wazuh Agent, it appeared on the dashboard:

![Agent added](/screens/agent-added.png)



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
