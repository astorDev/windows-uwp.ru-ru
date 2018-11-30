---
description: Используйте приведенные в этом разделе примеры кода на C#, чтобы подробнее ознакомиться с использованием API отправки в Microsoft Store для отправки трейлеров и параметров игры.
title: 'Пример на языке C#: отправка приложения с трейлерами и параметрами игры'
ms.date: 07/10/2017
ms.topic: article
keywords: windows 10, uwp, API отправки в Microsoft Store, примеры кода, параметры игры, трейлеры, дополнительные предложения, C#
ms.localizationpriority: medium
ms.openlocfilehash: 041f07fd6b24af3658bc9cfffe07117e4b353831
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8211925"
---
# <a name="c-sample-app-submission-with-game-options-and-trailers"></a>Пример на языке C\#: отправка приложения с трейлерами и параметрами игры

В этой статье представлены примеры кода на C# по использованию [API отправки в Microsoft Store](create-and-manage-submissions-using-windows-store-services.md) для решения этих задач.

* Получите маркер доступа Azure AD для использования с API отправки в Microsoft Store.
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

Класс ```CreateAndSubmitSubmissionExample``` определяет открытый метод ```Execute```, который вызывает другие методы из примера по использованию API отправки в Microsoft Store для создания и передачи отправки приложения, содержащей трейлер и параметры игры. Адаптация кода для собственного использования.

* Назначьте переменной ```tenantId``` идентификатор арендатора для своего приложения и назначьте переменным ```clientId``` и ```clientSecret``` идентификатор и ключ клиента для своего приложения. Дополнительные сведения см. в разделе [как привязать приложение Azure AD с учетной записью центра партнеров](create-and-manage-submissions-using-windows-store-services.md#how-to-associate-an-azure-ad-application-with-your-partner-center-account)
* Назначьте переменной ```applicationId``` [код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для приложения, отправку которого необходимо создать.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/cs/CreateAndSubmitSubmissionExample.cs#CreateAndSubmitSubmissionExample)]

<span id="token" />

## <a name="obtain-an-azure-ad-access-token"></a>Получение маркера доступа Azure AD

Класс ```DevCenterAccessTokenClient``` определяет вспомогательный метод, который использует ваши значения ```tenantId```, ```clientId``` и ```clientSecret``` для создания маркера доступа Azure AD для использования с API отправки в Microsoft Store.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/cs/DevCenterAccessTokenClient.cs#DevCenterAccessTokenClient)]

<span id="utilities" />

## <a name="helper-methods-to-invoke-the-submission-api-and-upload-submission-files"></a>Вспомогательные методы для вызова API отправки и передачи файлов отправки

Класс ```DevCenterClient``` определяет вспомогательные методы, которые вызывают различные методы в API отправки в Microsoft Store и выкладывают ZIP-файл, содержащий пакеты, изображения для описания и файлы трейлера для отправки приложения.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/cs/DevCenterClient.cs#DevCenterClient)]

## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
