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

**Запустить Nginx на нестандартном порту 3-мя разными способами:**

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

**Разрешу в SELinux работу nginx на порту TCP 4881 c помощью переключателей setsebool**

Находим в системных логах (/var/log/audit/audit.log) информацию о блокировании порта

<img width="843" height="56" alt="image" src="https://github.com/user-attachments/assets/2c345fe8-5dd7-4cdb-a038-9953325f576b" />

Копируем время, и с помощью утилиты audit2why смотрим 

<img width="834" height="168" alt="image" src="https://github.com/user-attachments/assets/31718d88-6d70-4d7d-8661-a9a214961b58" />

Утилита audit2why нам показывает почему трафик блокируется. Исходя из вывода утилиты, видим, что нам нужно поменять параметр nis_enabled. 

Включим параметр nis_enabled и перезапустим **nginx**: 

<img width="834" height="107" alt="image" src="https://github.com/user-attachments/assets/e70273d4-d933-4f5c-bc21-6e8cb61e89e4" />

Как можно увидеть сообщений об ошибках при запуске **nginx** нет, проверим статус сервиса:

<img width="833" height="284" alt="image" src="https://github.com/user-attachments/assets/56777615-3e2d-4c4a-a845-080bb2c83211" />

Так же видим службу в списке выполняем процессов:

<img width="699" height="86" alt="image" src="https://github.com/user-attachments/assets/729495f1-ee00-41fa-b408-1256080da0da" />

Также можно проверить работу nginx из браузера. Заходим в браузер на хосте и переходим по адресу http://127.0.0.1:4881

<img width="1862" height="858" alt="image" src="https://github.com/user-attachments/assets/4c56b964-51f0-4a0d-b9f7-2cb1d0546eb0" />

Работает!

Проверить статус параметра можно с помощью команды:

<img width="379" height="83" alt="image" src="https://github.com/user-attachments/assets/319c6e78-eca6-4ec1-a731-09fdf41f23e4" />

Вернём запрет работы **nginx** на порту 4881 обратно. Для этого отключу nis_enabled: 

<img width="412" height="84" alt="image" src="https://github.com/user-attachments/assets/75146ddc-4911-4306-a6df-b08d24bf0ae1" />

После отключения nis_enabled служба nginx снова не запустится.

**Теперь разрешим в SELinux работу **nginx** на порту TCP 4881 c помощью добавления нестандартного порта в имеющийся тип:**

<img width="590" height="100" alt="image" src="https://github.com/user-attachments/assets/9c6197fb-1c43-49bf-a612-eda700e24cc5" />

Добавим порт в тип http_port_t:

<img width="563" height="57" alt="image" src="https://github.com/user-attachments/assets/0cbb2484-2384-4519-b70d-4cec858139bd" />

<img width="666" height="71" alt="image" src="https://github.com/user-attachments/assets/8044b0f9-8c14-4282-b51b-d4cac040f7a6" />

Команда выполнена без ошибок. Теперь запустим **nginx**:

<img width="745" height="338" alt="image" src="https://github.com/user-attachments/assets/e1719991-ddc9-4c12-a5e3-8c663d06bf26" />

Теперь запустим браузер и проверим работу **nginx**:

<img width="1862" height="858" alt="image" src="https://github.com/user-attachments/assets/071d6a02-fd8c-45b1-a32f-8c590fb7fcdd" />

Удалить нестандартный порт из имеющегося типа можно с помощью команды:

<img width="586" height="73" alt="image" src="https://github.com/user-attachments/assets/06fe34ad-464e-4ac5-8750-a2a1f19a6919" />

Перезапустим службу и проверим её статус:

<img width="763" height="301" alt="image" src="https://github.com/user-attachments/assets/239b3be6-ae8a-40db-9f0e-415aa9e2c401" />

**Разрешим в SELinux работу nginx на порту TCP 4881 c помощью формирования и установки модуля SELinux:**

