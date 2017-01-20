---
author: mcleanbyron
ms.assetid: 37F2C162-4910-4336-BEED-8536C88DCA65
description: "Используйте эти методы в API отправки Магазина Windows для управления тестовыми пакетами для приложений, которые зарегистрированы в вашей учетной записи Центра разработки для Windows."
title: "Управление тестовыми пакетами с помощью API отправки Магазина Windows"
translationtype: Human Translation
ms.sourcegitcommit: 020c8b3f4d9785842bbe127dd391d92af0962117
ms.openlocfilehash: 626a59ba848c9ae1d97b85b67ef2c76eed49065a

---

# <a name="manage-package-flights-using-the-windows-store-submission-api"></a>Управление тестовыми пакетами с помощью API отправки Магазина Windows

Используйте указанные ниже методы в API отправки Магазина Windows для управления тестовыми пакетами для ваших приложений. Введение в API отправки Магазина Windows, включая необходимые условия для использования этого API, см. в разделе [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md).

>**Примечание.**&nbsp;&nbsp;Эти методы могут применяться только для учетных записей Центра разработки для Windows, у которых имеется разрешение на использование API отправки Магазина Windows. Такое разрешение имеется не у всех учетных записей. Эти методы можно использовать только для получения, создания или удаления тестовых пакетов. Руководство по созданию отправок для тестовых пакетов см. в описании методов в разделе [Управление отправками тестовых пакетов](manage-flight-submissions.md).

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
* [Управление отправкой тестового пакета](manage-flight-submissions.md)



<!--HONumber=Dec16_HO3-->


