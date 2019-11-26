---
ms.assetid: B071F6BC-49D3-4E74-98EA-0461A1A55EFB
description: Если у вас имеется каталог приложений и надстроек, с помощью API коллекции Microsoft Store и API покупок в Microsoft Store вы можете получить доступ к информации о принадлежности этих продуктов из ваших служб.
title: Управление правами на продукты из службы
ms.date: 08/01/2018
ms.topic: article
keywords: windows 10, uwp, API коллекции Microsoft Store, API покупки Microsoft Store, просмотр продуктов, предоставление продуктов
ms.localizationpriority: medium
ms.openlocfilehash: 2d0df7780943717d8e01f0efcf1583efe05608af
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259220"
---
# <a name="manage-product-entitlements-from-a-service"></a>Управление правами на продукты из службы

Если у вас имеется каталог приложений и надстроек, с помощью *API коллекции Microsoft Store* и *API покупок в Microsoft Store* вы можете получить доступ к информации о принадлежности этих продуктов из ваших служб. *Право* представляет собой право пользователя использовать приложение или надстройку, опубликованные в Microsoft Store.

Эти API состоят из методов REST, предназначенных для использования разработчиками с каталогами надстроек, которые поддерживаются кроссплатформенными службами. Эти API позволяют выполнять следующие действия.

-   API коллекции Microsoft Store: [запрашивать продукты, принадлежащие пользователю](query-for-products.md) и [объявлять потребляемый продукт в качестве выполненного](report-consumable-products-as-fulfilled.md).
-   API покупок в Microsoft Store: [предоставлять бесплатный продукт пользователю](grant-free-products.md), [получать подписки для пользователя](get-subscriptions-for-a-user.md) и [изменять состояние выставления счета подписки для пользователя](change-the-billing-state-of-a-subscription-for-a-user.md).

> [!NOTE]
> Для получения доступа к информации о принадлежности API коллекции Microsoft Store и API покупок в Microsoft Store используют проверку подлинности Azure Active Directory (Azure AD). Для использования этих API у вас (или у вашей организации) должен быть каталог Azure AD, а также разрешение [глобального администратора](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) для этого каталога. Если вы уже используете Office 365 или другие бизнес-службы Майкрософт, то у вас уже есть Azure Active Directory.

## <a name="overview"></a>Обзор

Ниже описан от начала до конца процесс использования API коллекции и API покупки Microsoft Store:

