# Использование SSH

В этом разделе будет рассказано, как подключиться к серверу с помощью протокола SSH, а также как сконфигурировать OpenSSH - клиент по умолчанию для Linux.

## Глоссарий

*Имя хоста* - по сути, адрес сервера в интернете.

*Логин* - имя пользователя, которое используется для входа на сервер.

*Порт* - некоторая числовая характеристика единичного подключения к серверу. Для SSH-соединения по умолчанию это 22.

## Подключение к серверу

Подключиться к серверу достаточно легко - для этого достаточно указать имя хоста и логин:

`ssh имя_хоста -l логин`

Или так:

`ssh логин@имя_хоста`

Если необходимо указать порт, это делается с помощью аргумента `-p`:

`ssh имя_хоста -l логин -p порт`

При подключении, возможно, потребуется ввести пароль.

## Настройка подключения

Для того, чтобы не вводить все данные по подключению каждый раз, можно внести их заранее в конфигурационный файл.

Путь к этому файлу - `~/.ssh/config`. Если такого файла нет, его нужно создать.

Придумаем псевдоним для нашего сервера и занесём данные о подключении в следующем формате:

```bash
Host псевдоним
	Hostname имя_хоста
	User логин
```

Здесь можно задать самые разные параметры, например:

- `Port` - порт.
- `ServerAliveInterval` - интервал (в секундах), через который на сервер будет отправлен запрос, если от него не поступало данных (это делается для предотвращения внезапного обрыва соединения)
- `ServerAliveCountMax` - количество попыток восстановить соединение

После сохранения файла можно будет подключаться к серверу командой `ssh псевдоним`.

## Вход без пароля

На сервер можно входить и без пароля, если сгенерировать пару RSA-ключей.

Для этого можно воспользоваться командой `ssh-keygen`.
Сначала в неё нужно ввести имя пары ключей, а затем задать и подтвердить кодовую фразу (которую затем надо будет каждый раз вводить при использовании ключа). 
Если вместо этого дважды нажать Enter, кодовая фраза задана не будет - так и нужно поступить.
Пример использования:

```
~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/alexander/.ssh/id_rsa): имя_ключа
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in имя_ключа
Your public key has been saved in имя_ключа.pub
The key fingerprint is:
SHA256:OtERqh9AhBtVR/PgS2oBPF5EWsw8f8zhoSKzDHelHY0 alexander@alexander-pc
The key's randomart image is:
+---[RSA 3072]----+
|   ==B=.*o       |
|  o.o+*+E=+      |
|   +ooo*+*.o     |
|  o =o+=+o=      |
|   +.==.S.       |
|    oo +         |
|      +          |
|       .         |
|                 |
+----[SHA256]-----+
```

Чтобы настроить вход по ключу без пароля, нужно воспользоваться командой `ssh-copy-id`:

`ssh-copy-id логин@имя_хоста`

В процессе потребуется ввести пароль. После выполнения можно подключаться к серверу без ввода пароля.

**Важно.** Пара ключей по умолчанию имеет имя `id_rsa`. Если для подключения используется пара с другим именем, это нужно указать при настройке сервера:

`ssh-copy-id -i имя_ключа логин@имя_хоста`

И при настройке подключения - в файл `~/.ssh/config` нужно добавить следующую строчку:

`IdentityFile имя_ключа`

----

[На главную](../Readme.md)