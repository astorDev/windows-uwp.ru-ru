---
author: mijacobs
Description: Многие предприятия используют брандмауэры для блокировки нежелательного трафика. В этом документе описывается, как разрешить передачу трафика WNS через брандмауэры.
title: Добавление трафика WNS в брандмауэр разрешенных
ms.assetid: 2125B09F-DB90-4515-9AA6-516C7E9ACCCD
template: detail.hbs
ms.author: mijacobs
ms.date: 05/20/2019
ms.topic: article
keywords: Windows 10, UWP, WNS, служба уведомлений Windows, уведомление, Windows, брандмауэр, устранение неполадок, IP, трафик, предприятие, сеть, IPv4, VIP, полное доменное имя, общедоступный IP-адрес
ms.localizationpriority: medium
ms.openlocfilehash: 1f8a72eec46971fa27a4bd0dec112430f2eb3535
ms.sourcegitcommit: 789bfe3756c5c47f7324b96f482af636d12c0ed3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68867309"
---
# <a name="allowing-windows-notification-traffic-through-enterprise-firewalls"></a>Разрешение трафика уведомлений Windows через брандмауэры предприятия

## <a name="background"></a>Фон
Многие компании используют брандмауэры для блокировки нежелательного сетевого трафика; к сожалению, это также может блокировать такие важные вещи, как связь со службой уведомлений Windows. Это означает, что все уведомления, отправляемые через WNS, будут удалены. Чтобы избежать этого, администраторы сети могут добавить список утвержденных каналов WNS в их список исключений, чтобы разрешить передачу трафика WNS через брандмауэр. Ниже приведены дополнительные сведения о том, как и что следует добавить. 

> [!Note] 
Начиная с 6/24/2019 клиенты Windows **не** поддерживают прокси-серверы, подключение к WNS должно быть прямым подключением.

## <a name="what-information-should-be-added-to-the-allowlist"></a>Какие сведения следует добавить в разрешенных
Ниже приведен список, содержащий полные доменные имена, VIP и IP-адреса, используемые службой уведомлений Windows. 

> [!IMPORTANT]
> Мы настоятельно рекомендуем разрешить список по полному доменному имени, так как они не изменятся. При разрешении списка по полному доменному имени не нужно разрешать диапазоны IP-адресов.

> [!IMPORTANT]
> Диапазоны IP-адресов будут периодически изменяться; по этой причине они не включаются на эту страницу. Если вы хотите просмотреть список диапазонов IP-адресов, скачайте файл из центра загрузки: [VIP и диапазоны IP-адресов службы уведомлений Windows (WNS)](https://www.microsoft.com/download/details.aspx?id=44238). Регулярно проверяйте его, чтобы убедиться, что у вас есть самые последние сведения. 


### <a name="fqdns-vips-and-ips"></a>Полные доменные имена, VIP и IP-адреса
Каждый из элементов в следующем XML-документе объясняется в таблице, следующей за ней (в [терминах и нотациях](#terms-and-notations)). Диапазоны IP-адресов были намеренно оставлены в этом документе, чтобы помочь вам использовать только полные доменные имена, так как FQDN будет оставаться постоянным. Однако можно скачать XML-файл, содержащий полный список, из центра загрузки: [VIP и диапазоны IP-адресов службы уведомлений Windows (WNS)](https://www.microsoft.com/download/details.aspx?id=44238). Новые VIP или диапазоны IP-адресов будут **действовать на одну неделю после отправки**.

```XML
<?xml version="1.0" encoding="UTF-8"?>
<WNSPublicIpAddresses xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
    <!-- This file contains the FQDNs, VIPs, and IP address ranges used by the Windows Notification Service. A new text file will be uploaded every time a new VIP or IP range is released in production.  Please copy the below information and perform the necessary changes on your site. Endpoints in CloudService nodes are used for cloud services to send notifications to WNS. Endpoints in Client nodes are used by devices to receive notifications from WNS. --> 
    <CloudServiceDNS>
    <DNS FQDN="*.notify.windows.com"/>
    </CloudServiceDNS>
    <ClientDNS>
        <DNS FQDN="*.wns.windows.com"/>
        <DNS FQDN="*.notify.live.net"/>
    </ClientDNS>
    <CloudServiceIPs>
        <IpRange Subnet=""/>
        <!-- See the file in Download Center for the complete list of IP ranges -->
    </CloudServiceIPs>
    <ClientIPsIPv4>
        <IpRange Subnet=""/>
        <!-- See the file in Download Center for the complete list of IP ranges -->
    </ClientIPsIPv4>
</WNSPublicIpAddresses>

```

### <a name="terms-and-notations"></a>Термины и обозначения
Ниже приведены пояснения к нотациям и элементам, используемым в приведенном выше фрагменте кода XML.

| Термин | Объяснение |
|---|---|
| **Точечная-десятичная нотация (т. е. 64.4.28.0/26)** | Десятичная нотация — это способ описания диапазона IP-адресов. Например, 64.4.28.0/26 означает, что первые 26 бит 64.4.28.0 являются константами, а последние 6 бит — переменными.  В этом случае диапазон IPv4 — 64.4.28.0-64.4.28.63. |
| **клиентднс** | Это полные фильтры доменных имен (FQDN) для клиентских устройств (компьютеров Windows, настольных компьютеров), получающих уведомления от WNS. Они должны быть разрешены через брандмауэр, чтобы клиенты WNS могли использовать функцию WNS.  Рекомендуется разрешить в список FQDN, а не диапазоны IP/VIP, так как они никогда не изменятся. |
| **ClientIPsIPv4** | Это IPv4-адреса серверов, к которым обращаются клиентские устройства (компьютеры Windows, настольные системы), которые получают уведомления от WNS. |
| **клаудсервицеднс** | Это фильтры полного доменного имени (FQDN) для серверов WNS, с которыми будет взаимодействовать облачная служба для отправки нотификатиос в WNS. Они должны быть разрешены через брандмауэр, чтобы службы могли отправлять уведомления WNS.  Рекомендуется разрешить в список FQDN, а не диапазоны IP/VIP, так как они никогда не изменятся.|
| **клаудсервицеипс** | Клаудсервицеипс — IPv4-адреса серверов, используемых облачными службами для отправки уведомлений в WNS.  |


## <a name="microsoft-push-notifications-service-mpns-public-ip-ranges"></a>Диапазоны общедоступных IP-адресов Microsoft Push Notification Service (MPNS)
Если вы используете устаревшую службу уведомлений, то в центре загрузки будут доступны диапазоны IP-адресов, которые необходимо добавить в список разрешений. [Диапазоны общедоступных IP-адресов Microsoft Push Notification Service (MPNS)](https://www.microsoft.com/download/details.aspx?id=44535).


## <a name="related-topics"></a>См. также

* [Краткое руководство. Отправка push-уведомления](https://docs.microsoft.com/previous-versions/windows/apps/hh868252(v=win.10))
* [Как запросить, создать и сохранить канал уведомлений](https://docs.microsoft.com/previous-versions/windows/apps/hh465412(v=win.10))
* [Перехват уведомлений для запуска приложений](https://docs.microsoft.com/previous-versions/windows/apps/jj709907(v=win.10))
* [Проверка подлинности с помощью службы push-уведомлений Windows (WNS)](https://docs.microsoft.com/previous-versions/windows/apps/hh465407(v=win.10))
* [Заголовки запросов и ответов службы push-уведомлений](https://docs.microsoft.com/previous-versions/windows/apps/hh465435(v=win.10))
* [Рекомендации и контрольный список для push-уведомлений](https://docs.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)
 
