---
layout: post
title:  "Разворачиваем за минуту openvpn-сервер с пользовательскими ключами"
meta_description: "Инструкция по мгновенному поднятию OpenVPN сервера и быстрой генерации пользовательских ключей"
date:   2015-09-20 
tags: openvpn installer secure server ubuntu debian centos 
categories: article
---

## Вступление

Про правильную настройку OpenVPN на пользовательском компьютере (клиенте) <a href="/article/secure+openvpn+connection/" target="_blank">мы уже писали</a>, сегодня речь пойдёт о мгновенном развёртывании OpenVPN-сервера и генерации пользовательских ключей.<br>
Для этого можно использовать самую дохлую и дешёвую (3-5$ в год(!)) vps'ку, либо RaspberryPI.
<br><br>
Представленный ниже скрипт тестировался и работает на более-менее современных версиях Debian, Ubuntu, CentOS.

## Установка OpenVPN на сервер 

Вся установка сводится к скачиванию и запуску соответствующего скрипта (назвал его **OpenVPN Paranoid Installer**):

```
$ sudo su
# wget https://raw.githubusercontent.com/cryptopunks/openvpn_paranoid_installer/master/openvpn_paranoid_installer.sh --no-check-certificate -O openvpn_paranoid_installer.sh; bash openvpn_paranoid_installer.sh
```

Особые параноики могут <a href="https://raw.githubusercontent.com/cryptopunks/openvpn_paranoid_installer/master/openvpn_paranoid_installer.sh" target="_blank">скачать скрипт</a> самостоятельно, ознакомится с его содержанием и лишь после запустить; это - самое правильное решение для всего что качается из интернетов и github не исключение.

Инсталлятор запросит на какой из IP вешать OpenVPN, какой порт для него использовать (советую поставить нестандартный, либо 443, если он не занят https), какой DNS-сервер использовать (на выбор предоставит несколько вариантов), необходимый размер ключа, пароль (если требуется), после чего запросит имя первого клиентского файла с конфигом и ключами (их размер и пароль) и начнёт устанавливать необходимые пакеты и генерировать ключи.<br>
При этом все необходимые sysctl-переменные и правила iptables пропишутся автоматически в автозагрузку (используются файлы **/etc/sysctl.conf** и **/etc/rc.local**).<br><br>
Для большей безопасности можете воспользоваться советами из статьи <a href="/article/secure+storage+of+keys+openvpn+on+server/" target="_blank">Безопасное хранение ключей OpenVPN на сервере без шифрованного диска</a>.

## Работа с клиентом

После окончания работы скрипта в домашней директории появится файл с расширением **.ovpn** который и будет являться пользовательским конфигом (конфигом и всеми необходимыми ключами, всё-в-одном). Его необходимо скопировать на клиентскую машину и запустить: 

```
$ sudo openvpn --config client.ovpn
```

Файл этот необходимо хранить в тайне, потому как если он утечёт - можно будет расшифровать весь ваш трафик.<br><br>
Если вы работаете в \*nix-системе, то советую скопировать его в **/etc/openvpn**, сменить владельца на root и выставить чтение/запись только root:

```
# cp client.ovpn /etc/openvpn
# chown -R root:root /etc/openvpn
# chmod -R 600 /etc/openvpn 
```

Ну и запускать соответственно под root:

```
$ sudo openvpn --config /etc/openvpn/client.ovpn
```

## Что ещё умеет OpenVPN Paranoid Installer 

На момент написания статьи скрипт умеет при повторном запуске управлять пользовательскими ключами (создавать и отзывать их) и имеется функция удаления всего что он поставил и прописал в системе.<br>
<br>
Советую подписаться на репозиторий: <a href="https://github.com/cryptopunks/openvpn_paranoid_installer" target="_blank">https://github.com/cryptopunks/openvpn_paranoid_installer</a>, либо при поднятии нового VPN обращаться всегда к свежей версии, т.к. допиливаться сей инструмент планируется. 
<br><br>
Свои пожелания и предложения по улучшению можете отправлять <a href="/contacts/" target="_blank">на почту</a>.
