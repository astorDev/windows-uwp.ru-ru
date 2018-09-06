---
author: jnHs
Description: Review this list to help avoid issues that frequently prevent apps from getting certified, or that might be identified during a spot check after the app is published.
title: Недопущение распространенных ошибок при сертификации
ms.assetid: 9E9E3841-2F9B-42D4-B5F8-4C7C31E42E3D
ms.author: wdg-dev-content
ms.date: 06/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 283856ad163d2e67078c61559f6f8ec667e92b87
ms.sourcegitcommit: 914b38559852aaefe7e9468f6f53a7465bf36e30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2018
ms.locfileid: "3398646"
---
# <a name="avoid-common-certification-failures"></a>Недопущение распространенных ошибок при сертификации


Изучите этот список, чтобы избежать часто возникающих проблем, из-за которых приложения не проходят сертификацию, а также тех проблем, которые могут быть обнаружены при выборочной проверке после публикации приложения.

> [!NOTE]
> Обязательно изучите [Политик Microsoft Store](https://docs.microsoft.com/legal/windows/agreements/store-policies) , чтобы убедиться, что ваше приложение соответствует всем требованиям, перечисленным существует.

-   Отправляйте приложение, только когда оно закончено. Вы можете упоминать о планируемых возможностях в описании приложения, однако необходимо исключить из приложения незавершенные разделы, ссылки на веб-страницы, находящиеся в разработке, и прочие элементы, которые вызовут у пользователя ощущение незаконченности приложения.

-   Перед тем как отправить приложение, [протестируйте его с помощью комплекта сертификации приложений для Windows](../debug-test-perf/windows-app-certification-kit.md).

-   Проверьте работу приложения в нескольких разных конфигурациях, чтобы убедиться, что оно работает максимально стабильно.

-   Проверьте, чтобы ваше приложение не завершается аварийно при отсутствии подключения к сети. Даже если подключение необходимо для работы приложения, оно должно корректно обрабатывать ситуацию с отсутствием подключения.

-   [Предоставьте всю информацию](notes-for-certification.md), необходимую для использования приложения, в том числе имя пользователя и пароль тестовой учетной записи, если приложение должно выполнять вход в сетевую службу, а также описание всех действий, необходимых для доступа к скрытым или заблокированным возможностям.

-   Добавьте [политику конфиденциальности](create-app-store-listings.md#privacy-policy), если она необходима для приложения. Например, если ваше приложение любым образом получает доступ к любым личным сведениям или если это предусмотрено законом. Чтобы определить, необходима ли вашему приложению политика конфиденциальности, ознакомьтесь с [Соглашением с разработчиком приложений](https://docs.microsoft.com/legal/windows/agreements/app-developer-agreement) и [Политик Microsoft Store](https://docs.microsoft.com/legal/windows/agreements/store-policies).

-   Убедитесь, что описание приложения четко отражает его функции. В этом вам поможет руководство по [созданию эффективного описания приложения](write-a-great-app-description.md).

-   Предоставьте полные и точные ответы на все вопросы в разделе [Возрастные категории](age-ratings.md).

-   Не [объявляйте приложение поддерживающим специальные возможности](app-declarations.md#this-app-has-been-tested-to-meet-accessibility-guidelines), если вы не спроектировали его таким намеренно и не проверили его в сценариях использования специальных возможностей.

-   Если в приложении используются коммерческие API Магазина Windows из пространства имен [**Windows.ApplicationModel.Store**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store), обязательно протестируйте приложение и удостоверьтесь в том, что обрабатываются типичные исключения. Кроме того, убедитесь, что в приложении используется класс [**CurrentApp**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp), а не класс [**CurrentAppSimulator**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentAppSimulator), который предназначен только для тестирования. (Обратите внимание, что если ваше приложение предназначено для Windows 10 версии 1607 или более поздней, для управления покупками из приложения рекомендуется использовать элементы, входящие в пространство имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store), а не Windows.ApplicationModel.Store.)


 

 




