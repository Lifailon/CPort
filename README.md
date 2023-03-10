# CPort

### Модуль для проверки доступности TCP-портов.

Используется два метода из **класса .NET: System.Net.Sockets.TcpClient**, с применением **модуля ThreadJob**.

- [Установка](#Установка)
- [Синтаксис](#Синтаксис)
- [Метод ConnectAsync](#Метод-ConnectAsync)
- [Метод BeginConnect](#Метод-BeginConnect)

## Установка

* Установите модуль **[ThreadJob](https://www.powershellgallery.com/packages/ThreadJob/2.0.3)**: ` Install-Module -Name ThreadJob ` \
Запустите powershell, и проверьте, что модуль установлен: ` Get-Module ThreadJob -List `

* **Скопируйте директорию `cport` с модулем [cport.psm1](https://github.com/Lifailon/CPort/releases) в один из каталогов:** \
` C:\Users\%username%\Documents\WindowsPowerShell\Modules ` \
` C:\Program Files\WindowsPowerShell\Modules ` \

## Синтаксис

**Справка:** ` Get-Help cport `

Проверка нескольких портов на одном хосте: \
` cport 192.168.1.10 443 ` \
` cport 192.168.1.10 22,3389 ` \
` cport 192.168.1.10 20-70 `

Проверка хостов всей подсети: \
` cport 192.168.1.0 80 `

Быстрый режим (Fast Mode): \
` cport 192.168.1.0 80 100 `

![Image alt](https://github.com/Lifailon/CPort/blob/rsa/Screen/cport-1.3.jpg)

## Метод ConnectAsync

**Медленный метод**, т.к. не имеет возможности на уровне клиента (за исключением метода Wait) сократить время ожидания ответа от хоста. Данный способ собирает информацию о подключении, и возвращает ErrorCode, из которого можно инициализировать причину недоступности порта. \
**10061** - порт закрыт/фильтруется \
**10060** - хост недоступен \
**Преимущество:** удобно использовать в случае, **если закрыт icmp, с помощью проверки одного TCP-порта, можно выявить доступность хоста**. \
**Недостаток:** на проверку одного выключенного хоста уходит 20 секунд, а на доступном хосте 2 секунды.

**[Скачать (cport-1.1.ps1)](https://github.com/Lifailon/CPort/blob/rsa/Scripts/cport-1.1.ps1)** \
Импортировать: ` Import-Module .\cport-1.1.ps1 `

![Image alt](https://github.com/Lifailon/CPort/blob/rsa/Screen/1.1.%20Method-ConnectAsync.jpg)

## Метод BeginConnect

**Быстрый метод**, где мы пытаемся установить соединение, без последующего подключения. Тем самым можно задать **timeout (третий параметр)**, и сократить время сканирования портов, разрывая попытку соединения.

**Сравнение данного метода с и без использования ThreadJob**.

**Timeout 500 milliseconds:** \
![Image alt](https://github.com/Lifailon/CPort/blob/rsa/Screen/1.2.%20Method-BeginConnect.jpg)

**Timeout 100 milliseconds:** \
![Image alt](https://github.com/Lifailon/CPort/blob/rsa/Screen/1.3.%20Method-ConnectAsync-100ms.jpg)

**Исходя из полученных результатов, создание задания (Jobs) в итоге занимает ровно на 25% меньше времени, чем Sleep 100 Milliseconds, но тут уже зависит от производительности процессора.**

**[Модуль без применения ThreadJob (cport-1.2.ps1)](https://github.com/Lifailon/CPort/blob/rsa/Scripts/cport-1.2.ps1)**
