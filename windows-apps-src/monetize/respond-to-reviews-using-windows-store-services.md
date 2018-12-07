---
ms.assetid: c92c0ea8-f742-4fc1-a3d7-e90aac11953e
description: Используйте API отзывов Microsoft Store, чтобы отправлять ответы на отзывы о вашем приложении в Store программным способом.
title: Ответ на отзывы с помощью служб Магазина
ms.date: 06/04/2018
ms.topic: article
keywords: Windows 10, UWP, API отзывов Microsoft Store, ответ на отзывы
ms.localizationpriority: medium
ms.openlocfilehash: 95de2cc1de1b71a435fc8d4388f599c417132814
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8795552"
---
# <a name="respond-to-reviews-using-store-services"></a>Ответ на отзывы с помощью служб Магазина

Используйте *API отзывов Microsoft Store*, чтобы отвечать на отзывы о вашем приложении в Store программным способом. Этот API особенно полезен для разработчиков, которые хотят массово ответить на множество отзывов без использования центра партнеров. Для проверки подлинности вызовов из приложения или службы в этом интерфейсе используется служба Azure Active Directory (Azure AD).

Далее описан весь процесс.

1.  Убедитесь, что вы выполнили все [необходимые условия](#prerequisites).
2.  Перед вызовом метода в API отзывов Microsoft Store [получите маркер доступа Azure AD](#obtain-an-azure-ad-access-token). После получения маркера доступа у вас будет 60минут, чтобы использовать его в вызовах к API отзывов Microsoft Store до окончания срока действия маркера. После истечения срока действия маркера можно сформировать новый маркер.
3.  [Вызов интерфейса API отзывов Microsoft Store](#call-the-windows-store-reviews-api).

> [!NOTE]
> Помимо использования API для программного ответа на отзывы, вы можете также отвечать на отзывы [с помощью центра партнеров](../publish/respond-to-customer-reviews.md)отзывов в Microsoft Store.

<span id="prerequisites" />

## <a name="step-1-complete-prerequisites-for-using-the-microsoft-store-reviews-api"></a>Шаг 1. Выполнение необходимых условий для использования API отзывов Microsoft Store

Перед тем как начать писать код для вызова API отзывов Microsoft Store, убедитесь, что вы выполнили следующие необходимые условия.

* У вас (или у вашей организации) должен быть каталог Azure AD, а также разрешение [глобального администратора](http://go.microsoft.com/fwlink/?LinkId=746654) для этого каталога. Если вы уже используете Office 365 или другие бизнес-службы Майкрософт, то у вас уже есть Azure Active Directory. В противном случае можно [Создать новую Azure AD в центре партнеров](../publish/associate-azure-ad-with-partner-center.md#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) без дополнительной платы.

* Необходимо привязать приложение Azure AD к учетной записи центра партнеров, получить идентификатор владельца и идентификатор клиента для приложения и создать ключ. Приложение Azure AD представляет собой приложение или службу, из которой отправляются вызовы в интерфейс API отзывов Microsoft Store. Чтобы получить маркер доступа Azure AD, который вы передадите в API, необходимо иметь в наличии идентификатор владельца, идентификатор клиента и ключ.
    > [!NOTE]
    > Эту операцию необходимо выполнить только один раз. После того как вы получите идентификатор владельца, идентификатор клиента и ключ, их можно будет использовать повторно в любое время для создания нового маркера доступа Azure AD.

Чтобы привязать приложение Azure AD к учетной записи центра партнеров и получить необходимые значения:

1.  В центре партнеров, [связать учетную запись центра партнеров вашей организации в каталог Azure AD вашей организации](../publish/associate-azure-ad-with-partner-center.md).

2.  Затем на странице **пользователей** в разделе **Параметры учетной записи** центра партнеров, [добавьте приложение Azure AD](../publish/add-users-groups-and-azure-ad-applications.md#add-azure-ad-applications-to-your-partner-center-account) , представляющее собой приложение или службу, которые вы будете использовать для ответа на отзывы. Обязательно назначьте этому приложению роль **Менеджер**. Если приложение еще не существует в каталоге Azure AD, вы можете [Создать новое приложение Azure AD в центре партнеров](../publish/add-users-groups-and-azure-ad-applications.md#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account). 

3.  Вернитесь на страницу **Пользователи**, щелкните имя приложения Azure AD, чтобы перейти к параметрам приложения, и скопируйте значения **идентификатора владельца** и **идентификатора клиента**.

4. Нажмите кнопку **Добавить новый ключ**. На следующем экране скопируйте **ключ**. Вы не сможете получить доступ к этой информации после закрытия страницы. Дополнительные сведения см. в разделе [Управление ключами для приложения Azure AD](../publish/add-users-groups-and-azure-ad-applications.md#manage-keys).

<span id="obtain-an-azure-ad-access-token" />

## <a name="step-2-obtain-an-azure-ad-access-token"></a>Шаг 2. Получение маркера доступа Azure AD

Перед тем как можно будет вызвать любой из методов в API отзывов Microsoft Store, сначала необходимо получить маркер доступа Azure AD и передать его в заголовок **Авторизация** каждого метода в API. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера вы можете обновить его, чтобы дальше использовать в последующих вызовах к API.

Для получения маркера доступа следуйте инструкциям в разделе [Вызовы между службами с помощью учетных данных клиентов](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/), чтобы отправить HTTP-запрос POST в конечную точку ```https://login.microsoftonline.com/<tenant_id>/oauth2/token```. Ниже приведен пример запроса.

```syntax
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource=https://manage.devcenter.microsoft.com
```

Значение *tenant\_id* в POST URI и параметров, *client\_id* и *client\_secret* укажите идентификатор владельца, идентификатор клиента и ключ для вашего приложения, которые вы получили из центра партнеров в предыдущем разделе. Для параметра *resource* укажите ```https://manage.devcenter.microsoft.com```.

После истечения срока действия маркера доступа вы можете обновить его, следуя инструкциям, приведенным [здесь](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-code/#refreshing-the-access-tokens).

<span id="call-the-windows-store-reviews-api" />

## <a name="step-3-call-the-microsoft-store-reviews-api"></a>Шаг 3. Вызов интерфейса API отзывов Microsoft Store

После получения маркера доступа Azure AD вы можете вызвать API отзывов Microsoft Store. Необходимо передать маркер доступа в заголовок **Authorization** каждого метода.

API отзывов Microsoft Store содержит несколько методов, которые можно использовать, чтобы определить, разрешено ли вам отвечать на данный отзыв и отправлять ответы на один или несколько отзывов. Используйте следующий процесс, чтобы использовать этот API.

1. Получите идентификаторы отзывов, на которые вы хотите ответить. Идентификаторы отзывов доступны в данных ответов метода [получения отзывов о приложении](get-app-reviews.md) в API аналитики для Microsoft Store и в [автономном](../publish/download-analytic-reports.md) [отчете об отзывах](../publish/reviews-report.md).
2. Вызовите [метод получения сведений об отзывах о приложении](get-response-info-for-app-reviews.md), чтобы определить, разрешено ли вам отвечать на отзывы. Когда пользователи отправляют отзывы, они могут отказаться от получения ответов на них. Невозможно ответить на отзывы от пользователей, которые отказались получать ответы на отзывы.
3. Вызовите метод [отправки ответов на отзывы о приложении](submit-responses-to-app-reviews.md), чтобы программно ответить на отзывы.


## <a name="related-topics"></a>Статьи по теме

* [Получение отзывов о приложении](get-app-reviews.md)
* [Получение сведений об ответах на отзывы о приложении](get-response-info-for-app-reviews.md)
* [Отправка ответов на отзывы о приложении](submit-responses-to-app-reviews.md)

 
