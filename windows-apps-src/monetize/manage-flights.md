---
ms.assetid: 37F2C162-4910-4336-BEED-8536C88DCA65
description: Эти методы можно используете в интерфейсе API отправки Microsoft Store для управления авиарейсов пакета для приложений, зарегистрированных для учетной записи центра партнеров.
title: Управление тестовыми пакетами
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, тестируемые возможности
ms.localizationpriority: medium
ms.openlocfilehash: 8678ee4d73f13e241a2c72d6dac532289af13ced
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57601249"
---
# <a name="manage-package-flights"></a>Управление тестовыми пакетами

Используйте указанные ниже методы в API отправки в Microsoft Store для управления тестовыми пакетами для ваших приложений. Введение в API отправки в Microsoft Store, включая необходимые условия для использования этого API, см. в разделе [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md).

Эти методы можно использовать только для получения, создания или удаления тестовых пакетов. Руководство по созданию отправок для тестовых пакетов см. в описании методов в разделе [Управление отправками тестовых пакетов](manage-flight-submissions.md).

<table>
<colgroup>
<col width="10%" />
<col width="30%" />
<col width="60%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Метод</th>
<th align="left">URI</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">GET</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}</td>
<td align="left"><a href="get-a-flight.md">Получить пакет рейса</a></td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights</td>
<td align="left"><a href="create-a-flight.md">Создание пакета рейса</a></td>
</tr>
<tr>
<td align="left">DELETE</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}</td>
<td align="left"><a href="delete-a-flight.md">Удаление пакета рейса</a></td>
</tr>
</tbody>
</table>

## <a name="prerequisites"></a>Предварительные условия

Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store, прежде чем использовать любой из этих методов.

## <a name="related-topics"></a>Статьи по теме

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправкой пакета рейсов](manage-flight-submissions.md)