1.  [Настройка приложения в Azure AD](#step-1).
2.  [СВЯЖИТЕ идентификатор приложения Azure AD с вашим приложением в центре партнеров](#step-2).
3.  В своей службе [создайте маркеры доступа Azure AD](#step-3), представляющие ваше удостоверение издателя.
4.  В клиентском приложении Windows [Создайте ключ идентификатора Microsoft Store](#step-4) , который представляет идентификатор текущего пользователя, и передайте этот ключ обратно в службу.
5.  После получения требуемого маркера доступа Azure AD и ключа идентификатора Microsoft Store [вызовите API коллекции Microsoft Store или API покупок в Microsoft Store из своей службы](#step-5).

Этот сквозной процесс включает два программных компонента, выполняющих различные задачи:

* **Ваша служба**. Это приложение, которое безопасно работает в контексте бизнес-среды и может быть реализовано с помощью любой выбранной платформы разработки. Ваша служба несет ответственность за создание маркеров доступа Azure AD, необходимых для сценария, а также на вызов URI-кодов RESTFUL для API сбора Microsoft Store и API покупки.
* **Ваше клиентское приложение Windows**. Это приложение, для которого требуется получить доступ к сведениям о правах доступа клиентов и управлять ими (включая надстройки для приложения). Это приложение отвечает за создание ключей идентификатора Microsoft Store, которые необходимы для вызова API коллекции Microsoft Store и API-интерфейса покупки из службы.

<span id="step-1"/>

## <a name="step-1-configure-an-application-in-azure-ad"></a>Шаг 1. Настройка приложения в Azure AD

Прежде чем можно будет использовать API сбора Microsoft Store или API покупки, необходимо создать веб-приложение Azure AD, получить идентификатор клиента и идентификатор приложения, а также создать ключ. Веб-приложение Azure AD представляет службу, из которой вы хотите вызвать API сбора Microsoft Store или API-интерфейс покупки. Вам потребуется идентификатор клиента, идентификатор приложения и ключ для создания маркеров доступа Azure AD, которые необходимы для вызова API.

> [!NOTE]
> Описанные в этом разделе задачи необходимо выполнить только один раз. После обновления манифеста приложения Azure AD и получения идентификатора клиента, идентификатора приложения и секрета клиента эти значения можно повторно использовать в любой момент, когда потребуется создать новый маркер доступа Azure AD.

1.  Если вы еще не сделали этого, следуйте инструкциям в статье [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) , чтобы зарегистрировать приложение **веб-приложения или API** в Azure AD.
    > [!NOTE]
    > При регистрации приложения необходимо выбрать **веб-приложение или API** в качестве типа приложения, чтобы можно было получить ключ (также называемый *секретом клиента*) для вашего приложения. В последнем шаге для вызова API коллекции Microsoft Store или API покупок в Microsoft Store необходимо предоставить секрет клиента при запросе маркера доступа от Azure AD.

2.  В [портал управления Azure](https://portal.azure.com/)перейдите к **Azure Active Directory**. Выберите каталог, щелкните **Регистрация приложений** в левой области навигации, а затем выберите свое приложение.
3.  Вы перейдете на главную страницу регистрации приложения. На этой странице скопируйте значение **идентификатора приложения** для дальнейшего использования.
4.  Создайте ключ, который потребуется позже (все это называется *секретом клиента*). В левой области щелкните **Параметры** , а затем — **ключи**. На этой странице выполните действия по [созданию ключа](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis). Скопируйте этот ключ для последующего использования.
5.  Добавьте в [манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)несколько обязательных URI аудитории. В левой области щелкните **Манифест**. Нажмите кнопку **изменить**, замените раздел `"identifierUris"` следующим текстом, а затем нажмите кнопку **сохранить**.

    ```json
    "identifierUris" : [                                
            "https://onestore.microsoft.com",
            "https://onestore.microsoft.com/b2b/keys/create/collections",
            "https://onestore.microsoft.com/b2b/keys/create/purchase"
        ],
    ```

    В этих строках указывается аудитория, на которую рассчитано ваше приложение. Далее вы создадите маркеры доступа Azure AD, связанные с каждым из этих значений аудитории.

<span id="step-2"/>

## <a name="step-2-associate-your-azure-ad-application-id-with-your-client-app-in-partner-center"></a>Шаг 2. Связывание идентификатора приложения Azure AD с вашим клиентским приложением в центре партнеров

Прежде чем использовать API сбора Microsoft Store или API покупки для настройки владельца и покупок приложения или надстройки, необходимо связать идентификатор приложения Azure AD с приложением (или приложением, содержащим надстройку) в центре партнеров.

> [!NOTE]
> Эту операцию необходимо выполнить только один раз.

1.  Войдите в [Центр партнеров](https://partner.microsoft.com/dashboard) и выберите свое приложение.
2.  Перейдите на страницу **службы** &gt; **коллекции продуктов и покупки** и введите идентификатор приложения Azure AD в одно из доступных полей **идентификатора клиента** .

<span id="step-3"/>

## <a name="step-3-create-azure-ad-access-tokens"></a>Шаг 3. Создание маркеров доступа Azure AD

Прежде чем вы извлекать ключ идентификатора Microsoft Store или вызывать API коллекции или API покупки Microsoft Store, ваша служба должна создать несколько разных маркеров доступа Azure AD, которые представляют ваше удостоверение издателя. Каждый маркер будет использоваться со своим API. Срок действия каждого токена составляет 60 минут. После истечения срока действия их можно обновить.

> [!IMPORTANT]
> Создайте маркеры доступа Azure AD только в контексте службы, а не в приложении. При отправке секрета клиента в приложение этот секрет может быть скомпрометирован.

<span id="access-tokens" />

### <a name="understanding-the-different-tokens-and-audience-uris"></a>О различных маркерах и URI аудиторий

В зависимости от того, какие методы вы хотите вызывать в API коллекции или API покупки Microsoft Store, вы должны создать два или три разных маркера. Каждый маркер доступа связан со своим URI аудитории (это те же URI, которые вы ранее добавили в раздел `"identifierUris"` манифеста приложения Azure AD).

  * Во всех случаях необходимо создать маркер с URI аудитории `https://onestore.microsoft.com`. На более позднем этапе вам предстоит передать этот маркер в заголовок **Authorization** методов в API коллекции или API покупки Microsoft Store.
      > [!IMPORTANT]
      > Используйте аудиторию `https://onestore.microsoft.com` только с маркерами доступа, которые хранятся безопасно в рамках службы. Использование маркеров доступа для этой аудитории за пределами вашей службы может сделать ее уязвимой для атак с повторением пакетов.

  * Если вы хотите вызывать метод в API коллекции Microsoft Store для [запроса продуктов, принадлежащих пользователю](query-for-products.md) или [объявления потребляемого продукта в качестве выполненного](report-consumable-products-as-fulfilled.md), необходимо также создать маркер с URI аудитории `https://onestore.microsoft.com/b2b/keys/create/collections`. На более позднем этапе вам предстоит передать этот маркер в клиентский метод в Windows SDK для запроса ключа идентификатора Microsoft Store, который вы сможете использовать с API коллекции Microsoft Store.

  * Если вы хотите вызвать метод в API для покупок в Microsoft Store, чтобы [предоставить бесплатный продукт пользователю](grant-free-products.md), [получить подписки для пользователя](get-subscriptions-for-a-user.md) или [изменить состояние выставления счета подписки для пользователя](change-the-billing-state-of-a-subscription-for-a-user.md), необходимо также создать маркер с `https://onestore.microsoft.com/b2b/keys/create/purchase`URI аудитории. На более позднем этапе вам предстоит передать этот маркер в клиентский метод в Windows SDK для запроса ключа идентификатора Microsoft Store, который вы сможете использовать с API покупки Microsoft Store.

<span />

### <a name="create-the-tokens"></a>Создание маркеров

Чтобы создать маркеры доступа, используйте в своей службе API OAuth 2.0, следуя инструкциям в разделе [Служба обслуживания вызовов с помощью учетных данных клиента](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) для отправки запроса HTTP POST в конечную точку ```https://login.microsoftonline.com/<tenant_id>/oauth2/token```. Ниже приведен пример запроса.

``` syntax
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource=https://onestore.microsoft.com
```

Для каждого токена укажите следующие параметры:

* Для параметров *клиент\_идентификатор* и *клиент\_секрета* укажите идентификатор приложения и секрет клиента для приложения, полученного из [портал управления Azure](https://portal.azure.com/). Оба эти параметра являются обязательными для создания маркера доступа с уровнем проверки подлинности, необходимым для API коллекции Microsoft Store или API покупки Microsoft Store.

* В качестве параметра *resource* укажите один из URI аудиторий, указанных в [предыдущем разделе](#access-tokens), в зависимости от типа создаваемого маркера доступа.

После истечения срока действия маркера доступа вы можете обновить его, следуя инструкциям, приведенным [здесь](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-code/#refreshing-the-access-tokens). Дополнительную информацию о структуре маркера доступа см. в статье [Поддерживаемые токены и типы утверждений](https://docs.microsoft.com/azure/active-directory/develop/id-tokens).

<span id="step-4"/>

## <a name="step-4-create-a-microsoft-store-id-key"></a>Шаг 4. Создание ключа идентификатора Microsoft Store

Прежде чем вы сможете вызвать какой-либо метод в API коллекции или API покупки Microsoft Store, ваше приложение должно создать ключ идентификатора Microsoft Store и отправить его службе. Этот ключ — это маркер JSON Web Token (JWT), который представляет удостоверение пользователя, доступ к информации о владении продуктами которого вы хотите получить. Подробнее об утверждениях в этом ключе см. в статье [Утверждения в ключе идентификатора Microsoft Store](#claims-in-a-microsoft-store-id-key).

В настоящее время единственным способом создания ключа идентификатора Microsoft Store является вызов API универсальной платформы Windows (UWP) из клиентского кода приложения. Созданный ключ представляет удостоверение пользователя, который в данный момент вошел в Microsoft Store на устройстве.

> [!NOTE]
> Каждый ключ идентификатора Microsoft Store действителен в течение 90 дней. После истечения срока действия ключа можно [обновить его](renew-a-windows-store-id-key.md). Рекомендуется обновлять ключи идентификаторов Microsoft Store, а не создавать новые.

<span />

### <a name="to-create-a-microsoft-store-id-key-for-the-microsoft-store-collection-api"></a>Создание ключа идентификатора Microsoft Store для API коллекции Microsoft Store

Выполните следующие действия, чтобы создать ключ идентификатора Microsoft Store, который можно использовать с API коллекции Microsoft Store для [запроса продуктов, принадлежащих пользователю](query-for-products.md) или [объявления потребляемого продукта в качестве выполненного](report-consumable-products-as-fulfilled.md).

1.  Передайте маркер доступа Azure AD, созданный с использованием значения URI `https://onestore.microsoft.com/b2b/keys/create/collections`, из своей службы в свое клиентское приложение. Это один из маркеров, которые вы создали [ранее на Шаге 3](#step-3).

2.  В коде приложения вызовите один из следующих методов, чтобы извлечь ключ идентификатора Microsoft Store:

  * Если в вашем приложении для управления покупками из приложения используется класс [StoreContext](https://docs.microsoft.com/uwp/api/Windows.Services.Store.StoreContext) из пространства имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store), используйте метод [StoreContext.GetCustomerCollectionsIdAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getcustomercollectionsidasync).

  * Если в вашем приложении для управления покупками из приложения используется класс [CurrentApp](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp) из пространства имен [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store), используйте метод [CurrentApp.GetCustomerCollectionsIdAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.getcustomercollectionsidasync).

    Передайте маркер доступа Azure AD в параметр *serviceTicket* метода. Если вы сохраняете идентификаторы анонимных пользователей в контексте служб, которыми вы управляете в качестве издателя текущего приложения, можно также передать идентификатор пользователя в параметр *публишерусерид* , чтобы связать текущего пользователя с новым ключом идентификатора Microsoft Store (идентификатор пользователя будет внедрен в ключ). В противном случае, если не нужно связывать идентификатор пользователя с ключом идентификатора Microsoft Store, можно передать любое строковое значение в параметр *публишерусерид* .

3.  После того как приложение успешно создало ключ идентификатора Microsoft Store, передайте этот ключ обратно своей службе.

<span />

### <a name="to-create-a-microsoft-store-id-key-for-the-microsoft-store-purchase-api"></a>Создание ключа идентификатора Microsoft Store для API покупки Microsoft Store

Выполните следующие действия, чтобы создать ключ идентификатора Microsoft Store, который можно использовать с API для покупок в Microsoft Store, чтобы [предоставить бесплатный продукт пользователю](grant-free-products.md), [получить подписки для пользователя](get-subscriptions-for-a-user.md) или [изменить состояние выставления счета подписки для пользователя](change-the-billing-state-of-a-subscription-for-a-user.md).

1.  Передайте маркер доступа Azure AD, созданный с использованием значения URI `https://onestore.microsoft.com/b2b/keys/create/purchase`, из своей службы в свое клиентское приложение. Это один из маркеров, которые вы создали [ранее на Шаге 3](#step-3).

2.  В коде приложения вызовите один из следующих методов, чтобы извлечь ключ идентификатора Microsoft Store:

  * Если в вашем приложении для управления покупками из приложения используется класс [StoreContext](https://docs.microsoft.com/uwp/api/Windows.Services.Store.StoreContext) из пространства имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store), используйте метод [StoreContext.GetCustomerPurchaseIdAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getcustomerpurchaseidasync).

  * Если в вашем приложении для управления покупками из приложения используется класс [CurrentApp](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp) из пространства имен [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store), используйте метод [CurrentApp.GetCustomerPurchaseIdAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.getcustomerpurchaseidasync).

    Передайте маркер доступа Azure AD в параметр *serviceTicket* метода. Если вы сохраняете идентификаторы анонимных пользователей в контексте служб, которыми вы управляете в качестве издателя текущего приложения, можно также передать идентификатор пользователя в параметр *публишерусерид* , чтобы связать текущего пользователя с новым ключом идентификатора Microsoft Store (идентификатор пользователя будет внедрен в ключ). В противном случае, если не нужно связывать идентификатор пользователя с ключом идентификатора Microsoft Store, можно передать любое строковое значение в параметр *публишерусерид* .

3.  После того как приложение успешно создало ключ идентификатора Microsoft Store, передайте этот ключ обратно своей службе.

### <a name="diagram"></a>Схема

На следующей схеме показан процесс создания ключа идентификатора Microsoft Store.

  ![Создать ключ идентификатора магазина Windows](images/b2b-1.png)

<span id="step-5"/>

## <a name="step-5-call-the-microsoft-store-collection-api-or-purchase-api-from-your-service"></a>Шаг 5. Вызов API коллекции или API покупки в Microsoft Store из службы.

Как только у вашей службы есть ключ идентификатора Microsoft Store, который позволяет ей получать доступ к информации о владении продуктами определенным пользователем, ваша служба может вызывать API коллекции Microsoft Store или API покупки Microsoft Store. Следуйте инструкциям ниже:

* [Запрос продуктов](query-for-products.md)
* [Сообщать об использовании продукты как выполненные](report-consumable-products-as-fulfilled.md)
* [Предоставление бесплатных продуктов](grant-free-products.md)
* [Получение подписок для пользователя](get-subscriptions-for-a-user.md)
* [Изменение состояния выставления счетов для подписки пользователя](change-the-billing-state-of-a-subscription-for-a-user.md)

Для каждого сценария передайте API-интерфейсу следующие данные.

-   В заголовке запроса передайте маркер доступа Azure AD со значением URI аудитории `https://onestore.microsoft.com`. Это один из маркеров, которые вы создали [ранее на Шаге 3](#step-3). Этот маркер представляет ваше удостоверение издателя.
-   В теле запроса передайте ключ идентификатора Microsoft Store, полученный [ранее в Шаге 4](#step-4) из клиентского кода вашего приложения. Этот ключ представляет удостоверение пользователя – владельца продукта, доступ к данным о котором вы хотите получить.

### <a name="diagram"></a>Схема

На следующей схеме описывается процесс вызова метода в API Microsoft Store сбора данных или в API-интерфейсе покупки из службы.

  ![Коллекции вызовов или API приобретения](images/b2b-2.png)

## <a name="claims-in-a-microsoft-store-id-key"></a>Утверждения в ключе идентификатора Microsoft Store

Ключ идентификатора Microsoft Store — это веб-маркер JSON (JWT), который представляет удостоверение пользователя — владельца продукта, доступ к данным о котором вы хотите получить. После расшифровки в Base64 ключ идентификатора Microsoft Store содержит следующие утверждения.

* `iat`:&nbsp;&nbsp;&nbsp;определяет время выдачи ключа. Это утверждение можно использовать для определения возраста маркера. Это значение выражается как время эпохи.
* `iss`:&nbsp;&nbsp;&nbsp;идентифицирует издателя. Имеет такое же значение, что и утверждение `aud`.
* `aud`:&nbsp;&nbsp;&nbsp;идентифицирует аудиторию. Должно иметь одно из следующих значений: `https://collections.mp.microsoft.com/v6.0/keys` или `https://purchase.mp.microsoft.com/v6.0/keys`.
* `exp`:&nbsp;&nbsp;&nbsp;определяет срок действия, по истечении которого ключ больше не будет приниматься для обработки, кроме продления ключей. Значение этого утверждения выражается как время эпохи.
* `nbf`:&nbsp;&nbsp;&nbsp;определяет время, когда маркер будет принят для обработки. Значение этого утверждения выражается как время эпохи.
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/clientId`:&nbsp;&nbsp;&nbsp;идентификатор клиента, определяющий разработчика.
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/payload`:&nbsp;&nbsp;&nbsp;непрозрачные полезные данные (зашифрованные и Base64-кодированные), которые содержат сведения, предназначенные только для использования службами Microsoft Store Services.
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/userId`:&nbsp;&nbsp;&nbsp;идентификатор пользователя, определяющий текущего пользователя в контексте служб. Это то же значение, которое вы передаете в необязательный параметр *publisherUserId*[метода, который используется для создания ключа](#step-4).
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/refreshUri`:&nbsp;&nbsp;&nbsp;URI, который можно использовать для обновления ключа.

Вот пример расшифрованного заголовка ключа идентификатора Microsoft Store.

```json
{
    "typ":"JWT",
    "alg":"RS256",
    "x5t":"agA_pgJ7Twx_Ex2_rEeQ2o5fZ5g"
}
```

Вот пример расшифрованного набора утверждений ключа идентификатора Microsoft Store.

```json
{
    "http://schemas.microsoft.com/marketplace/2015/08/claims/key/clientId": "1d5773695a3b44928227393bfef1e13d",
    "http://schemas.microsoft.com/marketplace/2015/08/claims/key/payload": "ZdcOq0/N2rjytCRzCHSqnfczv3f0343wfSydx7hghfu0snWzMqyoAGy5DSJ5rMSsKoQFAccs1iNlwlGrX+/eIwh/VlUhLrncyP8c18mNAzAGK+lTAd2oiMQWRRAZxPwGrJrwiq2fTq5NOVDnQS9Za6/GdRjeiQrv6c0x+WNKxSQ7LV/uH1x+IEhYVtDu53GiXIwekltwaV6EkQGphYy7tbNsW2GqxgcoLLMUVOsQjI+FYBA3MdQpalV/aFN4UrJDkMWJBnmz3vrxBNGEApLWTS4Bd3cMswXsV9m+VhOEfnv+6PrL2jq8OZFoF3FUUpY8Fet2DfFr6xjZs3CBS1095J2yyNFWKBZxAXXNjn+zkvqqiVRjjkjNajhuaNKJk4MGHfk2rZiMy/aosyaEpCyncdisHVSx/S4JwIuxTnfnlY24vS0OXy7mFiZjjB8qL03cLsBXM4utCyXSIggb90GAx0+EFlVoJD7+ZKlm1M90xO/QSMDlrzFyuqcXXDBOnt7rPynPTrOZLVF+ODI5HhWEqArkVnc5MYnrZD06YEwClmTDkHQcxCvU+XUEvTbEk69qR2sfnuXV4cJRRWseUTfYoGyuxkQ2eWAAI1BXGxYECIaAnWF0W6ThweL5ZZDdadW9Ug5U3fZd4WxiDlB/EZ3aTy8kYXTW4Uo0adTkCmdLibw=",
    "http://schemas.microsoft.com/marketplace/2015/08/claims/key/userId": "infusQMLaYCrgtC0d/SZWoPB4FqLEwHXgZFuMJ6TuTY=",
    "http://schemas.microsoft.com/marketplace/2015/08/claims/key/refreshUri": "https://collections.mp.microsoft.com/v6.0/b2b/keys/renew",
    "iat": 1442395542,
    "iss": "https://collections.mp.microsoft.com/v6.0/keys",
    "aud": "https://collections.mp.microsoft.com/v6.0/keys",
    "exp": 1450171541,
    "nbf": 1442391941
}
```

## <a name="related-topics"></a>См. также

* [Запрос продуктов](query-for-products.md)
* [Сообщать об использовании продукты как выполненные](report-consumable-products-as-fulfilled.md)
* [Предоставление бесплатных продуктов](grant-free-products.md)
* [Получение подписок для пользователя](get-subscriptions-for-a-user.md)
* [Изменение состояния выставления счетов для подписки пользователя](change-the-billing-state-of-a-subscription-for-a-user.md)
* [Обновление ключа идентификатора Microsoft Store](renew-a-windows-store-id-key.md)
* [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
* [Основные сведения о манифесте приложения Azure Active Directory]( https://go.microsoft.com/fwlink/?LinkId=722500)
* [Поддерживаемые типы токенов и утверждений](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
