---
author: mcleanbyron
ms.assetid: B071F6BC-49D3-4E74-98EA-0461A1A55EFB
description: Если у вас имеется каталог приложений и надстроек, с помощью API коллекции Microsoft Store и API покупок в Microsoft Store вы можете получить доступ к информации о принадлежности этих продуктов из ваших служб.
title: Управление правами на продукты из службы
ms.author: mcleans
ms.date: 08/01/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, API коллекции Microsoft Store, API покупки Microsoft Store, просмотр продуктов, предоставление продуктов
ms.localizationpriority: medium
ms.openlocfilehash: 3a0766830bc2110dffcf5baf886e8ccb98ac6446
ms.sourcegitcommit: 232543fba1fb30bb1489b053310ed6bd4b8f15d5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "4178243"
---
# <a name="manage-product-entitlements-from-a-service"></a>Управление правами на продукты из службы

Если у вас имеется каталог приложений и надстроек, с помощью *API коллекции Microsoft Store* и *API покупок в Microsoft Store* вы можете получить доступ к информации о принадлежности этих продуктов из ваших служб. *Право* представляет собой право пользователя использовать приложение или надстройку, опубликованные в Microsoft Store.

Эти API состоят из методов REST, предназначенных для использования разработчиками с каталогами надстроек, которые поддерживаются кроссплатформенными службами. Эти API позволяют выполнять следующие действия.

-   API коллекции Microsoft Store: [запрашивать продукты, принадлежащие пользователю](query-for-products.md) и [объявлять потребляемый продукт в качестве выполненного](report-consumable-products-as-fulfilled.md).
-   API покупок в Microsoft Store: [предоставлять бесплатный продукт пользователю](grant-free-products.md), [получать подписки для пользователя](get-subscriptions-for-a-user.md) и [изменять состояние выставления счета подписки для пользователя](change-the-billing-state-of-a-subscription-for-a-user.md).

