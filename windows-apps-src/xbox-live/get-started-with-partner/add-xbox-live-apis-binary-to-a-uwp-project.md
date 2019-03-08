---
title: Добавление Xbox Live интерфейсов API двоичных проекта UWP
description: Узнайте, как использовать NuGet для добавления API-интерфейсы Live Xbox двоичный пакет в проект универсальной платформы Windows.
ms.assetid: 1e77ce9f-8a0e-402c-9f46-e37f9cda90ed
ms.date: 11/28/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, nuget
ms.localizationpriority: medium
ms.openlocfilehash: 20b4d4ae27282ccf71964d31da4d1f577c280de8
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57593949"
---
# <a name="add-xbox-live-apis-binary-package-to-your-uwp-project"></a>Добавление API-интерфейсы Live Xbox двоичный пакет в проект универсальной платформы Windows

## <a name="requirements"></a>Требования

2. **[Windows 10](https://microsoft.com/windows)**.
3. **[Visual Studio](https://www.visualstudio.com/)**. Приложения универсальной платформы Windows можно создавать с помощью Visual Studio 2015 с обновлением 3 или более поздней версии. Мы рекомендуем использовать последний выпуск Visual Studio для разработчиков и обновления системы безопасности.
4. **[Пакет SDK для Windows 10](https://developer.microsoft.com/windows/downloads/windows-10-sdk) v10.0.10586.0** или более поздней версии.

## <a name="add-the-binary-package-via-nuget"></a>Добавление двоичный пакет с помощью NuGet

Чтобы использовать API Xbox Live из проекта, можно либо добавить ссылки на двоичные файлы, с помощью пакетов NuGet или добавлении источника API. Добавление пакетов NuGet ускоряет компиляцию, а добавление источника упрощает отладку. В этой статье поможет выполнить с помощью пакетов NuGet. Если вы хотите использовать источник, обратитесь к [компиляции Xbox Live API-интерфейсы источника в ваш проект UWP](add-xbox-live-apis-source-to-a-uwp-project.md).

API службы Xbox имеет вариации для универсальной платформы Windows и XDK, а также для C++ и WinRT и их пространство имен структурированные как **Microsoft.Xbox.Live.SDK.*. UWP** и **Microsoft.Xbox.Live.SDK.*. XboxOneXDK**.

1. **UWP** предназначен для разработчиков, работающих над игрой UWP, которая может выполняться на ПК, Xbox One или Windows Phone.
2. **XboxOneXDK** для ID@Xbox и управляемых разработчиков, использующих один XDK Xbox.
3. Пакет SDK C++ можно использовать для C++ игровых движков, а пакет SDK WinRT для игровых движков, написанный на языке C++, C#, или JavaScript.
4. При использовании WinRT с обработчиком C++, следует использовать C + +/ CX, использующего шляпы (^). C++ является рекомендованным интерфейсом API для игровых движков C++.  

> [!TIP]
> Дополнительные сведения об универсальной платформы Windows на Xbox One в [Приступая к работе с разработкой приложений для универсальной платформы Windows в службе Xbox One](https://docs.microsoft.com/windows/uwp/xbox-apps/getting-started).

Можно добавить пакет Xbox Live SDK NuGet для по:

1. В Visual Studio перейдите в раздел **средства** > **диспетчер пакетов NuGet** > **управление пакетами NuGet для решения...** .
2. В диспетчере пакетов NuGet, щелкните **Обзор** и введите **Xbox.Live.SDK** в поле поиска.
3. Выберите версию пакета SDK Xbox Live, вы хотите использовать в списке слева.
3. В правой части окна установите флажок рядом с проектом и нажмите кнопку **установить**.

> [!NOTE]
> Разработчики для Xbox Live Creators Program необходимо использовать одну из версий универсальной платформы Windows, Xbox Live пакета SDK, как XDK не поддерживается.

![Добавление XBL с помощью NuGet](../images/getting_started/vs-add-nuget-xbl.gif)

> [!IMPORTANT]
> Для `Microsoft.Xbox.Live.SDK.Cpp.*` проектов на основе, не забудьте включить заголовок `#include <xsapi\services.h>` в исходном проекте.
