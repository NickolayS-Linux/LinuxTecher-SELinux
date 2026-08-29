# LinuxTecher-SELinux

Размещаем свой RPM в своем репозитории, ДЗ №8

Создал аккаунт на GitHub - https://github.com/

Предварительно установленное и настроенное следующее ПО:

Vagrant for Windows

Требуется предварительно установленный и работоспособный Hashicorp Vagrant

Oracle VirtualBox (https://www.virtualbox.org/wiki/Windows_Downloads).

Все дальнейшие действия были проверены при использовании VirtualBox 7.2.6 r172322, хостовая ОС: Windows 10.

Гостевая система — Almalinux/9 (версия 1.0.0) из https://vagrant.elab.pro/downloads/. 

**Цель домашнего задания:

Запустить Nginx на нестандартном порту 3-мя разными способами:

  * переключатели setsebool;

  * добавление нестандартного порта в имеющийся тип;

  * формирование и установка модуля SELinux.
**

Устанавливаем сначала Vagrant для Windows.

Подготавливаем файл - Vagrantfile для работы с среде Windows, и загрузки образа AlmaLinux для выполнения домашнего задания.

Vagrantfile:

<img width="1181" height="760" alt="image" src="https://github.com/user-attachments/assets/b760a339-0dcf-45b6-ab6f-d2b8d0d24dc4" />

В PowerShell выполняем команду, и производим установка VM с almalinux:

<img width="672" height="631" alt="image" src="https://github.com/user-attachments/assets/1879992d-3968-44f2-b52c-cc0ef3714f65" />

Образ устанавливает сразу Nginx, это можно увидеть на скриншоте ниже

<img width="686" height="550" alt="image" src="https://github.com/user-attachments/assets/2a303e82-a4e7-4b72-80b2-c821d549aff7" />

Во время развертывания VM можно увидеть что сервис nginx не запустился

<img width="825" height="299" alt="image" src="https://github.com/user-attachments/assets/ba1d94f6-8a96-49cf-b80b-37bc55894523" />

Данная ошибка появляется из-за того, что SELinux блокирует работу nginx на нестандартном порту.

После запуска VM выполняем команду:

<img width="502" height="19" alt="image" src="https://github.com/user-attachments/assets/4adb60cf-167f-46cb-b778-2164ca121078" />

и приступаю к выполнению ДЗ.

Дальнейшие действия выполняются от пользователя root. Переходим в root пользователя: sudo -i

<img width="376" height="88" alt="image" src="https://github.com/user-attachments/assets/8bc03ff2-ef8f-448c-a412-d41dad04f021" />

Для начала проверим, что в ОС отключен **файервол**: systemctl status firewalld

<img width="352" height="96" alt="image" src="https://github.com/user-attachments/assets/29d87cf9-adc7-4ca2-b210-d51d1ade5ecb" />

у меня данный сервис отсутствует.

Также можно проверить, что конфигурация **nginx** настроена без ошибок:

<img width="528" height="99" alt="image" src="https://github.com/user-attachments/assets/61e698da-3875-40c4-8963-ac876f67b29c" />

Далее проверю режим работы SELinux: 

<img width="358" height="125" alt="image" src="https://github.com/user-attachments/assets/05d0764a-3d1b-4b49-aaba-0ffa2405f732" />

Из скриншота видим, что у меня включен режим Enforcing. Данный режим означает, что SELinux будет блокировать запрещенную активность.

Разрешу в SELinux работу nginx на порту TCP 4881 c помощью переключателей setsebool

Находим в системных логах (/var/log/audit/audit.log) информацию о блокировании порта

<img width="843" height="56" alt="image" src="https://github.com/user-attachments/assets/2c345fe8-5dd7-4cdb-a038-9953325f576b" />



<img width="843" height="56" alt="image" src="https://github.com/user-attachments/assets/42b18e88-28a6-4c2d-b133-9a4df943726a" />
