> [!NOTE]
> Для получения доступа к информации о принадлежности API коллекции Microsoft Store и API покупок в Microsoft Store используют проверку подлинности Azure Active Directory (Azure AD). Для использования этих API у вас (или у вашей организации) должен быть каталог Azure AD, а также разрешение [глобального администратора](http://go.microsoft.com/fwlink/?LinkId=746654) для этого каталога. Если вы уже используете Office 365 или другие бизнес-службы Майкрософт, то у вас уже есть катало Azure AD.

## <a name="overview"></a>Обзор

Ниже описан от начала до конца процесс использования API коллекции и API покупки Microsoft Store:

1.  [Настройка приложения в Azure AD](#step-1).
2.  [Связать свой идентификатор приложения Azure AD с приложением на информационной панели центра разработки для Windows](#step-2).
3.  В своей службе [создайте маркеры доступа Azure AD](#step-3), представляющие ваше удостоверение издателя.
4.  В свое клиентское приложение Windows, [Создание ключа идентификатора Microsoft Store](#step-4) , представляющий удостоверение текущего пользователя, и передайте этот ключ обратно службе.
5.  После получения требуемого маркера доступа Azure AD и ключа идентификатора Microsoft Store [вызовите API коллекции Microsoft Store или API покупок в Microsoft Store из своей службы](#step-5).

Этот процесс начала до конца включает два программных компонентов, которые выполнять разные задачи:

* **Ваша служба**. Это приложение, которое безопасно выполняется в контексте среды бизнеса, и он может осуществляться с помощью любого платформы разработки, который вы выбираете. Ваша служба отвечает, для создания маркера доступа Azure AD требуется для сценария, а также для вызова REST идентификаторы URI для коллекции Microsoft Store, API и API покупок.
* **Windows свое клиентское приложение**. Это приложение, для которого требуется получить доступ к информации и управление клиентом права (включая надстройки для приложения). Это приложение отвечает за создание ключей идентификатора Microsoft Store, вам нужно вызывать API коллекции Microsoft Store и API покупки из службы.

<span id="step-1"/>

## <a name="step-1-configure-an-application-in-azure-ad"></a>Шаг 1: Настройка приложения в Azure AD

Прежде чем использовать API коллекции Microsoft Store или API покупки, необходимо создать Azure AD веб-приложения, извлечь идентификатор владельца и идентификатор приложения для приложения и создать ключ. Azure AD веб-приложение представляет службу, из которой вы хотите вызывать API коллекции Microsoft Store или API покупки. Требуется идентификатор владельца, идентификатор и ключ для создания маркера доступа Azure AD, необходимо вызвать API.

> [!NOTE]
> Описанные в этом разделе задачи необходимо выполнить только один раз. После того как вы обновите манифест приложения Azure AD и у вас есть идентификатор владельца, идентификатор и клиент секрет приложения, вы можете повторно использовать эти значения любое время, вам необходимо создать новый маркер доступа Azure AD.

1.  Если вы этого еще не сделали, следуйте инструкциям в [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) для регистрации **веб-приложения или API** приложений с Azure AD.
    > [!NOTE]
    > Когда вы регистрируете вашего приложения, необходимо выбрать **веб-приложения или API** вводе приложения таким образом, чтобы вы могли извлечь ключ (также называемых *секрета клиента*) для вашего приложения. В последнем шаге для вызова API коллекции Microsoft Store или API покупок в Microsoft Store необходимо предоставить секрет клиента при запросе маркера доступа от Azure AD.

2.  На [Портале управления Azure](https://portal.azure.com/)перейдите к **Azure Active Directory**. Выберите каталог, щелкните **Регистрация приложений** в левой области навигации и затем выберите приложение.
3.  Будет выполнен переход к странице регистрации основного приложения. На этой странице скопируйте значение **Идентификатора приложения** для использования в дальнейшем.
4.  Создайте ключ, который необходимо будет более поздней версии (это все называется *секрета клиента*). На левой панели выберите **Параметры** , а затем **ключи**. На этой странице выполните действия, чтобы [Создать ключ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis). Скопируйте этот ключ для последующего использования.
5.  Добавление нескольких требуется аудиторий, ваш [манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest). На левой панели щелкните **манифест**. Нажмите кнопку **Изменить**, замените `"identifierUris"` раздела следующим текстом и нажмите кнопку **Сохранить**.

    ```json
    "identifierUris" : [                                
            "https://onestore.microsoft.com",
            "https://onestore.microsoft.com/b2b/keys/create/collections",
            "https://onestore.microsoft.com/b2b/keys/create/purchase"
        ],
    ```

    В этих строках указывается аудитория, на которую рассчитано ваше приложение. Далее вы создадите маркеры доступа Azure AD, связанные с каждым из этих значений аудитории.

<span id="step-2"/>

## <a name="step-2-associate-your-azure-ad-application-id-with-your-client-app-in-windows-dev-center"></a>Шаг 2: Привязка идентификатор приложения Azure AD с приложением клиента в центре разработки для Windows

Прежде чем использовать API коллекции Microsoft Store или API покупки для настройки право собственности и покупок из приложения или надстройки, вы должны связать свой идентификатор приложения Azure AD с приложением (или приложения, которое содержит надстройку) на информационной панели центра разработки.

> [!NOTE]
> Эту операцию необходимо выполнить только один раз.

1.  Выполните вход в [информационную панель Центра разработки](https://dev.windows.com/overview) и выберите свое приложение.
2.  Перейдите к **службам** &gt; **коллекции и покупки продуктов** странице и введите идентификатор приложения Azure AD в одно из доступных полей **Идентификатор клиента** .

<span id="step-3"/>

## <a name="step-3-create-azure-ad-access-tokens"></a>Шаг 3. Создание маркеров доступа Azure AD

Прежде чем вы извлекать ключ идентификатора Microsoft Store или вызывать API коллекции или API покупки Microsoft Store, ваша служба должна создать несколько разных маркеров доступа Azure AD, которые представляют ваше удостоверение издателя. Каждый маркер будет использоваться со своим API. Срок действия каждого маркера составляет 60минут. После истечения срока действия их можно обновить.

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

Чтобы создать маркеры доступа, используйте в своей службе API OAuth 2.0, следуя инструкциям в разделе [Служба обслуживания вызовов с помощью учетных данных клиента](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service) для отправки запроса HTTP POST в конечную точку ```https://login.microsoftonline.com/<tenant_id>/oauth2/token```. Ниже приведен пример запроса.

``` syntax
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource=https://onestore.microsoft.com
```

Для каждого маркера укажите следующие параметры:

* Для параметров *client\_id* и *client\_secret* укажите идентификатор приложения и секрет клиента для приложения, полученные на [Портале управления Azure](http://manage.windowsazure.com). Оба эти параметра являются обязательными для создания маркера доступа с уровнем проверки подлинности, необходимым для API коллекции Microsoft Store или API покупки Microsoft Store.

* В качестве параметра *resource* укажите один из URI аудиторий, указанных в [предыдущем разделе](#access-tokens), в зависимости от типа создаваемого маркера доступа.

После истечения срока действия маркера доступа вы можете обновить его, следуя инструкциям [здесь](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-code/#refreshing-the-access-tokens). Дополнительную информацию о структуре маркера доступа см. в статье [Поддерживаемые маркеры и типы утверждений](http://go.microsoft.com/fwlink/?LinkId=722501).

<span id="step-4"/>

## <a name="step-4-create-a-microsoft-store-id-key"></a>Шаг 4. Создание ключа идентификатора Microsoft Store

Прежде чем вы сможете вызвать какой-либо метод в API коллекции или API покупки Microsoft Store, ваше приложение должно создать ключ идентификатора Microsoft Store и отправить его службе. Этот ключ— это маркер JSON Web Token (JWT), который представляет удостоверение пользователя, доступ к информации о владении продуктами которого вы хотите получить. Подробнее об утверждениях в этом ключе см. в статье [Утверждения в ключе идентификатора Microsoft Store](#claims-in-a-microsoft-store-id-key).

В настоящее время единственным способом создания ключа идентификатора Microsoft Store является вызов API универсальной платформы Windows (UWP) из клиентского кода приложения. Созданный ключ представляет удостоверение пользователя, который в данный момент вошел в Microsoft Store на устройстве.

> [!NOTE]
> Каждый ключ идентификатора Microsoft Store действителен в течение 90дней. После истечения срока действия ключа можно [обновить его](renew-a-windows-store-id-key.md). Рекомендуется обновлять ключи идентификаторов Microsoft Store, а не создавать новые.

<span />

### <a name="to-create-a-microsoft-store-id-key-for-the-microsoft-store-collection-api"></a>Создание ключа идентификатора Microsoft Store для API коллекции Microsoft Store

Выполните следующие действия, чтобы создать ключ идентификатора Microsoft Store, который можно использовать с API коллекции Microsoft Store для [запроса продуктов, принадлежащих пользователю](query-for-products.md) или [объявления потребляемого продукта в качестве выполненного](report-consumable-products-as-fulfilled.md).

1.  Передайте маркер доступа Azure AD, созданный с использованием значения URI `https://onestore.microsoft.com/b2b/keys/create/collections`, из своей службы в свое клиентское приложение. Это один из маркеров, которые вы создали [ранее на Шаге 3](#step-3).

2.  В коде приложения вызовите один из следующих методов, чтобы извлечь ключ идентификатора Microsoft Store:

  * Если в вашем приложении для управления покупками из приложения используется класс [StoreContext](https://docs.microsoft.com/uwp/api/Windows.Services.Store.StoreContext) из пространства имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store), используйте метод [StoreContext.GetCustomerCollectionsIdAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getcustomercollectionsidasync).

  * Если в вашем приложении для управления покупками из приложения используется класс [CurrentApp](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp) из пространства имен [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store), используйте метод [CurrentApp.GetCustomerCollectionsIdAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.getcustomercollectionsidasync).

    Передайте маркер доступа Azure AD в параметр *serviceTicket* метода. Если вы используете анонимные идентификаторы пользователей в контексте службы, которые используют в качестве издателя текущего приложения, можно передать идентификатор параметру *publisherUserId* привязка новый ключ идентификатора Microsoft Store (идентификатор пользователя будет em текущего пользователя bedded в ключе). В противном случае если вам не нужно связать идентификатор пользователя с помощью ключа идентификатора Microsoft Store, можно передать любое строковое значение в качестве параметра *publisherUserId* .

3.  После того как приложение успешно создало ключ идентификатора Microsoft Store, передайте этот ключ обратно своей службе.

<span />

### <a name="to-create-a-microsoft-store-id-key-for-the-microsoft-store-purchase-api"></a>Создание ключа идентификатора Microsoft Store для API покупки Microsoft Store

Выполните следующие действия, чтобы создать ключ идентификатора Microsoft Store, который можно использовать с API для покупок в Microsoft Store, чтобы [предоставить бесплатный продукт пользователю](grant-free-products.md), [получить подписки для пользователя](get-subscriptions-for-a-user.md) или [изменить состояние выставления счета подписки для пользователя](change-the-billing-state-of-a-subscription-for-a-user.md).

1.  Передайте маркер доступа Azure AD, созданный с использованием значения URI `https://onestore.microsoft.com/b2b/keys/create/purchase`, из своей службы в свое клиентское приложение. Это один из маркеров, которые вы создали [ранее на Шаге 3](#step-3).

2.  В коде приложения вызовите один из следующих методов, чтобы извлечь ключ идентификатора Microsoft Store:

  * Если в вашем приложении для управления покупками из приложения используется класс [StoreContext](https://docs.microsoft.com/uwp/api/Windows.Services.Store.StoreContext) из пространства имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store), используйте метод [StoreContext.GetCustomerPurchaseIdAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getcustomerpurchaseidasync).

  * Если в вашем приложении для управления покупками из приложения используется класс [CurrentApp](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp) из пространства имен [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store), используйте метод [CurrentApp.GetCustomerPurchaseIdAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.getcustomerpurchaseidasync).

    Передайте маркер доступа Azure AD в параметр *serviceTicket* метода. Если вы используете анонимные идентификаторы пользователей в контексте службы, которые используют в качестве издателя текущего приложения, можно передать идентификатор параметру *publisherUserId* привязка новый ключ идентификатора Microsoft Store (идентификатор пользователя будет em текущего пользователя bedded в ключе). В противном случае если вам не нужно связать идентификатор пользователя с помощью ключа идентификатора Microsoft Store, можно передать любое строковое значение в качестве параметра *publisherUserId* .

3.  После того как приложение успешно создало ключ идентификатора Microsoft Store, передайте этот ключ обратно своей службе.

### <a name="diagram"></a>Схема

На следующей схеме показана процесс создания ключа идентификатора Microsoft Store.

  ![Создание ключа идентификатора магазина Windows](images/b2b-1.png)

<span id="step-5"/>

## <a name="step-5-call-the-microsoft-store-collection-api-or-purchase-api-from-your-service"></a>Шаг 5. Вызов API коллекции или API покупки в Microsoft Store из службы.

Как только у вашей службы есть ключ идентификатора Microsoft Store, который позволяет ей получать доступ к информации о владении продуктами определенным пользователем, ваша служба может вызывать API коллекции Microsoft Store или API покупки Microsoft Store. Следуйте инструкциям ниже:

* [Запрос продуктов](query-for-products.md)
* [Объявление потребляемого продукта в качестве выполненного](report-consumable-products-as-fulfilled.md)
* [Предоставление бесплатных продуктов](grant-free-products.md)
* [Получение подписок для пользователя](get-subscriptions-for-a-user.md)
* [Изменение состояния выставления счетов за подписку для пользователя](change-the-billing-state-of-a-subscription-for-a-user.md)

Для каждого сценария передайте API следующие данные:

-   В заголовке запроса передайте маркер доступа Azure AD со значением URI аудитории `https://onestore.microsoft.com`. Это один из маркеров, которые вы создали [ранее на Шаге 3](#step-3). Этот маркер представляет ваше удостоверение издателя.
-   В теле запроса передайте ключ идентификатора Microsoft Store, полученный [ранее в Шаге 4](#step-4) из клиентского кода вашего приложения. Этот ключ представляет удостоверение пользователя– владельца продукта, доступ к данным о котором вы хотите получить.

### <a name="diagram"></a>Схема

Следующая схема описывает процесс при вызове метода в API или покупки API коллекции Microsoft Store из службы.

  ![Вызовите метод коллекций или приобретать API](images/b2b-2.png)

## <a name="claims-in-a-microsoft-store-id-key"></a>Утверждения в ключе идентификатора Microsoft Store

Ключ идентификатора Microsoft Store— это веб-маркер JSON (JWT), который представляет удостоверение пользователя— владельца продукта, доступ к данным о котором вы хотите получить. После расшифровки в Base64 ключ идентификатора Microsoft Store содержит следующие утверждения.

* `iat`:&nbsp;&nbsp;&nbsp;Указывает время выпуска ключа. Это утверждение можно использовать для определения возраста маркера. Это значение выражается как время эпохи.
* `iss`:&nbsp;&nbsp;&nbsp;Определяет издателя. Имеет такое же значение, что и утверждение `aud`.
* `aud`:&nbsp;&nbsp;&nbsp;Указывает аудиторию. Должно иметь одно из следующих значений: `https://collections.mp.microsoft.com/v6.0/keys` или `https://purchase.mp.microsoft.com/v6.0/keys`.
* `exp`:&nbsp;&nbsp;&nbsp;Указывает период истечения срока действия, после которого ключ будет приниматься только для продления ключей. Для обработки другой информации ключ приниматься не будет. Значение этого утверждения выражается как время эпохи.
* `nbf`:&nbsp;&nbsp;&nbsp;Определяет период времени, в который маркер будет принят для обработки. Значение этого утверждения выражается как время эпохи.
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/clientId`:&nbsp;&nbsp;&nbsp;Идентификатор клиента, который определяет разработчика.
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/payload`:&nbsp;&nbsp;&nbsp;Непрозрачные полезные данные (зашифрованные и закодированные в Base64), которые содержат сведения, предназначенные только для использования службами Microsoft Store.
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/userId`:&nbsp;&nbsp;&nbsp;Идентификатор, который определяет текущего пользователя в контексте ваших служб. Это то же значение, которое вы передаете в необязательный параметр *publisherUserId* [метода, который используется для создания ключа](#step-4).
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/refreshUri`:&nbsp;&nbsp;&nbsp;URI, который вы можете использовать, чтобы обновить ключ.

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

## <a name="related-topics"></a>Статьи по теме

* [Запрос продуктов](query-for-products.md)
* [Объявление потребляемого продукта в качестве выполненного](report-consumable-products-as-fulfilled.md)
* [Предоставление бесплатных продуктов](grant-free-products.md)
* [Получение подписок для пользователя](get-subscriptions-for-a-user.md)
* [Изменение состояния выставления счетов за подписку для пользователя](change-the-billing-state-of-a-subscription-for-a-user.md)
* [Обновление ключа идентификатора Microsoft Store](renew-a-windows-store-id-key.md)
* [Интеграция приложений с Azure Active Directory](http://go.microsoft.com/fwlink/?LinkId=722502)
* [Основные сведения о манифесте приложения Azure Active Directory]( http://go.microsoft.com/fwlink/?LinkId=722500)
* [Поддерживаемые токены и типы утверждений](http://go.microsoft.com/fwlink/?LinkId=722501)