Попробуем снова запустить Nginx:

<img width="621" height="85" alt="image" src="https://github.com/user-attachments/assets/05fc55bb-a40f-4448-9dd3-aaa06b4991ad" />

**Nginx** не запустится, так как SELinux продолжает его блокировать. Посмотрим логи SELinux, которые относятся к **Nginx**:

<img width="838" height="167" alt="image" src="https://github.com/user-attachments/assets/d07ff31b-82f6-481b-9d23-681058673ace" />

Воспользуемся утилитой audit2allow для того, чтобы на основе логов SELinux сделать модуль, разрешающий работу nginx на нестандартном порту:

<img width="661" height="98" alt="image" src="https://github.com/user-attachments/assets/0fa91804-53b7-41d3-94c1-169ae5473b6a" />

Audit2allow сформировал модуль, и сообщил нам команду, с помощью которой можно применить данный модуль:

Применим модуль, запущу службу **nginx** и проверю её статус:

<img width="754" height="324" alt="image" src="https://github.com/user-attachments/assets/b5f08af2-602d-4977-9df4-1cbf7c075985" />

Первая часть ДЗ выполнена.

**Обеспечение работоспособности приложения при включенном SELinux**

Для того, чтобы развернуть стенд потребуется хост, с установленным git и ansible.

Git и Ansible были развернуты в консоли без графического терминала, при установке Ubuntu

Выполним клонирование репозитория:

<img width="875" height="470" alt="image" src="https://github.com/user-attachments/assets/cd71b4bd-abe7-49e1-8b72-b54bb2196501" />

После того, как стенд развернется, проверим ВМ с помощью команды:

<img width="729" height="202" alt="image" src="https://github.com/user-attachments/assets/a7418bdd-196c-4f51-a9ba-57f9951a9cf6" />

Подключимся к клиенту:

<img width="651" height="534" alt="image" src="https://github.com/user-attachments/assets/0aed4855-04a1-4883-83df-80951e393e97" />

Попробуем внести изменения в зону: 

<img width="609" height="378" alt="image" src="https://github.com/user-attachments/assets/ace6c808-373d-4d00-8278-610241e96c84" />

Изменения внести не получилось. ПGосмотрим логи SELinux, чтобы понять в чём может быть проблема:

<img width="1178" height="489" alt="image" src="https://github.com/user-attachments/assets/6ce9e01b-2175-4cce-b410-de27b2cd1a52" />

Не закрывая сессию на клиенте, подключусь к серверу ns01 и проверим логи SELinux:

<img width="1178" height="749" alt="image" src="https://github.com/user-attachments/assets/64af4f88-7398-4801-a716-de738c47caa0" />

В логах видно, что ошибка в контексте безопасности. Целевой контекст named_conf_t. Для сравнения посмотрим существующую зону (localhost) 

и её контекст:

<img width="1178" height="96" alt="image" src="https://github.com/user-attachments/assets/0b800334-9210-4291-bbaf-3df1422d0a02" />

У наших конфигов в /etc/named вместо типа named_zone_t используется тип named_conf_t.

Проверим данную проблему в каталоге /etc/named:

<img width="1030" height="247" alt="image" src="https://github.com/user-attachments/assets/a3591057-49b2-4deb-bb45-9c7f3c732f02" />

Тут также видим, что контекст безопасности неправильный. Проблема заключается в том, что конфигурационные файлы лежат в другом каталоге. 

Посмотреть в каком каталоги должны лежать, файлы, чтобы на них распространялись правильные политики SELinux можно с помощью команды: 

<img width="1091" height="464" alt="image" src="https://github.com/user-attachments/assets/36177c4c-96c5-4a5e-b755-e64a0a669694" />

Изменим тип контекста безопасности для каталога /etc/named:

<img width="1091" height="282" alt="image" src="https://github.com/user-attachments/assets/1bb8f481-d499-4320-a202-1223bf137818" />



