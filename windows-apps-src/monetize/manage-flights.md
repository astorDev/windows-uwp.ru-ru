---
author: Xansky
ms.assetid: 37F2C162-4910-4336-BEED-8536C88DCA65
description: Используйте эти методы в API отправки в Microsoft Store для управления тестовыми пакетами для приложений, которые зарегистрированы в вашей учетной записи Центра разработки для Windows.
title: Управление тестовыми пакетами
ms.author: mhopkins
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, тестируемые возможности
ms.localizationpriority: medium
ms.openlocfilehash: 41cf0d224dfca4d11bbd1e3fde7da44c5201a601
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "5757674"
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
<td align="left"><a href="get-a-flight.md">Получение тестового пакета</a></td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights</td>
<td align="left"><a href="create-a-flight.md">Создание тестового пакета</a></td>
</tr>
<tr>
<td align="left">DELETE</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}</td>
<td align="left"><a href="delete-a-flight.md">Удаление тестового пакета</a></td>
</tr>
</tbody>
</table>

## <a name="prerequisites"></a>Необходимые условия

Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store, прежде чем использовать любой из этих методов.

## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправками тестового пакета](manage-flight-submissions.md)
