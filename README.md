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

После запуска VM выполняем команду:

<img width="502" height="19" alt="image" src="https://github.com/user-attachments/assets/4adb60cf-167f-46cb-b778-2164ca121078" />

и Приступаю к выполнению ДЗ.










