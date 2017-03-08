---
author: mcleanbyron
ms.assetid: 66400066-24BF-4AF2-B52A-577F5C3CA474
description: "Используйте эти методы в API отправки Магазина Windows для управления отправками надстроек для приложений, которые зарегистрированы в вашей учетной записи Центра разработки для Windows."
title: "Управление отправками надстроек с помощью API отправки Магазина Windows"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, API отправки Магазина Windows, отправки надстроек, внутренний продукт приложения, IAP"
translationtype: Human Translation
ms.sourcegitcommit: e5d9d3e08aaae7e349f7aaf23f6683e2ce9a4f88
ms.openlocfilehash: 589946e159202c3ed5d13057642c808d5df4f738
ms.lasthandoff: 02/08/2017

---

# <a name="manage-add-on-submissions-using-the-windows-store-submission-api"></a>Управление отправками надстроек с помощью API отправки Магазина Windows

API отправки Магазина Windows предоставляет методы, которые можно использовать для управления отправками надстроек (также называются внутренним продуктом приложения или IAP) для ваших приложений. Введение в API отправки Магазина Windows, включая необходимые условия для использования этого API, см. в разделе [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md).

>**Примечание.**&nbsp;&nbsp;Эти методы могут применяться только для учетных записей Центра разработки для Windows, у которых имеется разрешение на использование API отправки Магазина Windows. Это разрешение предоставляется учетным записям разработчиков поэтапно. В настоящий момент это разрешение предоставлено не всем учетным записям. Чтобы запросить ранний доступ, войдите в панель мониторинга Центра разработки, щелкните **Обратная связь** в нижней части панели, выберите пункт **API отправки** в разделе обратной связи и отправьте запрос. Когда для вашей учетной записи будет предоставлено разрешение, вы получите уведомление по электронной почте.

>**Важно!**&nbsp;&nbsp;При использовании API отправки Магазина Windows для создания отправки надстройки обязательно вносите дополнительные изменения в отправку только с помощью API-интерфейса, а не через информационную панель Центра разработки. Если вы используете информационную панель для изменения отправки, изначально созданной с помощью API, вы больше не сможете изменять или фиксировать эту отправку с помощью API. В некоторых случаях отправка может остаться в состоянии ошибки, где продолжение процесса отправки невозможно. В этом случае необходимо удалить отправку и создать новую.

<span id="methods-for-add-on-submissions" />
## <a name="methods-for-managing-add-on-submissions"></a>Методы для управления отправками надстроек

Используйте следующие методы для получения, создания, обновления, фиксации и удаления отправки надстройки. Для того чтобы вы могли использовать эти методы, надстройка должна уже существовать в вашей учетной записи Центра разработки. Надстройку можно создать с помощью информационной панели, [указав тип и идентификатор продукта](../publish/set-your-add-on-product-id.md), или с помощью методов API отправки Магазина Windows, описываемых в разделе [Управление надстройками](manage-add-ons.md).

