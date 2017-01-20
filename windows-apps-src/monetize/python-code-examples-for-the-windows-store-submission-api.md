---
author: mcleanbyron
ms.assetid: 8AC56AAF-8D8C-4193-A6B3-BB5D0669D994
description: "Используйте примеры кода на языке Python, приведенные в этом разделе, чтобы более подробно ознакомиться с работой API отправки Магазина Windows."
title: "Примеры кода Python для API отправки Магазина Windows"
translationtype: Human Translation
ms.sourcegitcommit: ccc7cfea885cc9c8803cfc70d2e043192a7fee84
ms.openlocfilehash: 1f8d415744d53120191f02a17aed4b7b04fa2f57

---

# <a name="python-code-examples-for-the-windows-store-submission-api"></a>Примеры кода Python для API отправки Магазина Windows

В этой статье представлены примеры кода Python для использования *API отправки Магазина Windows*. Дополнительные сведения об этом API-интерфейсе см. в разделе [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md).

В этих примерах кода демонстрируются следующие задачи:

* [Получение токена доступа Azure AD](#token)
* [Создание надстройки](#create-add-on)
* [Создание тестового пакета](#create-package-flight)
* [Создание отправки приложения](#create-app-submission)
* [Создание отправки надстройки](#create-add-on-submission)
* [Создание отправки тестового пакета](#create-flight-submission)

<span id="token" />
## <a name="obtain-an-azure-ad-access-token"></a>Получение токена доступа Azure AD

В следующем примере показано, как [получить маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который можно использовать для вызова методов в API отправки Магазина Windows. После получения маркера доступа у вас будет 60 минут, чтобы использовать его в вызовах к API отправки Магазина Windows до окончания срока действия маркера. После истечения срока действия маркера можно сформировать новый маркер.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L1-L20)]

<span id="create-add-on" />
## <a name="create-an-add-on"></a>Создание надстройки

В следующем примере показано, как [создать](create-an-add-on.md), а затем [удалить](delete-an-add-on.md) надстройку (надстройки также известны как внутренние продукты приложений или IAP).

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L26-L52)]

<span id="create-package-flight" />
## <a name="create-a-package-flight"></a>Создание тестового пакета

В следующем примере показано, как [создать](create-a-flight.md), а затем [удалить](delete-a-flight.md) тестовый пакет.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L58-L87)]

<span id="create-app-submission" />
## <a name="create-an-app-submission"></a>Создание отправки приложения

В следующем примере показано, как использовать несколько методов в API отправки Магазина Windows для создания отправки приложения. Чтобы это сделать, код создает новую отправку в качестве клона последней опубликованной отправки, а затем обновляет и фиксирует клонированную отправку в Центре разработки для Windows. В частности, в примере этого кода выполняются следующие задачи:

1. Сначала код [получает данные для указанного приложения](get-an-app.md).
2. Затем он [удаляет ожидающую отправку для приложения](delete-an-app-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для приложения](create-an-app-submission.md) (новая отправка — это копия последней опубликованной отправки).
4. Код изменяет некоторые сведения о новой отправке и отправляет новый пакет отправки в хранилище BLOB-объектов Azure.
5. Затем он [обновляет](update-an-app-submission.md) и [фиксирует](commit-an-app-submission.md) новую отправку в Центре разработки для Windows.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-an-app-submission.md), пока она не будет успешно зафиксирована.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L93-L166)]

<span id="create-add-on-submission" />
## <a name="create-an-add-on-submission"></a>Создание отправки надстройки

В следующем примере показано, как использовать несколько методов в API отправки Магазина Windows для создания отправки надстройки. Чтобы это сделать, код создает новую отправку в качестве клона последней опубликованной отправки, а затем обновляет и фиксирует клонированную отправку в Центре разработки для Windows. В частности, в примере этого кода выполняются следующие задачи:

1. Сначала код [получает данные для указанной надстройки](get-an-add-on.md).
2. Затем он [удаляет ожидающую отправку для надстройки](delete-an-add-on-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для надстройки](create-an-add-on-submission.md) (новая отправка — это копия последней опубликованной отправки).
4. Код передает ZIP-архив, содержащий значки для отправки, в хранилище BLOB-объектов Azure. Дополнительные сведения приведены в соответствующих инструкциях о передаче ZIP-архива в хранилище BLOB-объектов Azure в разделе [Создание отправки надстройки](manage-add-on-submissions.md#create-an-add-on-submission).
5. Затем он [обновляет](update-an-add-on-submission.md) и [фиксирует](commit-an-add-on-submission.md) новую отправку в Центре разработки для Windows.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-an-add-on-submission.md), пока она не будет успешно зафиксирована.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L172-L245)]

<span id="create-flight-submission" />
## <a name="create-a-package-flight-submission"></a>Создание отправки тестового пакета

В следующем примере показано, как использовать несколько методов в API отправки Магазина Windows для создания отправки тестового пакета. Чтобы это сделать, код создает новую отправку в качестве клона последней опубликованной отправки, а затем обновляет и фиксирует клонированную отправку в Центре разработки для Windows. В частности, в примере этого кода выполняются следующие задачи:

1. Сначала код [получает данные для указанного тестового пакета](get-a-flight.md).
2. Затем он [удаляет ожидающую отправку для тестового пакета](delete-a-flight-submission.md), если она существует.
3. После этого [выполняется создание новой отправки для тестового пакета](create-a-flight-submission.md) (новая отправка — это копия последней опубликованной отправки).
4. Код передает новый пакет для отправки в хранилище BLOB-объектов Azure. Дополнительные сведения приведены в соответствующих инструкциях о передаче ZIP-архива в хранилище BLOB-объектов Azure в разделе [Создание отправки тестового пакета](manage-flight-submissions.md#create-a-package-flight-submission).
5. Затем он [обновляет](update-a-flight-submission.md) и [фиксирует](commit-a-flight-submission.md) новую отправку в Центре разработки для Windows.
6. Наконец, он периодически [проверяет состояние новой отправки](get-status-for-a-flight-submission.md), пока она не будет успешно зафиксирована.

[!code[SubmissionApi](./code/StoreServicesExamples_Submission/python/Examples.py#L251-L325)]

## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)



<!--HONumber=Dec16_HO3-->


