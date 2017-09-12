---
author: mcleanbyron
description: "Используйте приведенные в этом разделе примеры кода на C#, чтобы подробнее ознакомиться с использованием API отправки в Магазин Windows для отправки трейлеров и параметров игры."
title: "Пример на языке C#: отправка приложения с трейлерами и параметрами игры"
ms.author: mcleans
ms.date: 07/10/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, API отправки в Магазин Windows, примеры кода, параметры игры, трейлеры, дополнительные предложения, C#"
ms.openlocfilehash: 2020d280d7b4ffa3cede11b089f808920ddf46b9
ms.sourcegitcommit: a7a1b41c7dce6d56250ce3113137391d65d9e401
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="c-sample-app-submission-with-game-options-and-trailers"></a>Пример на языке C\#: отправка приложения с трейлерами и параметрами игры

В этой статье представлены примеры кода на C# по использованию [API отправки в Магазин Windows](create-and-manage-submissions-using-windows-store-services.md) для решения этих задач.

* Получите маркер доступа Azure AD для использования с API отправки в Магазин Windows.
* Создание отправки приложения
* Настройте данные описания в Магазине для отправки приложения, в том числе дополнительные параметры описания для [игр](manage-app-submissions.md#gaming-options-object) и [трейлеров](manage-app-submissions.md#trailer-object).
* Выложите ZIP-файл, содержащий пакеты, изображения для описания и файлы трейлера для отправки приложения.
* Передайте отправку приложения.

Вы можете ознакомиться с каждым примером, чтобы подробнее узнать о демонстрируемой в нем задаче, либо вы можете собрать все примеры кода в этой статье в консольное приложение. Для сборки примеров создайте консольное приложение C# с именем **DevCenterApiSample** в Visual Studio, скопируйте каждый пример в отдельный файл с кодом в проекте и соберите проект.

## <a name="prerequisites"></a>Что вам понадобится

Для этих примеров необходимо выполнение следующих требований.

* Добавьте ссылку на сборку System.Web в свой проект.
* Установите пакет NuGet [Newtonsoft.Json](http://www.newtonsoft.com/json) от Newtonsoft в свой проект.

<span id="create-app-submission" />
## <a name="create-an-app-submission"></a>Создание отправки приложения

Класс ```CreateAndSubmitSubmissionExample``` определяет открытый метод ```Execute```, который вызывает другие методы из примера по использованию API отправки в Магазин Windows для создания и передачи отправки приложения, содержащей трейлер и параметры игры. Адаптация кода для собственного использования.

* Назначьте переменной ```tenantId``` идентификатор арендатора для своего приложения и назначьте переменным ```clientId``` и ```clientSecret``` идентификатор и ключ клиента для своего приложения. Дополнительные сведения см. в разделе [Привязка приложения Azure AD к учетной записи в Центре разработки для Windows](create-and-manage-submissions-using-windows-store-services.md#how-to-associate-an-azure-ad-application-with-your-windows-dev-center-account).
* Назначьте переменной ```applicationId``` [код продукта в Магазине](in-app-purchases-and-trials.md#store_ids) для приложения, отправку которого необходимо создать.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/cs/CreateAndSubmitSubmissionExample.cs#CreateAndSubmitSubmissionExample)]

<span id="token" />
## <a name="obtain-an-azure-ad-access-token"></a>Получение маркера доступа Azure AD

Класс ```DevCenterAccessTokenClient``` определяет вспомогательный метод, который использует ваши значения ```tenantId```, ```clientId``` и ```clientSecret``` для создания маркера доступа Azure AD для использования с API отправки в Магазин Windows.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/cs/DevCenterAccessTokenClient.cs#DevCenterAccessTokenClient)]

<span id="utilities" />
## <a name="helper-methods-to-invoke-the-submission-api-and-upload-submission-files"></a>Вспомогательные методы для вызова API отправки и передачи файлов отправки

Класс ```DevCenterClient``` определяет вспомогательные методы, которые вызывают различные методы в API отправки в Магазин Windows и выкладывают ZIP-файл, содержащий пакеты, изображения для описания и файлы трейлера для отправки приложения.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/cs/DevCenterClient.cs#DevCenterClient)]

## <a name="related-topics"></a>Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
