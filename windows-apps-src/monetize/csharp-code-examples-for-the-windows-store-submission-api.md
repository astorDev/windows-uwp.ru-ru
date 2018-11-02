---
author: Xansky
ms.assetid: FABA802F-9CB2-4894-9848-9BB040F9851F
description: Используйте приведенные в этом разделе примеры кода на C#, чтобы подробнее ознакомиться с работой API отправки в Microsoft Store.
title: 'Пример на языке C#: отправки для приложений, надстроек и тестовых пакетов'
ms.author: mhopkins
ms.date: 08/03/2017
ms.topic: article
keywords: windows 10, UWP, API отправки в Microsoft Store, примеры кода, C#
ms.localizationpriority: medium
ms.openlocfilehash: 55843f229252b2f68df096442f27544a59ce69cd
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5945243"
---
# <a name="c-sample-submissions-for-apps-add-ons-and-flights"></a>Пример на языке C\#: отправки для приложений, надстроек и тестовых пакетов

В этой статье представлены примеры кода на C# по использованию [API отправки в Microsoft Store](create-and-manage-submissions-using-windows-store-services.md) для решения этих задач.

* [Создание отправки приложения](#create-app-submission)
* [Создание отправки надстройки](#create-add-on-submission)
* [Обновление отправки надстройки](#update-add-on-submission)
* [Создание отправки тестового пакета](#create-flight-submission)

Вы можете ознакомиться с каждым примером, чтобы подробнее узнать о демонстрируемой в нем задаче, либо вы можете собрать все примеры кода в этой статье в консольное приложение. Для сборки примеров создайте консольное приложение C# с именем **DeveloperApiCSharpSample** в Visual Studio, скопируйте каждый пример в отдельный файл с кодом в проекте и соберите проект.

## <a name="prerequisites"></a>Необходимые условия

В этих примерах используются следующие библиотеки:

* Microsoft.WindowsAzure.Storage.dll. Эта библиотека доступна в [Пакете SDK Azure для .NET](https://azure.microsoft.com/downloads/). Ее также можно получить путем установки [пакета NuGet WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage).
* Пакет NuGet [Newtonsoft.Json](http://www.newtonsoft.com/json) от Newtonsoft.

## <a name="main-program"></a>Основная программа

В следующем примере реализуется программа командной строки, вызывающая другие методы из примеров в этой статье для демонстрации различных вариантов использования API отправки в Microsoft Store. Адаптация программы для собственного использования.

* Назначьте свойства ```ApplicationId```, ```InAppProductId``` и ```FlightId``` идентификатору приложения, надстройке и тестовому пакету, которыми вы хотите управлять.
* Назначьте свойства ```ClientId``` и ```ClientSecret``` идентификатору клиента и ключу своего приложения и замените строку *tenantid* в URL-адресе ```TokenEndpoint``` идентификатором владельца для своего приложения. Дополнительные сведения см. в разделе [Привязка приложения Azure AD к учетной записи в Центре разработки для Windows](create-and-manage-submissions-using-windows-store-services.md#how-to-associate-an-azure-ad-application-with-your-windows-dev-center-account).

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_Submission/cs/Program.cs#Main)]

<span id="clientconfiguration" />

## <a name="clientconfiguration-helper-class"></a>Вспомогательный класс ClientConfiguration

Образец приложения использует вспомогательный класс ```ClientConfiguration``` для передачи данных Azure Active Directory и данных приложения остальным методам из примеров, в которых используется API отправки в Microsoft Store.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_Submission/cs/ClientConfiguration.cs#ClientConfiguration)]

<span id="create-app-submission" />

## <a name="create-an-app-submission"></a>Создание отправки приложения

В следующем примере реализован класс, который использует несколько методов из API отправки в Microsoft Store для обновления отправки приложения. Метод ```RunAppSubmissionUpdateSample``` в классе создает новую отправку в качестве клона последней опубликованной отправки, а затем обновляет и фиксирует клонированную отправку в Центре разработки для Windows. В частности, метод ```RunAppSubmissionUpdateSample``` выполняет следующие задачи:

1. Сначала метод [получает данные для указанного приложения](get-an-app.md).
2. Затем он [удаляет ожидающую отправку для приложения](delete-an-app-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для приложения](create-an-app-submission.md) (новая отправка— это копия последней опубликованной отправки).
4. Код изменяет некоторые сведения о новой отправке и отправляет новый пакет отправки в хранилище BLOB-объектов Azure.
5. Затем он [обновляет](update-an-app-submission.md) и [фиксирует](commit-an-app-submission.md) новую отправку в Центре разработки для Windows.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-an-app-submission.md), пока она не будет успешно зафиксирована.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_Submission/cs/AppSubmissionUpdateSample.cs#AppSubmissionUpdateSample)]

<span id="create-add-on-submission" />

## <a name="create-an-add-on-submission"></a>Создание отправки надстройки

В следующем примере реализован класс, который использует несколько методов из API отправки в Microsoft Store для создания новой отправки надстройки. Метод ```RunInAppProductSubmissionCreateSample``` в классе выполняет следующие задачи:

1. Сначала метод [создает новую надстройку](create-an-add-on.md).
2. Затем он [создает новую отправку для надстройки](create-an-add-on-submission.md).
3. Код передает ZIP-архив, содержащий значки для отправки, в хранилище BLOB-объектов Azure.
4. Затем он [фиксирует новую отправку в Центре разработки для Windows](commit-an-add-on-submission.md).
5. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-an-add-on-submission.md), пока она не будет успешно зафиксирована.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_Submission/cs/InAppProductSubmissionCreateSample.cs#InAppProductSubmissionCreateSample)]

<span id="update-add-on-submission" />

## <a name="update-an-add-on-submission"></a>Обновление отправки надстройки

В следующем примере реализован класс, который использует несколько методов в API отправки в Microsoft Store для обновления имеющейся отправки надстройки. Метод ```RunInAppProductSubmissionUpdateSample``` в классе создает новую отправку в качестве клона последней опубликованной отправки, а затем обновляет и фиксирует клонированную отправку в Центре разработки для Windows. В частности, метод ```RunInAppProductSubmissionUpdateSample``` выполняет следующие задачи:

1. Сначала метод [получает данные для указанной надстройки](get-an-add-on.md).
2. Затем он [удаляет ожидающую отправку для надстройки](delete-an-add-on-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для надстройки](create-an-add-on-submission.md) (новая отправка— это копия последней опубликованной отправки).
5. Затем он [обновляет](update-an-add-on-submission.md) и [фиксирует](commit-an-add-on-submission.md) новую отправку в Центре разработки для Windows.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-an-add-on-submission.md), пока она не будет успешно зафиксирована.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_Submission/cs/InAppProductSubmissionUpdateSample.cs#InAppProductSubmissionUpdateSample)]

<span id="create-flight-submission" />

## <a name="create-a-package-flight-submission"></a>Создание отправки тестового пакета

В следующем примере реализован класс, который использует несколько методов в API отправки в Microsoft Store для обновления отправки тестового пакета. Метод ```RunFlightSubmissionUpdateSample``` в классе создает новую отправку в качестве клона последней опубликованной отправки, а затем обновляет и фиксирует клонированную отправку в Центре разработки для Windows. В частности, метод ```RunFlightSubmissionUpdateSample``` выполняет следующие задачи:

1. Сначала метод [получает данные для указанного тестового пакета](get-a-flight.md).
2. Затем он [удаляет ожидающую отправку для тестового пакета](delete-a-flight-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для тестового пакета](create-a-flight-submission.md) (новая отправка— это копия последней опубликованной отправки).
4. Код передает новый пакет для отправки в хранилище BLOB-объектов Azure.
5. Затем он [обновляет](update-a-flight-submission.md) и [фиксирует](commit-a-flight-submission.md) новую отправку в Центре разработки для Windows.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-a-flight-submission.md), пока она не будет успешно зафиксирована.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_Submission/cs/FlightSubmissionUpdateSample.cs#FlightSubmissionUpdateSample)]

<span id="ingestionclient" />

## <a name="ingestionclient-helper-class"></a>Вспомогательный класс IngestionClient

Класс ```IngestionClient``` предоставляет вспомогательные методы, используемые другими методами в образце приложения для выполнения следующих задач:

* [Получение маркера доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который можно использовать для вызова методов в API отправки в Microsoft Store. После получения маркера доступа у вас будет 60минут, чтобы использовать его в вызовах к API отправки Microsoft Store до окончания срока действия маркера. После истечения срока действия маркера можно сформировать новый маркер.
* Отправьте ZIP-архив, содержащий новые средства для отправки приложения или надстройки в хранилище BLOB-объектов Azure. Дополнительные сведения о передаче ZIP-архива в хранилище BLOB-объектов Azure для отправки приложений и надстроек см. в соответствующих инструкциях в разделах [Создание отправки приложения](manage-app-submissions.md#create-an-app-submission) и [Создание отправки надстройки](manage-add-on-submissions.md#create-an-add-on-submission).
* Обработайте запросы HTTP для API отправки в Microsoft Store.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_Submission/cs/IngestionClient.cs#IngestionClient)]

## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
