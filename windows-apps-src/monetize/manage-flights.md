---
author: mcleanbyron
ms.assetid: 37F2C162-4910-4336-BEED-8536C88DCA65
description: "Используйте эти методы в API отправки Магазина Windows для управления тестовыми пакетами для приложений, которые зарегистрированы в вашей учетной записи Центра разработки для Windows."
title: "Управление тестовыми пакетами"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, API отправки Магазина Windows, тестируемые возможности"
ms.openlocfilehash: b560c2c12dc2fd7984287d039d20c121698aef85
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="manage-package-flights"></a>Управление тестовыми пакетами

Используйте указанные ниже методы в API отправки Магазина Windows для управления тестовыми пакетами для ваших приложений. Введение в API отправки Магазина Windows, включая необходимые условия для использования этого API, см. в разделе [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md).

>**Примечание.**&nbsp;&nbsp;Эти методы могут применяться только для учетных записей Центра разработки для Windows, у которых имеется разрешение на использование API отправки Магазина Windows. Это разрешение предоставляется учетным записям разработчиков поэтапно. В настоящий момент это разрешение предоставлено не всем учетным записям. Чтобы запросить ранний доступ, войдите в панель мониторинга Центра разработки, щелкните **Обратная связь** в нижней части панели, выберите пункт **API отправки** в разделе обратной связи и отправьте запрос. Когда для вашей учетной записи будет предоставлено разрешение, вы получите уведомление по электронной почте.

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
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}```</td>
<td align="left">[Получение тестового пакета](get-a-flight.md)</td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights```</td>
<td align="left">[Создание тестового пакета](create-a-flight.md)</td>
</tr>
<tr>
<td align="left">DELETE</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}```</td>
<td align="left">[Удаление тестового пакета](delete-a-flight.md)</td>
</tr>
</tbody>
</table>

## <a name="prerequisites"></a>Необходимые условия

Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows, прежде чем использовать любой из этих методов.

## <a name="related-topics"></a>Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправками тестового пакета](manage-flight-submissions.md)
