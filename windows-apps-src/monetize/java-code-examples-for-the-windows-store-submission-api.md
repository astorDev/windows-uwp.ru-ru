---
author: Xansky
ms.assetid: 4920D262-B810-409E-BA3A-F68AADF1B1BC
description: Используйте примеры кода на языке Java, приведенные в этом разделе, чтобы более подробно ознакомиться с работой API отправки в Microsoft Store.
title: Пример на языке Java — отправки для приложений, надстроек и тестовых пакетов
ms.author: mhopkins
ms.date: 07/10/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, UWP, API отправки в Microsoft Store, примеры кода, java
ms.localizationpriority: medium
ms.openlocfilehash: e058e456b16b5415984e6cf8de0a0fdb68159f07
ms.sourcegitcommit: 9354909f9351b9635bee9bb2dc62db60d2d70107
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2018
ms.locfileid: "4684717"
---
# <a name="java-sample-submissions-for-apps-add-ons-and-flights"></a>Пример на языке Java: отправки для приложений, надстроек и тестовых пакетов

В этой статье представлены примеры кода на Java по использованию [API отправки в Microsoft Store](create-and-manage-submissions-using-windows-store-services.md) для решения этих задач.

* [Получение маркера доступа Azure AD](#token)
* [Создание надстройки](#create-add-on)
* [Создание тестового пакета](#create-package-flight)
* [Создание отправки приложения](#create-app-submission)
* [Создание отправки надстройки](#create-add-on-submission)
* [Создание отправки тестового пакета](#create-flight-submission)

Вы можете ознакомиться с каждым примером, чтобы подробнее узнать о демонстрируемой в нем задаче, либо вы можете собрать все примеры кода в этой статье в консольное приложение. Полный текст кода см. в разделе [Текст кода](java-code-examples-for-the-windows-store-submission-api.md#code-listing) в конце данной статьи.

## <a name="prerequisites"></a>Необходимые условия

В этих примерах используются следующие библиотеки:

* [Apache Commons Logging 1.2](http://commons.apache.org/proper/commons-logging) (commons-logging-1.2.jar).
* [Apache HttpComponents Core 4.4.5 и Apache HttpComponents Client 4.5.2](https://hc.apache.org/) (httpcore-4.4.5.jar и httpclient-4.5.2.jar).
* [JSR 353 JSON Processing API 1.0](https://mvnrepository.com/artifact/javax.json/javax.json-api/1.0) и [JSR 353 JSON Processing Default Provider API 1.0.4](https://mvnrepository.com/artifact/org.glassfish/javax.json/1.0.4) (javax.json-api-1.0.jar и javax.json-1.0.4.jar).

## <a name="main-program-and-imports"></a>Главная программа и операторы импорта

В следующем примере показаны операторы import, используемые во всех примерах кода, и реализована программа командной строки, которая вызывает другие примеры методов.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/java/MainExample.java#L1-L64)]

<span id="token" />

## <a name="obtain-an-azure-ad-access-token"></a>Получение токена доступа Azure AD

В следующем примере показано, как [получить маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который можно использовать для вызова методов в API отправки в Microsoft Store. После получения маркера доступа у вас будет 60минут, чтобы использовать его в вызовах к API отправки Microsoft Store до окончания срока действия маркера. После истечения срока действия маркера можно сформировать новый маркер.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/java/CompleteExample.java#L65-L95)]

<span id="create-add-on" />

## <a name="create-an-add-on"></a>Создание надстройки

В следующем примере показано, как [создать](create-an-add-on.md), а затем [удалить](delete-an-add-on.md) надстройку.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/java/CompleteExample.java#L310-L345)]

<span id="create-package-flight" />

## <a name="create-a-package-flight"></a>Создание тестового пакета

В следующем примере показано, как [создать](create-a-flight.md), а затем [удалить](delete-a-flight.md) тестовый пакет.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/java/CompleteExample.java#L185-L221)]

<span id="create-app-submission" />

## <a name="create-an-app-submission"></a>Создание отправки приложения

В следующем примере показано, как использовать несколько методов в API отправки в Microsoft Store для создания отправки приложения. Чтобы это сделать, метод ```SubmitNewApplicationSubmission``` создает новую отправку в качестве клона последней опубликованной отправки, а затем обновляет и фиксирует клонированную отправку в Центре разработки для Windows. В частности, метод ```SubmitNewApplicationSubmission``` выполняет следующие задачи.

1. Сначала метод [получает данные для указанного приложения](get-an-app.md).
2. Затем он [удаляет ожидающую отправку для приложения](delete-an-app-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для приложения](create-an-app-submission.md) (новая отправка— это копия последней опубликованной отправки).
4. Код изменяет некоторые сведения о новой отправке и отправляет новый пакет отправки в хранилище BLOB-объектов Azure.
5. Затем он [обновляет](update-an-app-submission.md) и [фиксирует](commit-an-app-submission.md) новую отправку в Центре разработки для Windows.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-an-app-submission.md), пока она не будет успешно зафиксирована.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/java/CompleteExample.java#L97-L183)]

<span id="create-add-on-submission" />

## <a name="create-an-add-on-submission"></a>Создание отправки надстройки

В следующем примере показано, как использовать несколько методов в API отправки в Microsoft Store для создания отправки надстройки. Чтобы это сделать, метод ```SubmitNewInAppProductSubmission``` создает новую отправку в качестве клона последней опубликованной отправки, а затем обновляет и фиксирует клонированную отправку в Центре разработки для Windows. В частности, метод ```SubmitNewInAppProductSubmission``` выполняет следующие задачи:

1. Сначала метод [получает данные для указанной надстройки](get-an-add-on.md).
2. Затем он [удаляет ожидающую отправку для надстройки](delete-an-add-on-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для надстройки](create-an-add-on-submission.md) (новая отправка— это копия последней опубликованной отправки).
4. Код передает ZIP-архив, содержащий значки для отправки, в хранилище BLOB-объектов Azure.
5. Затем он [обновляет](update-an-add-on-submission.md) и [фиксирует](commit-an-add-on-submission.md) новую отправку в Центре разработки для Windows.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-an-add-on-submission.md), пока она не будет успешно зафиксирована.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/java/CompleteExample.java#L347-L431)]

<span id="create-flight-submission" />

## <a name="create-a-package-flight-submission"></a>Создание отправки тестового пакета

В следующем примере показано, как использовать несколько методов в API отправки в Microsoft Store для создания отправки тестового пакета. Чтобы это сделать, метод ```SubmitNewFlightSubmission``` создает новую отправку в качестве клона последней опубликованной отправки, а затем обновляет и фиксирует клонированную отправку в Центре разработки для Windows. В частности, метод ```SubmitNewFlightSubmission``` выполняет следующие задачи:

1. Сначала метод [получает данные для указанного тестового пакета](get-a-flight.md).
2. Затем он [удаляет ожидающую отправку для тестового пакета](delete-a-flight-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для тестового пакета](create-a-flight-submission.md) (новая отправка— это копия последней опубликованной отправки).
4. Код передает новый пакет для отправки в хранилище BLOB-объектов Azure.
5. Затем он [обновляет](update-a-flight-submission.md) и [фиксирует](commit-a-flight-submission.md) новую отправку в Центре разработки для Windows.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-a-flight-submission.md), пока она не будет успешно зафиксирована.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/java/CompleteExample.java#L223-L308)]

<span id="utilities" />

## <a name="utility-methods-to-upload-submission-files-and-handle-request-responses"></a>Служебные методы для передачи файлов отправки и обработки ответов на запросы

Эти служебные методы используются для выполнения следующих задач.

* Передача ZIP-архива, содержащего новые средства для отправки приложения или надстройки в хранилище BLOB-объектов Azure. Дополнительные сведения о передаче ZIP-архива в хранилище BLOB-объектов Azure для отправок приложения и надстройки см. в соответствующих инструкциях в разделах [Создание отправки приложения](manage-app-submissions.md#create-an-app-submission), [Создание отправки надстройки](manage-add-on-submissions.md#create-an-add-on-submission) и [Создание отправки тестового пакета](manage-flight-submissions.md#create-a-package-flight-submission).
* Обработка ответов на запросы.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/java/CompleteExample.java#L433-L490)]

<span id="code-listing" />

## <a name="complete-code-listing"></a>Полный текст кода

В приведенном ниже коде содержатся все предыдущие примеры, собранные в один исходный файл.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/java/CompleteExample.java#L1-L491)]

## <a name="related-topics"></a>Связанные разделы

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
