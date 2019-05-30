---
author: mijacobs
Description: На многих предприятиях использовать брандмауэры для блокировки нежелательного трафика. Этот документ описывает способ разрешить WNS прохождение трафика через брандмауэры.
title: Добавление WNS трафика в список разрешенных брандмауэра
ms.assetid: 2125B09F-DB90-4515-9AA6-516C7E9ACCCD
template: detail.hbs
ms.author: mijacobs
ms.date: 05/20/2019
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, WNS, службу уведомлений windows, уведомления, windows, брандмауэра, устранение неполадок, IP-адрес, трафик, enterprise, сеть, IPv4, виртуальный IP-адрес, полное доменное имя, общедоступный IP-адрес
ms.localizationpriority: medium
ms.openlocfilehash: 9ed4ad6ed828abda9d487ef96beca9b655c92421
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66366669"
---
# <a name="allowing-windows-notification-traffic-through-enterprise-firewalls"></a>Разрешения Windows уведомления трафика через брандмауэры предприятий

## <a name="background"></a>Фон
На многих предприятиях использование брандмауэров для блокировки нежелательного трафика сети; к сожалению это может также блокировать важных моментов, таких как взаимодействие со службой уведомлений Windows. Это означает, что все уведомления, отправленный с помощью WNS, будут удалены. Чтобы избежать этого, сетевых администраторов можно добавить в список утвержденных WNS каналов, их список исключений, чтобы разрешить трафик может проходить через брандмауэр, WNS. Ниже приведены дополнительные сведения о том, как и что нужно добавить. 


## <a name="what-information-should-be-added-to-the-allowlist"></a>Какие сведения должны добавляться в список разрешенных
Ниже список, содержащий полные доменные имена, виртуальные IP-адреса и IP-адрес диапазоны, используемые службой уведомлений Windows. 

> [!IMPORTANT]
> Настоятельно рекомендуется разрешить список по полному доменному ИМЕНИ, поскольку они не изменится. Если вы разрешите списка по полному доменному ИМЕНИ, не необходимо также разрешить диапазоны IP-адресов.

> [!IMPORTANT]
> Диапазоны IP-адресов приведет к изменению периодически; по этой причине они не включаются на этой странице. Если вы хотите просмотреть список диапазонов IP-адресов, можно загрузить файл из центра загрузки: [Служба уведомлений Windows (WNS), виртуальный IP-адрес и IP-диапазонов](https://www.microsoft.com/download/details.aspx?id=44238). Проверьте регулярно, чтобы убедитесь, что у вас есть актуальная информация. 


### <a name="fqdns-vips-and-ips"></a>Полные доменные имена, виртуальные IP-адреса и IP-адресов
Каждый элемент в следующем XML-документе описан в таблице, что следующий за ней (в [условия и нотации](#terms-and-notations). Диапазоны IP-адресов были специально оставлены из этого документа, чтобы рекомендуется использовать только полные доменные имена, так как остается постоянным для полных доменных имен. Тем не менее можно загрузить XML-файл, содержащий полный список, из центра загрузки: [Служба уведомлений Windows (WNS), виртуальный IP-адрес и IP-диапазонов](https://www.microsoft.com/download/details.aspx?id=44238). Новые виртуальные IP-адреса или IP-диапазоны адресов будут быть **действующие одну неделю после загрузки**.

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

### <a name="terms-and-notations"></a>Условия и нотации
Ниже приведены объяснения в нотации и элементы, используемые в приведенном выше фрагменте кода XML.

| Термин | Объяснение |
|---|---|
| **Точечно десятичной нотации (т. е. 64.4.28.0/26)** | Точечно десятичной нотации способ для описания диапазона IP-адресов. Например 64.4.28.0/26 означает, что первые 26 бита 64.4.28.0 постоянны, хотя последние 6 разрядов переменной.  В этом случае диапазон адресов IPv4 является 64.4.28.0 - 64.4.28.63. |
| **ClientDNS** | Это полное доменное имя (FQDN) фильтры для клиентских устройств (ПК Windows, настольных компьютеров) получение уведомлений с WNS. Они должны быть включены в брандмауэре, чтобы WNS клиентов для использования WNS.  Ее рекомендуется разрешить список полных доменных имен вместо диапазоны IP-адрес или VIP, так как они никогда не изменится. |
| **ClientIPsIPv4** | Это IPv4-адреса серверов доступа с устройств клиента (для компьютеров Windows, настольных компьютеров) получение уведомлений с WNS. |
| **CloudServiceDNS** | Это полное доменное имя (FQDN) фильтры для WNS серверам, которыми будет взаимодействовать облачной службы для отправки notificatios WNS. Их необходимо разрешить через брандмауэр службы для отправки уведомлений WNS.  Ее рекомендуется разрешить список полных доменных имен вместо диапазоны IP-адрес или VIP, так как они никогда не изменится.|
| **CloudServiceIPs** | CloudServiceIPs являются IPv4-адреса серверов, которые используются для облачных служб для отправки уведомлений в WNS  |


## <a name="microsoft-push-notifications-service-mpns-public-ip-ranges"></a>Диапазоны общедоступных IP-адрес службы Push-уведомлений Майкрософт (MPNS)
Если вы используете службу устаревшие уведомления MPNS, необходимо будет добавить в список разрешений диапазоны IP-адресов доступны из центра загрузки: [Общедоступный IP-адрес службы Push-уведомлений Майкрософт (MPNS) диапазонов адресов](https://www.microsoft.com/download/details.aspx?id=44535).


## <a name="related-topics"></a>См. также

* [Краткое руководство. Отправка Push-уведомление](https://docs.microsoft.com/previous-versions/windows/apps/hh868252(v=win.10))
* [Запрос, создание и сохранение канала уведомлений с помощью](https://docs.microsoft.com/previous-versions/windows/apps/hh465412(v=win.10))
* [Как перехватить уведомления для запуска приложений](https://docs.microsoft.com/previous-versions/windows/apps/jj709907(v=win.10))
* [Способ проверки подлинности с помощью Windows Push Notification Service (WNS)](https://docs.microsoft.com/previous-versions/windows/apps/hh465407(v=win.10))
* [Push-уведомления службы: заголовки запросов и ответов](https://docs.microsoft.com/previous-versions/windows/apps/hh465435(v=win.10))
* [Контрольный список для Push-уведомлений](https://docs.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)
 
