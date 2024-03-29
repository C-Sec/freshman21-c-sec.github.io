---
layout: post
title:  "Snort: Введение"
date:   2017-02-01 01:00:00
categories: Snort
tags: Snort IDS IPS
author: RAlexeev
---

* Table of Contents
{:toc}

Snort является сетевой системой обнаружения (IDS) и предотвращения вторжений (IPS) с открытым исходным кодом, способная выполнять регистрацию пакетов и в реальном времени осуществлять анализ трафика в IP-сетях, комбинируя возможности сопоставления по сигнатурам, средства для инспекции протоколов и механизмы обнаружения аномалий. Snort был создан Мартином Решем в 1998-м году и очень быстро завоевал популярность, как бесплатная система обнаружения вторжений, позволяющая самостоятельно и без особых усилий писать правила для обнаружения атак. По сути язык описания сигнатур Snort стал стандартом де-факто для многих систем обнаружения вторжений, которые стали его использовать в своих движках.

![Snort Logo](http://i.imgur.com/JclowSu.png)

<!-- more -->

## Структура и функционирование Snort

Систему обнаружения вторжений Snort по способу мониторинга системы можно отнести как к узловой, так и к сетевой системе в зависимости от параметров настройки. Обычно она защищает определённый сегмент локальной сети от внешних атак из интернета. Система Snort выполняет протоколирование, анализ, поиск по содержимому, а также широко используется для активного блокирования или пассивного обнаружения целого ряда нападений и зондирований. Snort способен выявлять:

* Плохой трафик
* Использование эксплойтов (выявление Shellcode)
* Сканирование системы (порты, ОС, пользователи и т.д.)
* Атаки на такие службы как Telnet, FTP, DNS, и т.д.
* Атаки DoS/DDoS
* Атаки связанные с Web серверами (cgi, php, frontpage, iss и т.д.)
* Атаки на базы данных SQL, Oracle и т.д.
* Атаки по протоколам SNMP, NetBios, ICMP
* Атаки на SMTP, imap, pop2, pop3
* Различные Backdoors
* Web-фильтры (чаще используетя для блокировки порно контента)
* Вирусы

Вы можете настроить Snort для работы в нескольких различных режимах — режим анализа пакетов, режим журналирования пакетов, режим обнаружения сетевых вторжений и встраиваемым (inline) режим. Snort может быть сконфигурирован для работы в этих режимах:


### **Режиме анализа пакетов (Sniffer mode)**

Snort просто читает пакеты приходящие из сети и выводит их на экран. В этом режиме Snort действует просто как анализатор, показывая нефильтрованное содержимое среды передачи. Конечно, если вам требуется только анализатор, можно применить Tcpdump или Ethereal, однако данный режим позволяет убедиться, что все работает правильно и Snort видит пакеты.

Опции Snort:

| Опция        | Описание          |
|:-------------|:------------------|
| **-v**       | Выводит на экран IP и TCP/UDP/ICMP заголовки пакетов. (verbose mode) <br> Пример вывода Snort, запущенного с ключом `snort -v`, на команду `ping 192.168.0.116`: <br> {% gist RAlexeev/91a26f47a409d2e6b3131353f7e65400 snort-v %} |
| **-d**       | Отображает данные прикладного уровня, используется в подробном режиме вывода (опция -v) или в режиме журналирования пакетов. <br> Пример вывода Snort, запущенного с ключами `snort -vd`, на команду `ping 192.168.0.116`: <br> {% gist RAlexeev/91a26f47a409d2e6b3131353f7e65400 snort-vd %} |
| **-e**       | Включает вывод заголовков канального уровня. <br> Пример вывода Snort, запущенного с ключами `snort -vde`, на команду `ping 192.168.0.116`: {% gist RAlexeev/91a26f47a409d2e6b3131353f7e65400 snort-vde %} |


### Режиме журналирования (протоколирования) пакетов (Packet Logger mode)

Позволяет записывать пакеты на диск для последующего анализа. Это полезно при проведении анализа за определенный интервал времени или проверки изменений в настройках и политике безопасности.
Чтобы запустить Snort в режиме журналирования, воспользуйтесь той же командой, что и для режима анализа ( -v, -d и/или -e ), но с добавлением ключа -l каталог_журналов, задающего маршрутное имя каталога журналов, в которые Snort будет записывать пакеты. Пример:
```
snort -vde -l /var/log/snort
```
Эта команда создаст файлы журналов в каталоге /var/log/snort.

### Режиме обнаружения сетевых вторжений (Network Intrusion Detection System (NIDS) mode)

Наиболее сложный и конфигурируемый режим, который позволяет анализировать сетевой трафик и выполнять обнаружение вторжений на основе набора правил.
В этом режиме Snort протоколирует подозрительные или требующие дополнительного внимания пакеты. Для перевода Snort в режим обнаружения вторжений достаточно добавить к приведенной выше инструкции ключ `-c конфигурационный_файл`, предписывающий использовать указанный конфигурационный файл для управления протоколированием пакетов. Конфигурационный файл определяет все настройки Snort, он очень важен. Snort поставляется с подразумеваемым конфигурационным файлом, но перед запуском в него целесообразно внести некоторые изменения, отражающие специфику вашей среды.


### Встраиваемым режим (inline mode)

Режим работы совместно с файерволом iptables.
Для того, чтобы запустить в этом режиме, необходимо добавить дополнительный ключ Q:
`./snort -GDc ../etc/drop.conf -l /var/log/snort`
Перед запуском в этом режиме необходимо убедиться, что программа установлена с поддержкой данного режими. После этого следует настроить файервол для взаимодействия со Snort.



## Архитектура Snort

Основу Snort составляет движок, состоящий из пяти модулей:

* Снифер пакетов: данный модуль отвечает за захват передаваемых по сети данных для последующей их передаче на декодер. Делает он это с помощью библиотеки DAQ (Data AcQuisition). Работать данный снифер может "в разрыв" (inline), в пассивном режиме (passive) или читать сетевые данные из заранее подготовленного файла.

* Декодер пакетов: данный модуль занимается разбором заголовков захваченных пакетов, их разбором, поиском аномалий и отклонений от RFC, анализом TCP-флагов, исключением отдельных протоколов из дальнейшего анализа и другой аналогичной работой. Фокусируется данный декодер на стеке TCP/IP.

* Препроцессоры: если декодер разбирал трафик на 2-м и 3-м уровне эталонной модели, то препроцессоры предназначены для более детального анализа и нормализации протоколов на 3-м, 4-м и 7-м уровнях. Среди самых популярных препроцессоров можно назвать frag3 (работа с фрагментированным трафиком), stream5 (реконструкция TCP-потоков), http_inspect_ (нормализация HTTP-трафика), DCE/RPC2, sfPortscan (применяется для обнаружения сканирования портов) и различные декодеры для протоколов Telnet, FTP, SMTP, SIP, SSL, SSH, IMAP и т.п. Некоторые российские разработчики пишут свои препроцессоры (например, для промышленных протоколов) и добавляют в собственные системы обнаружения вторжений (IDS), построенные на базе Snort.

* Движок обнаружения атак: данный движок состоит из двух частей. Конструктор правил собирает множество различных решающих правил (сигнатур атак) в единый набор, оптимизированный для последующего применения подсистемой инспекции захваченного и обработанного трафика в поисках тех или иных нарушений.

* Модуль вывода: по факту обнаружения атаки Snort может выдать (записать или отобразить) соответствующее сообщение в различных форматах — файл, syslog, ASCII, PCAP, Unified2 (двоичный формат для ускоренной и облегченный обработки).

Рассмотрим простое графическое представление прохождения данных через Snort:

![Snort DataFlow](http://i.imgur.com/CQgvJkd.png)
