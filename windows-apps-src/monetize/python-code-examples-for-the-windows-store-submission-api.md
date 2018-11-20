---
author: Xansky
ms.assetid: 8AC56AAF-8D8C-4193-A6B3-BB5D0669D994
description: Используйте примеры кода на языке Python, приведенные в этом разделе, чтобы более подробно ознакомиться с работой API отправки в Microsoft Store.
title: Пример на языке Python — отправки для приложений, надстроек и тестовых пакетов
ms.author: mhopkins
ms.date: 07/10/2017
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, примеры кода, python
ms.localizationpriority: medium
ms.openlocfilehash: 34d686b8e20d384da4a3db1ea3805ad082d5f8a8
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "7417335"
---
# <a name="python-sample-submissions-for-apps-add-ons-and-flights"></a>Пример на языке Python: отправки для приложений, надстроек и тестовых пакетов

В этой статье представлены примеры кода на Python по использованию [API отправки в Microsoft Store](create-and-manage-submissions-using-windows-store-services.md) для решения этих задач.

* [Получение маркера доступа Azure AD](#token)
* [Создание надстройки](#create-add-on)
* [Создание тестового пакета](#create-package-flight)
* [Создание отправки приложения](#create-app-submission)
* [Создание отправки надстройки](#create-add-on-submission)
* [Создание отправки тестового пакета](#create-flight-submission)

<span id="token" />

## <a name="obtain-an-azure-ad-access-token"></a>Получение токена доступа Azure AD

В следующем примере показано, как [получить маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который можно использовать для вызова методов в API отправки в Microsoft Store. После получения маркера доступа у вас будет 60минут, чтобы использовать его в вызовах к API отправки Microsoft Store до окончания срока действия маркера. После истечения срока действия маркера можно сформировать новый маркер.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L1-L20)]

<span id="create-add-on" />

## <a name="create-an-add-on"></a>Создание надстройки

В следующем примере показано, как [создать](create-an-add-on.md), а затем [удалить](delete-an-add-on.md) надстройку.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L26-L52)]

<span id="create-package-flight" />

## <a name="create-a-package-flight"></a>Создание тестового пакета

В следующем примере показано, как [создать](create-a-flight.md), а затем [удалить](delete-a-flight.md) тестовый пакет.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L58-L87)]

<span id="create-app-submission" />

## <a name="create-an-app-submission"></a>Создание отправки приложения

В следующем примере показано, как использовать несколько методов в API отправки в Microsoft Store для создания отправки приложения. Чтобы сделать это, код создает новую отправку в качестве клона последней опубликованной отправки и затем обновляет и фиксирует клонированную отправку в центре партнеров. В частности, в примере этого кода выполняются следующие задачи:

1. Сначала код [получает данные для указанного приложения](get-an-app.md).
2. Затем он [удаляет ожидающую отправку для приложения](delete-an-app-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для приложения](create-an-app-submission.md) (новая отправка— это копия последней опубликованной отправки).
4. Код изменяет некоторые сведения о новой отправке и отправляет новый пакет отправки в хранилище BLOB-объектов Azure.
5. Затем он [обновления](update-an-app-submission.md) , а затем [фиксирует](commit-an-app-submission.md) новую отправку в центре партнеров.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-an-app-submission.md), пока она не будет успешно зафиксирована.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L93-L166)]

<span id="create-add-on-submission" />

## <a name="create-an-add-on-submission"></a>Создание отправки надстройки

В следующем примере показано, как использовать несколько методов в API отправки в Microsoft Store для создания отправки надстройки. Чтобы сделать это, код создает новую отправку в качестве клона последней опубликованной отправки и затем обновляет и фиксирует клонированную отправку в центре партнеров. В частности, в примере этого кода выполняются следующие задачи:

1. Сначала код [получает данные для указанной надстройки](get-an-add-on.md).
2. Затем он [удаляет ожидающую отправку для надстройки](delete-an-add-on-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для надстройки](create-an-add-on-submission.md) (новая отправка— это копия последней опубликованной отправки).
4. Код передает ZIP-архив, содержащий значки для отправки, в хранилище BLOB-объектов Azure. Дополнительные сведения приведены в соответствующих инструкциях о передаче ZIP-архива в хранилище BLOB-объектов Azure в разделе [Создание отправки надстройки](manage-add-on-submissions.md#create-an-add-on-submission).
5. Затем он [обновления](update-an-add-on-submission.md) , а затем [фиксирует](commit-an-add-on-submission.md) новую отправку в центре партнеров.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-an-add-on-submission.md), пока она не будет успешно зафиксирована.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L172-L245)]

<span id="create-flight-submission" />

## <a name="create-a-package-flight-submission"></a>Создание отправки тестового пакета

В следующем примере показано, как использовать несколько методов в API отправки в Microsoft Store для создания отправки тестового пакета. Чтобы сделать это, код создает новую отправку в качестве клона последней опубликованной отправки и затем обновляет и фиксирует клонированную отправку в центре партнеров. В частности, в примере этого кода выполняются следующие задачи:

1. Сначала код [получает данные для указанного тестового пакета](get-a-flight.md).
2. Затем он [удаляет ожидающую отправку для тестового пакета](delete-a-flight-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для тестового пакета](create-a-flight-submission.md) (новая отправка— это копия последней опубликованной отправки).
4. Код передает новый пакет для отправки в хранилище BLOB-объектов Azure. Дополнительные сведения приведены в соответствующих инструкциях о передаче ZIP-архива в хранилище BLOB-объектов Azure в разделе [Создание отправки тестового пакета](manage-flight-submissions.md#create-a-package-flight-submission).
5. Затем он [обновления](update-a-flight-submission.md) , а затем [фиксирует](commit-a-flight-submission.md) новую отправку в центре партнеров.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-a-flight-submission.md), пока она не будет успешно зафиксирована.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L251-L325)]

## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
