---
title: Устранение неполадок в приложениях UWP ARM32
description: Распространенные проблемы с приложениями ARM32 при работе в ARM и способы их устранения.
ms.date: 01/03/2019
ms.topic: article
keywords: windows 10 s, постоянно подключенный, приложения ARM32 в ARM, windows 10 в ARM, устранение неполадок
ms.localizationpriority: medium
ms.openlocfilehash: 6213c8c69695d160d4e6fa362aa7aa322a0326fd
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75683947"
---
# <a name="troubleshooting-arm-uwp-apps"></a>Устранение неполадок приложений UWP ARM

Если приложение UWP ARM32 или ARM64 работает неправильно на ARM, вот некоторые рекомендации, которые могут помочь.

>[!NOTE]
> Чтобы создать приложение UWP для платформы ARM64, необходимо иметь Visual Studio 2017 версии 15,9 или более поздней или Visual Studio 2019. Дополнительные сведения см. в [этой записи блога](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development).


## <a name="common-issues"></a>Общие проблемы
Ниже приведены некоторые распространенные проблемы, которые следует учитывать при устранении неполадок в приложениях ARM32 и ARM64.

### <a name="using-windows-10-mobile-only-apis-on-arm-based-processors"></a>Использование API с поддержкой только Windows 10 Mobile в процессорах на базе ARM
Приложения ARM могут столкнуться с проблемами при использовании интерфейсов API только для мобильных устройств (например, **HardwareButtons**). Чтобы решить эту проблему, можно динамически определить, выполняется ли ваше приложение в Windows 10 Mobile, прежде чем вызывать эти API. Изучите инструкции в записи в блоге [Динамическое обнаружение функций с контрактами API](https://blogs.windows.com/buildingapps/2015/09/15/dynamically-detecting-features-with-api-contracts-10-by-10/).

### <a name="including-dependencies-not-supported-by-uwp-apps"></a>Включение зависимостей, не поддерживаемых приложениями UWP
Приложения универсальная платформа Windows (UWP), которые не были правильно созданы с помощью Visual Studio и пакета SDK для UWP, могут иметь зависимости от компонентов ОС, которые недоступны для приложений ARM, работающих в системе ARM64. Примеры таких зависимостей:

- Ожидание доступности частей .NET Framework.
- Ссылка на сторонние компоненты .NET, которые несовместимы с UWP.

Эти проблемы можно устранить путем удаления недоступных зависимостей и перестроения приложения с помощью последних версий пакета SDK Microsoft Visual Studio и UWP. или в качестве последнего средства удалите приложение ARM из Microsoft Store, чтобы версия x86 приложения (если доступно) загружалась на ПК пользователей.

Дополнительные сведения об API .NET, доступных для приложений UWP, см. в разделе [.NET для приложений UWP](https://docs.microsoft.com/dotnet/api/index?view=dotnet-uwp-10.0)

### <a name="compiling-an-app-with-an-older-version-of-visual-studio-and-sdk"></a>Компиляция приложения с более старой версией Visual Studio и SDK
Если возникнут проблемы, воспользуйтесь для компиляции приложения последними версиями Microsoft Visual Studio и Windows SDK. В приложениях, скомпилированных с помощью более ранней версии Visual Studio и SDK, могут возникать проблемы, устраненные в более поздних версиях.

## <a name="debugging"></a>Отладка
Вы можете использовать существующие инструменты для разработки приложений для платформы ARM. Вот некоторые полезные ресурсы.

- Visual Studio 15.5 Preview 1 и выше поддерживает запуск приложений ARM32 в режиме универсальной аутентификации. При этом автоматически начинается начальная загрузка необходимых инструментов удаленной отладки.
- Дополнительные сведения об инструментах и стратегиях отладки в ARM см. в разделе [Отладка в ARM64](https://docs.microsoft.com/windows-hardware/drivers/debugger/debugging-arm64).
