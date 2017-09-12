---
author: mcleanbyron
description: "Используйте приведенные в этом разделе примеры кода на Python, чтобы подробнее ознакомиться с использованием API отправки в Магазин Windows для отправки трейлеров и параметров игры."
title: "Пример на языке Python — отправка приложения с трейлерами и параметрами игры"
ms.author: mcleans
ms.date: 07/10/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, API отправки в Магазин Windows, примеры кода, параметры игры, трейлеры, дополнительные предложения, python"
ms.openlocfilehash: 225d6721760737388c0a14a3a63cc0c9608d53a9
ms.sourcegitcommit: a7a1b41c7dce6d56250ce3113137391d65d9e401
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="python-sample-app-submission-with-game-options-and-trailers"></a>Пример на языке Python: отправка приложения с трейлерами и параметрами игры

В этой статье представлены примеры кода на Python по использованию [API отправки в Магазин Windows](create-and-manage-submissions-using-windows-store-services.md) для решения этих задач.

* Получите маркер доступа Azure AD для использования с API отправки в Магазин Windows.
* Создание отправки приложения
* Настройте данные описания в Магазине для отправки приложения, в том числе дополнительные параметры описания для [игр](manage-app-submissions.md#gaming-options-object) и [трейлеров](manage-app-submissions.md#trailer-object).
* Выложите ZIP-файл, содержащий пакеты, изображения для описания и файлы трейлера для отправки приложения.
* Передайте отправку приложения.

<span id="create-app-submission" />
## <a name="create-an-app-submission"></a>Создание отправки приложения

Этот код вызывает другие класса и функции из примера по использованию API отправки в Магазин Windows для создания и передачи отправки приложения, содержащей трейлер и параметры игры. Адаптация кода для собственного использования.

* Назначьте переменной ```tenant``` идентификатор арендатора для своего приложения и назначьте переменным ```client``` и ```secret``` идентификатор и ключ клиента для своего приложения. Дополнительные сведения см. в разделе [Привязка приложения Azure AD к учетной записи в Центре разработки для Windows](create-and-manage-submissions-using-windows-store-services.md#how-to-associate-an-azure-ad-application-with-your-windows-dev-center-account).
* Назначьте переменной ```application_id``` [код продукта в Магазине](in-app-purchases-and-trials.md#store_ids) для приложения, отправку которого необходимо создать.

> [!div class="tabbedCodeSnippets"]
[!code[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/python/CreateAndSubmitAppSubmissionExample.py#L1-L74)]

<span id="token" />
## <a name="obtain-an-azure-ad-access-token-and-invoke-the-submission-api"></a>Получите маркер доступа Azure AD и вызовите API отправки

В следующем примере определяются следующие классы:

* Класс ```DevCenterAccessTokenClient``` определяет вспомогательный метод, который использует ваши значения ```tenantId```, ```clientId``` и ```clientSecret``` для создания маркера доступа Azure AD для использования с API отправки в Магазин Windows.
* Класс ```DevCenterClient``` определяет вспомогательные методы, которые вызывают различные методы в API отправки в Магазин Windows и выкладывают ZIP-файл, содержащий пакеты, изображения для описания и файлы трейлера для отправки приложения.

> [!div class="tabbedCodeSnippets"]
[!code[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/python/devcenterclient.py#L1-L126)]

<span id="token" />
## <a name="get-app-submission-listing-data"></a>Получение данных описания отправки приложения

В следующем примере определяются вспомогательные функции, которые возвращают данные описания в формате JSON для примера новой отправки приложения.

> [!div class="tabbedCodeSnippets"]
[!code[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/python/submissiondatasamples.py#L1-L170)]

## <a name="related-topics"></a>Еще по теме:

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
