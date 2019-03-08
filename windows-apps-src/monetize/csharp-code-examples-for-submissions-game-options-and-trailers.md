---
description: Используйте приведенные в этом разделе примеры кода на C#, чтобы подробнее ознакомиться с использованием API отправки в Microsoft Store для отправки трейлеров и параметров игры.
title: 'Пример на языке C#: отправка приложения с трейлерами и параметрами игры'
ms.date: 07/10/2017
ms.topic: article
keywords: windows 10, uwp, API отправки в Microsoft Store, примеры кода, параметры игры, трейлеры, дополнительные предложения, C#
ms.localizationpriority: medium
ms.openlocfilehash: 277d455fe3387452a4afe91fd74e5c2099f76ce4
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57594079"
---
# <a name="c-sample-app-submission-with-game-options-and-trailers"></a>C\# пример: Отправка приложения с помощью параметров игры и фильмов

В этой статье представлены примеры кода на C# по использованию [API отправки в Microsoft Store](create-and-manage-submissions-using-windows-store-services.md) для решения этих задач.

* Получите маркер доступа Azure AD для использования с API отправки в Microsoft Store.
* Создание отправки приложения
* Настройте данные описания в Магазине для отправки приложения, в том числе дополнительные параметры описания для [игр](manage-app-submissions.md#gaming-options-object) и [трейлеров](manage-app-submissions.md#trailer-object).
* Выложите ZIP-файл, содержащий пакеты, изображения для описания и файлы трейлера для отправки приложения.
* Передайте отправку приложения.

Вы можете ознакомиться с каждым примером, чтобы подробнее узнать о демонстрируемой в нем задаче, либо вы можете собрать все примеры кода в этой статье в консольное приложение. Для сборки примеров создайте консольное приложение C# с именем **DevCenterApiSample** в Visual Studio, скопируйте каждый пример в отдельный файл с кодом в проекте и соберите проект.

## <a name="prerequisites"></a>Предварительные условия

Для этих примеров необходимо выполнение следующих требований.

* Добавьте ссылку на сборку System.Web в свой проект.
* Установите пакет NuGet [Newtonsoft.Json](https://www.newtonsoft.com/json) от Newtonsoft в свой проект.

<span id="create-app-submission" />

## <a name="create-an-app-submission"></a>Создание отправки приложения

Класс ```CreateAndSubmitSubmissionExample``` определяет открытый метод ```Execute```, который вызывает другие методы из примера по использованию API отправки в Microsoft Store для создания и передачи отправки приложения, содержащей трейлер и параметры игры. Адаптация кода для собственного использования.

* Назначьте переменной ```tenantId``` идентификатор арендатора для своего приложения и назначьте переменным ```clientId``` и ```clientSecret``` идентификатор и ключ клиента для своего приложения. Дополнительные сведения см. в разделе [как связать приложение Azure AD с помощью учетной записи центра партнеров](create-and-manage-submissions-using-windows-store-services.md#how-to-associate-an-azure-ad-application-with-your-partner-center-account)
* Назначьте переменной ```applicationId```[код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для приложения, отправку которого необходимо создать.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/cs/CreateAndSubmitSubmissionExample.cs#CreateAndSubmitSubmissionExample)]

<span id="token" />

## <a name="obtain-an-azure-ad-access-token"></a>Получение токена доступа Azure AD

Класс ```DevCenterAccessTokenClient``` определяет вспомогательный метод, который использует ваши значения ```tenantId```, ```clientId``` и ```clientSecret``` для создания маркера доступа Azure AD для использования с API отправки в Microsoft Store.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/cs/DevCenterAccessTokenClient.cs#DevCenterAccessTokenClient)]

<span id="utilities" />

## <a name="helper-methods-to-invoke-the-submission-api-and-upload-submission-files"></a>Вспомогательные методы для вызова API отправки и передачи файлов отправки

Класс ```DevCenterClient``` определяет вспомогательные методы, которые вызывают различные методы в API отправки в Microsoft Store и выкладывают ZIP-файл, содержащий пакеты, изображения для описания и файлы трейлера для отправки приложения.

> [!div class="tabbedCodeSnippets"]
[!code-cs[SubmissionApi](./code/StoreServicesExamples_SubmissionAdvancedListings/cs/DevCenterClient.cs#DevCenterClient)]

## <a name="related-topics"></a>Статьи по теме

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