<table>
<colgroup>
<col width="10%" />
<col width="30%" />
<col width="60%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Способ</th>
<th align="left">URI</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">GET</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{id}/submissions/{submissionId}```</td>
<td align="left">[Получение существующей отправки надстройки](get-an-add-on-submission.md)</td>
</tr>
<tr>
<td align="left">GET</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{id}/submissions/{submissionId}/status```</td>
<td align="left">[Получение состояния существующей отправки надстройки](get-status-for-an-add-on-submission.md)</td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{id}/submissions```</td>
<td align="left">[Создание отправки надстройки](create-an-add-on-submission.md)</td>
</tr>
<tr>
<td align="left">PUT</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{id}/submissions/{submissionId}```</td>
<td align="left">[Обновление существующей отправки надстройки](update-an-add-on-submission.md)</td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{id}/submissions/{submissionId}/commit```</td>
<td align="left">[Фиксация новой или обновленной отправки надстройки](commit-an-add-on-submission.md)</td>
</tr>
<tr>
<td align="left">DELETE</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{id}/submissions/{submissionId}```</td>
<td align="left">[Удаление отправки надстройки](delete-an-add-on-submission.md)</td>
</tr>
</tbody>
</table>

<span id="create-an-add-on-submission">
## Создание отправки надстройки

Чтобы создать отправку для надстройки, выполните следующие действия.

1. Если это еще не сделано, выполните необходимые условия, описанные в разделе [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md), включая привязку приложения Azure AD к учетной записи Центра разработки для Windows и получение идентификатора клиента и ключа. Это необходимо сделать только один раз; после получения идентификатора клиента и ключа с их помощью можно в любой момент создать новый маркер доступа Azure AD.  

2. [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token). Этот маркер доступа необходимо передавать методам из API отправки Магазина Windows. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

3. Выполните следующий метод в API отправки Магазина Windows. Этот метод создает новую выполняющуюся отправку, которая является копией последней опубликованной отправки. Дополнительные сведения см. в разделе [Создание отправки надстройки](create-an-add-on-submission.md).

  > [!div class="tabbedCodeSnippets"]
  ``` syntax
  POST https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{id}/submissions
  ```

  Тело ответа содержит три элемента: идентификатор новой отправки, данные для новой отправки (в том числе все описания и информацию о ценах), а также URI подписанного URL-адреса (Shared Access Signature, SAS) для передачи любых значков надстроек для отправки в хранилище BLOB-объектов Azure.

  >**Примечание.**&nbsp;&nbsp;URI SAS предоставляет доступ к защищенному ресурсу в хранилище Azure без необходимости в использовании ключей учетной записи. Основные сведения об URI SAS и их использовании с хранилищем BLOB-объектов Azure см. в разделах [Подписанные URL-адреса, часть 1. Общие сведения о модели SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1) и [Подписанные URL-адреса, часть 2. Создание и использование SAS с хранилищем BLOB-объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/).

4. При добавлении новых значков для отправки [подготовьте значки](https://msdn.microsoft.com/windows/uwp/publish/create-iap-descriptions#icon) и добавьте их в ZIP-архив.

5. Обновите данные отправки, внеся все необходимые для новой отправки изменения, затем выполните следующий метод для обновления отправки. Дополнительные сведения см. в разделе [Обновление отправки надстройки](update-an-add-on-submission.md).

  > [!div class="tabbedCodeSnippets"]
  ``` syntax
  PUT https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{id}/submissions/{submissionId}
  ```

  <span/>
  >**Примечание.**&nbsp;&nbsp;Если в отправку добавляются новые значки, обязательно обновите данные отправки, чтобы они ссылались на имя и относительный путь этих файлов в ZIP-архиве.

4. При добавлении новых значков для отправки выложите ZIP-архив в [хранилище BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage), используя URI SAS, полученный в теле ответа метода POST, вызов которого был выполнен ранее. Существуют разные библиотеки Azure, которые можно использовать в этих целях на различных платформах, включая следующие.

  * [Клиентская библиотека службы хранилища Azure для .NET](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs)
  * [Пакет SDK службы хранилища Azure для Java](https://docs.microsoft.com/azure/storage/storage-java-how-to-use-blob-storage)
  * [Пакет SDK службы хранилища Azure для Python](https://docs.microsoft.com/azure/storage/storage-python-how-to-use-blob-storage)

  <span/>

  В следующем фрагменте кода на C# показано, как передать ZIP-архив в хранилище BLOB-объектов Azure с помощью класса [CloudBlockBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx) клиентской библиотеки службы хранилища Azure для .NET. В этом примере кода предполагается, что ZIP-архив уже был записан в потоковый объект.

  > [!div class="tabbedCodeSnippets"]
  ```csharp
  string sasUrl = "https://productingestionbin1.blob.core.windows.net/ingestion/26920f66-b592-4439-9a9d-fb0f014902ec?sv=2014-02-14&sr=b&sig=usAN0kNFNnYE2tGQBI%2BARQWejX1Guiz7hdFtRhyK%2Bog%3D&se=2016-06-17T20:45:51Z&sp=rwl";
  Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob blockBob =
      new Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob(new System.Uri(sasUrl));
  await blockBob.UploadFromStreamAsync(stream);
  ```

5. Подтвердите отправку, выполнив следующий метод. Таким образом Центр разработки оповещается, что отправка готова и обновления требуется применить к вашей учетной записи. Дополнительные сведения см. в разделе [Подтверждение отправки надстройки](commit-an-add-on-submission.md).

  > [!div class="tabbedCodeSnippets"]
  ``` syntax
  POST https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{id}/submissions/{submissionId}/commit
  ```

6. Проверьте состояние подтверждения, выполнив следующий метод. Дополнительные сведения см. в разделе [Получение состоянии отправки надстройки](get-status-for-an-add-on-submission.md).

  > [!div class="tabbedCodeSnippets"]
  ``` syntax
  GET https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{id}/submissions/{submissionId}/status
  ```

  Чтобы проверить состояние отправки, посмотрите значение поля *status* в теле ответа. Это значение должно измениться с **CommitStarted** на **PreProcessing**, если запрос выполнен успешно, или на **CommitFailed**, если в запросе возникли ошибки. Если имеются ошибки, поле *statusDetails* содержит дополнительные сведения об ошибке.

7. После успешного завершения подтверждения отправки она отправляется в Магазин для внедрения. Вы можете продолжать отслеживать ход выполнения отправки с помощью предыдущего метода или на информационной панели Центра разработки.

<span/>
### <a name="code-examples"></a>Примеры кода

Следующие статьи содержат подробные примеры кода на нескольких разных языках программирования, демонстрирующие процесс создания отправки надстройки.

* [Примеры кода на C#](csharp-code-examples-for-the-windows-store-submission-api.md)
* [Примеры кода на Java](java-code-examples-for-the-windows-store-submission-api.md)
* [Примеры кода на Python](python-code-examples-for-the-windows-store-submission-api.md)

>**Примечание**.&nbsp;&nbsp;Помимо приведенных выше примеров кода мы также предоставляем модуль PowerShell с открытым исходным кодом, который реализует интерфейс командной строки на основе API отправки Магазина Windows. Этот модуль называется [StoreBroker](https://aka.ms/storebroker). Этот модуль можно использовать для управления приложением, тестов и отправки надстройки из командной строки вместо вызова API отправки в Магазин Windows напрямую; также можно просто просмотреть исходный код, чтобы увидеть дополнительные примеры того, как вызывается этот API. Модуль StoreBroker активно используется в корпорации Майкрософт в качестве основного способа отправки множества собственных приложений в Магазин. Дополнительные сведения см. на нашей [странице StoreBroker на сайте GitHub](https://aka.ms/storebroker).

<span/>
## <a name="data-resources"></a>Ресурсы данных

Для управления отправками надстроек методы API отправки Магазина Windows используют следующие ресурсы данных JSON.

<span id="add-on-submission-object" />
### <a name="add-on-submission-resource"></a>Ресурс отправки надстройки

Этот ресурс описывает отправку надстройки.

```json
{
  "id": "1152921504621243680",
  "contentType": "EMagazine",
  "keywords": [
    "books"
  ],
  "lifetime": "FiveDays",
  "listings": {
    "en": {
      "description": "English add-on description",
      "icon": {
        "fileName": "add-on-en-us-listing2.png",
        "fileStatus": "Uploaded"
      },
      "title": "Add-on Title (English)"
    },
    "ru": {
      "description": "Russian add-on description",
      "icon": {
        "fileName": "add-on-ru-listing.png",
        "fileStatus": "Uploaded"
      },
      "title": "Add-on Title (Russian)"
    }
  },
  "pricing": {
    "marketSpecificPricings": {
      "RU": "Tier3",
      "US": "Tier4",
    },
    "sales": [],
    "priceId": "Free",
    "isAdvancedPricingModel": "true"
  },
  "targetPublishDate": "2016-03-15T05:10:58.047Z",
  "targetPublishMode": "Immediate",
  "tag": "SampleTag",
  "visibility": "Public",
  "status": "PendingCommit",
  "statusDetails": {
    "errors": [
      {
        "code": "None",
        "details": "string"
      }
    ],
    "warnings": [
      {
        "code": "ListingOptOutWarning",
        "details": "You have removed listing language(s): []"
      }
    ],
    "certificationReports": [
      {
      }
    ]
  },
  "fileUploadUrl": "https://productingestionbin1.blob.core.windows.net/ingestion/26920f66-b592-4439-9a9d-fb0f014902ec?sv=2014-02-14&sr=b&sig=usAN0kNFNnYE2tGQBI%2BARQWejX1Guiz7hdFtRhyK%2Bog%3D&se=2016-06-17T20:45:51Z&sp=rwl",
  "friendlyName": "Submission 2"
}
```

У этого ресурса есть следующие значения.

| Значение      | Тип   | Описание        |
|------------|--------|----------------------|
| id            | string  | Идентификатор отправки.  |
| contentType           | string  |  [Тип содержимого](../publish/enter-add-on-properties.md#content-type), которое предоставляется в надстройке. Может принимать одно из следующих значений. <ul><li>NotSet (Не задано)</li><li>BookDownload (Загрузка книги)</li><li>EMagazine (Электронный журнал)</li><li>ENewspaper (Электронная газета)</li><li>MusicDownload (Загрузка музыки)</li><li>MusicStream (Потоковая передача музыки)</li><li>OnlineDataStorage (Сетевое хранилище данных)</li><li>VideoDownload (Загрузка видео)</li><li>VideoStream (Потоковая передача видео)</li><li>Asp</li><li>OnlineDownload (Загрузка по Интернету)</li></ul> |  
| keywords           | массив  | Массив строк, содержащих до 10 [ключевых слов](../publish/enter-add-on-properties.md#keywords) для надстройки. Приложение может запрашивать надстройки с помощью этих ключевых слов.   |
| lifetime           | строка  |  Время существования надстройки. Может принимать одно из следующих значений. <ul><li>Forever (Навсегда)</li><li>OneDay (Один день)</li><li>ThreeDays (3 дня)</li><li>FiveDays (5 дней)</li><li>OneWeek (Одна неделя)</li><li>TwoWeeks (Две недели)</li><li>OneMonth (Один месяц)</li><li>TwoMonths (Два месяца)</li><li>ThreeMonths (Три месяца)</li><li>SixMonths (Шесть месяцев)</li><li>OneYear (Один год)</li></ul> |
| listings           | object  |  Словарь пар "ключ-значение", где каждый ключ представляет собой код страны ISO 3166-1 alpha-2 из двух букв, а каждое значение — объект [ресурса описания](#listing-object), содержащий информацию для описании надстройки.  |
| pricing           | Объект  | [Ресурс цены](#pricing-object), содержащий сведения о цене надстройки.   |
| targetPublishMode           | Строка  | Режим публикации для отправки. Может принимать одно из следующих значений. <ul><li>Immediate (Незамедлительно)</li><li>Manual (Вручную)</li><li>SpecificDate (Указанная дата)</li></ul> |
| targetPublishDate           | строка  | Дата публикации отправки в формате ISO 8601, если для *targetPublishMode* задано значение SpecificDate.  |
| tag           | строка  |  [Настраиваемые данные разработчика](../publish/enter-add-on-properties.md#custom-developer-data) для надстройки (ранее эта информация называлась *tag*).   |
| visibility  | строка  |  Видимость надстройки. Может принимать одно из следующих значений. <ul><li>Hidden (Скрыта)</li><li>Public (Общая)</li><li>Private (Частная)</li><li>NotSet (Не задано)</li></ul>  |
| status  | string  |  Состояние отправки. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>Canceled (Отменено)</li><li>PendingCommit (Ожидание фиксации)</li><li>CommitStarted (Фиксация запущена)</li><li>CommitFailed (Сбой фиксации)</li><li>PendingPublication (Ожидание публикации)</li><li>Publishing (Выполняется публикация)</li><li>Published (Опубликовано)</li><li>PublishFailed (Сбой публикации)</li><li>PreProcessing (Предварительная обработка)</li><li>PreProcessingFailed (Сбой предварительной обработки)</li><li>Certification (Сертификация)</li><li>CertificationFailed (Сбой сертификации)</li><li>Release (Выпуск)</li><li>ReleaseFailed (Сбой выпуска)</li></ul>   |
| statusDetails           | Объект  |  [Ресурс сведений о состоянии](#status-details-object), который содержит дополнительные сведения о состоянии отправки, включая сведения об ошибках. |
| fileUploadUrl           | Строка  | URI подписанного URL-адреса (SAS) для передачи пакетов для отправки. При добавлении новых пакетов для отправки выложите ZIP-архив, содержащий пакеты, по этому URI. Дополнительные сведения см. в разделе [Создание отправки надстройки](#create-an-add-on-submission).  |
| friendlyName  | строка  |  Понятное имя отправки для отображения на информационной панели Центра разработки. Это значение создается при создании отправки.  |

<span id="listing-object" />
### <a name="listing-resource"></a>Ресурс описания

Этот ресурс содержит информацию описания для надстройки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание       |
|-----------------|---------|------|
|  description               |    Строка     |   Описание для описания надстройки.   |     
|  icon               |   Объект      |[Ресурс значка](#icon-object), содержащий данные для значка описания надстройки.    |
|  title               |     Строка    |   Заголовок для описания надстройки.   |  

<span id="icon-object" />
### <a name="icon-resource"></a>Ресурс значка

Этот ресурс содержит данные значка для описания надстройки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание     |
|-----------------|---------|------|
|  fileName               |    string     |   Имя файла значка в ZIP-архиве, который был передан для отправки.    |     
|  fileStatus               |   string      |  Состояние файла значка. Может принимать одно из следующих значений. <ul><li>None</li><li>PendingUpload (Ожидает передачи)</li><li>Uploaded (Передан)</li><li>PendingDelete (Ожидает удаления)</li></ul>   |

<span id="pricing-object" />
### <a name="pricing-resource"></a>Ресурс цены

Этот ресурс содержит сведения о ценах для надстройки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание    |
|-----------------|---------|------|
|  marketSpecificPricings               |    object     |  Словарь пар "ключ-значение", где каждый ключ представляет собой код страны ISO 3166-1 alpha-2 из двух букв, а каждое значение — [ценовую категорию](#price-tiers). Эти элементы представляют [особые цены на вашу надстройку для определенных рынков](https://msdn.microsoft.com/windows/uwp/publish/set-iap-pricing-and-availability#markets-and-custom-prices). Любые элементы этого словаря переопределяют базовую цену, заданную значением *priceId* для указанного рынка.     |     
|  sales               |   Массив      |  **Не рекомендуется**. Массив [ресурсов продажи](#sale-object), содержащих сведения о продажах для надстройки.     |     
|  priceId               |   строка      |  [Ценовая категория](#price-tiers), указывающая [базовую цену](https://msdn.microsoft.com/windows/uwp/publish/set-iap-pricing-and-availability#base-price) для надстройки.    |    
|  isAdvancedPricingModel               |   boolean (логическая)      |  Если **true**, ваша учетная запись разработчика имеет доступ к расширенному набору ценовых категорий от 0,99 долл. США до 1999,99 долл. США. Если **false**, ваша учетная запись разработчика имеет доступ к стандартному набору ценовых категорий от 0,99 долл. США до 999,99 долл. США. Дополнительные сведения о разных категориях см. в разделе [Ценовые категории](#price-tiers).<br/><br/>**Примечание**.&nbsp;&nbsp;Данное поле предназначено только для чтения.   |


<span id="sale-object" />
### <a name="sale-resource"></a>Ресурс продажи

Этот ресурс содержит сведения о продаже для надстройки.

>**Важно!**&nbsp;&nbsp;Ресурс **продажи** больше не поддерживается, и в настоящее время невозможно получить или изменить данные продажи для отправки надстройки с помощью API отправки в Магазин Windows.

   > * После вызова [метода GET для получения отправки надстройки](get-an-add-on-submission.md) значение *sales* будет пустым. Вы можете продолжать использовать информационную панель Центра разработки для получения данных продаж для отправки надстройки.
   > * При вызове [метода PUT для обновления отправки надстройки](update-an-add-on-submission.md) сведения в значении *sales* будут игнорироваться. Вы можете продолжать использовать информационную панель Центра разработки для изменения данных продаж для отправки надстройки.

> В дальнейшем мы обновим API отправки Магазина Windows для внедрения нового способа программного доступа к информации о продажах для отправок надстроек.

Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание           |
|-----------------|---------|------|
|  name               |    string     |   Имя продажи.    |     
|  basePriceId               |   string      |  [Ценовая категория](#price-tiers), используемая для базовой цены продажи.    |     
|  startDate               |   string      |   Дата начала для продажи в формате ISO 8601.  |     
|  endDate               |   string      |  Дата окончания для продажи в формате ISO 8601.      |     
|  marketSpecificPricings               |   object      |   Словарь пар "ключ-значение", где каждый ключ представляет собой код страны ISO 3166-1 alpha-2 из двух букв, а каждое значение — [ценовую категорию](#price-tiers). Эти элементы представляют [особые цены на вашу надстройку для определенных рынков](https://msdn.microsoft.com/windows/uwp/publish/set-iap-pricing-and-availability#markets-and-custom-pricess). Любые элементы этого словаря переопределяют базовую цену, заданную значением *basePriceId* для указанного рынка.    |

<span id="status-details-object" />
### <a name="status-details-resource"></a>Ресурс сведений о состоянии

Этот ресурс содержит дополнительные сведения о состоянии отправки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание       |
|-----------------|---------|------|
|  errors               |    Объект     |   Массив [ресурсов сведений о состоянии](#status-detail-object), который содержит информацию об ошибках для отправки.   |     
|  warnings               |   Объект      | Массив [ресурсов сведений о состоянии](#status-detail-object), который содержит информацию о предупреждениях для отправки.     |
|  certificationReports               |     Объект    |   Массив [ресурсов отчета сертификации](#certification-report-object), который предоставляет доступ к данным отчета о сертификации для отправки. В случае сбоя сертификации можно проверить эти отчеты для получения дополнительной информации.    |  

<span id="status-detail-object" />
### <a name="status-detail-resource"></a>Ресурс сведений о состоянии

Этот ресурс содержит дополнительные сведения обо всех связанных ошибках или предупреждениях для отправки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание    |
|-----------------|---------|------|
|  code               |    Строка     |   [Код состояния отправки](#submission-status-code), описывающий тип ошибки или предупреждения.   |     
|  details               |     Строка    |  Сообщение с дополнительными сведениями о проблеме.     |

<span id="certification-report-object" />
### <a name="certification-report-resource"></a>Ресурс отчета о сертификации

Этот ресурс предоставляет доступ к данным отчета о сертификации для отправки. Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание               |
|-----------------|---------|------|
|     date            |    string     |  Дата и время создания отчета (в формате ISO 8601).    |
|     reportUrl            |    string     |  URL-адрес, по которому можно получить доступ к отчету.    |

## <a name="enums"></a>Перечисления

Эти методы используют следующие перечисления.

<span id="price-tiers" />
### <a name="price-tiers"></a>Ценовые категории

Следующие значения представляют доступные ценовые категории в ресурсе [ценовой ресурс](#pricing-object) для отправки надстройки.

| Значение           | Описание       |
|-----------------|------|
|  Base               |   Ценовая категория не задана; используется базовая цена для надстройки.      |     
|  NotAvailable              |   Надстройка недоступна в указанном регионе.    |     
|  Free              |   Надстройка бесплатна.    |    
|  Tier*xxxx*               |   Строка, указывающая ценовую категорию надстройки в формате **Tier<em>xxxx</em>**. В настоящее время поддерживаются следующие ценовые категории.<br/><br/><ul><li>Если значение *isAdvancedPricingModel* [ценового ресурса](#pricing-object) — **true**, для вашей учетной записи доступны такие ценовые категории: **Tier1012** - **Tier1424**.</li><li>Если значение *isAdvancedPricingModel* [ценового ресурса](#pricing-object) — **false**, для вашей учетной записи доступны такие ценовые категории: **Tier2** - **Tier96**.</li></ul>Полную таблицу ценовых категорий, доступных для вашей учетной записи разработчика, включая цены для различных рынков, связанные с каждой категорией, можно найти на странице **Цены и доступность** для любой вашей отправки приложения в информационной панели Центра разработки, нажав ссылку **посмотреть таблицу** в разделе **Рынки и особые цены** (для некоторых учетных записей разработчика эта ссылка находится в разделе **Цены**).     |

<span id="submission-status-code" />
### <a name="submission-status-code"></a>Код состояния отправки

Следующие значения представляют код состояния отправки.

| Значение           |  Описание      |
|-----------------|---------------|
|  None            |     Код не указан.         |     
|      InvalidArchive        |     ZIP-архив, содержащий пакет, является недопустимым или имеет неизвестный формат архива.  |
| MissingFiles | В ZIP-архиве отсутствуют файлы, перечисленные в данных отправки, или они находятся в неправильном месте в архиве. |
| PackageValidationFailed | Один или несколько пакетов в вашей отправке не прошли проверку. |
| InvalidParameterValue | Один из параметров в теле запроса не является допустимым. |
| InvalidOperation | Операция, которую вы пытались выполнить, является недопустимой. |
| InvalidState | Операция, которую вы пытались выполнить, является недопустимой для текущего состояния тестового пакета. |
| ResourceNotFound | Не удалось найти указанный тестовый пакет. |
| ServiceError | Запрос не был успешно выполнен из-за внутренней ошибки службы. Попробуйте повторить запрос. |
| ListingOptOutWarning | Разработчик удалил описание из предыдущей отправки или не включил данные описания, которые поддерживаются пакетом. |
| ListingOptInWarning  | Разработчик добавил описание. |
| UpdateOnlyWarning | Разработчик пытается вставить какой-то элемент, который имеет только поддержку обновления. |
| Other  | Отправка находится в неизвестном состоянии или состоянии, не отнесенном ни к одной из категорий. |
| PackageValidationWarning | В процессе проверки пакета создано предупреждение. |

<span/>

## <a name="related-topics"></a>Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
* [Управление надстройками с помощью API отправки Магазина Windows](manage-add-ons.md)
* [Отправки надстроек на информационной панели Центра разработки](https://msdn.microsoft.com/windows/uwp/publish/iap-submissions)

