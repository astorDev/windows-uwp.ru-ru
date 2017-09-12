---
author: mcleanbyron
description: "Используйте приведенные в этом разделе примеры кода на Java, чтобы подробнее ознакомиться с использованием API отправки в Магазин Windows для отправки трейлеров и параметров игры."
title: "Пример на языке Java — отправка приложения с трейлерами и параметрами игры"
ms.author: mcleans
ms.date: 07/10/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, API отправки в Магазин Windows, примеры кода, параметры игры, трейлеры, дополнительные предложения, java"
ms.openlocfilehash: 7112380e8bad9fc4e2255fd10d06fd86150e92c4
ms.sourcegitcommit: a7a1b41c7dce6d56250ce3113137391d65d9e401
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="java-sample-app-submission-with-game-options-and-trailers"></a>Пример на языке Java: отправка приложения с трейлерами и параметрами игры

В этой статье представлены примеры кода на Java по использованию [API отправки в Магазин Windows](create-and-manage-submissions-using-windows-store-services.md) для решения этих задач.

* Получите маркер доступа Azure AD для использования с API отправки в Магазин Windows.
* Создание отправки приложения
* Настройте данные описания в Магазине для отправки приложения, в том числе дополнительные параметры описания для [игр](manage-app-submissions.md#gaming-options-object) и [трейлеров](manage-app-submissions.md#trailer-object).
* Выложите ZIP-файл, содержащий пакеты, изображения для описания и файлы трейлера для отправки приложения.
* Передайте отправку приложения.

<span id="create-app-submission" />
## <a name="create-an-app-submission"></a>Создание отправки приложения

Класс ```CreateAndSubmitSubmissionExample``` реализует программу ```main```, которая вызывает другие методы из примера по использованию API отправки в Магазин Windows для создания и передачи отправки приложения, содержащей трейлер и параметры игры. Адаптация кода для собственного использования.

* Назначьте переменной ```tenantId``` идентификатор арендатора для своего приложения и назначьте переменным ```clientId``` и ```clientSecret``` идентификатор и ключ клиента для своего приложения. Дополнительные сведения см. в разделе [Привязка приложения Azure AD к учетной записи в Центре разработки для Windows](create-and-manage-submissions-using-windows-store-services.md#how-to-associate-an-azure-ad-application-with-your-windows-dev-center-account).
* Назначьте переменной ```applicationId``` [код продукта в Магазине](in-app-purchases-and-trials.md#store_ids) для приложения, отправку которого необходимо создать.

> [!div class="tabbedCodeSnippets"]
[!code[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/java/CreateAndSubmitSubmissionExample.java#L1-L313)]

<span id="token" />
## <a name="obtain-an-azure-ad-access-token"></a>Получение маркера доступа Azure AD

Класс ```DevCenterAccessTokenClient``` определяет вспомогательный метод, который использует ваши значения ```tenantId```, ```clientId``` и ```clientSecret``` для создания маркера доступа Azure AD для использования с API отправки в Магазин Windows.

> [!div class="tabbedCodeSnippets"]
[!code[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/java/DevCenterAccessTokenClient.java#L1-L69)]

<span id="utilities" />
## <a name="helper-methods-to-invoke-the-submission-api-and-upload-submission-files"></a>Вспомогательные методы для вызова API отправки и передачи файлов отправки

Класс ```DevCenterClient``` определяет вспомогательные методы, которые вызывают различные методы в API отправки в Магазин Windows и выкладывают ZIP-файл, содержащий пакеты, изображения для описания и файлы трейлера для отправки приложения.

> [!div class="tabbedCodeSnippets"]
[!code[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/java/DevCenterClient.java#L1-L224)]

## <a name="related-topics"></a>Еще по теме:

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
