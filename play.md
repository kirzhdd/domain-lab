## play

### как настроить статический ip на windows
-> через настройки\сеть\ethernet\свойства адаптера\ipv4 \
в качестве днс-сервера указать себя

### как развернуть домен через диспетчер серверов
через диспетчер серверов: управление -> добавить роли и компоненты \
-> выбрать роль "Доменные службы Active Directory"
после установки нажать повысить сервер до уровня контроллера домена, где \
выбрать "добавить новый лес" и ввести dns-имя домена, перезагруить


### настрока условной пересылки
на виндовс\
в диспетчере серверов через вкладку DNS добавить сервер условной пересылки, \
выбрать опцию "Сохранять зону в Active Directory" (нужно чтобы при добавлении второго виндовс-кд не пришлось добавлять заново)\
на альт-домен\
в ```/etc/samba/smb.conf``` в блоке \[global\] в строке с "dns forwarder" добавить ip
виндовского кд, перезапустить samba


### проверка настройки условной пересылки
на виндовс\
```nslookup pc-name.domain-name``` должен вернуть ip альтовского кд \
на альт-домен\
```host -t A pc-name.domain-name``` должен вернуть ip виндовского кд


### проверка добавления доверия
через альт-домен\
```samba-tool domain trust list``` - в списке появится новое доверие


### удалить и добавить доверие
удалить - \
```samba-tool domain trust delete test.win``` \
добавить - \
```samba-tool domain trust create test.win --type=external --direction=both``` \
через виндовский кд: через cmd открыть domain.msc \
проверить - 
```
samba-tool domain trust list
Type[External] Transitive[No]  Direction[BOTH]     Name[test.win]
```

### проверка работоспособности доверия
1. на ресурсе одного домена получить билет пользователя из второго домена\
на альт:
```
[root@dc1 ~]# kinit Администратор@TEST.WIN
Password for Администратор@TEST.WIN:
[root@dc1 ~]# klist
Ticket cache: FILE:/tmp/krb5cc_0
Default principal: Администратор@TEST.WIN

Valid starting       Expires              Service principal
03.06.2026 07:39:26  03.06.2026 17:39:26  krbtgt/TEST.WIN@TEST.WIN
	renew until 10.06.2026 07:39:19
[root@dc1 ~]#
```
Kerberos видит второй домен test.win
