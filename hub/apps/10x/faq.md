---
Description: Ознакомьтесь с ответами на некоторые основные вопросы разработчиков о Windows 10 раз.
title: Вопросы и ответы для разработчиков Windows 10 раз
ms.topic: article
ms.date: 06/02/2020
ms.localizationpriority: medium
ms.author: quradic
author: QuinnRadich
ms.openlocfilehash: 3ba14e33c098d3515522a9a5907065751fafba87
ms.sourcegitcommit: 13bda6040988461a61b1b5561fde2f7a54835ccd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84318240"
---
# <a name="windows-10x-developer-faq"></a>Вопросы и ответы для разработчиков Windows 10 раз

> [!IMPORTANT]
> Недавно мы объявили о некоторых изменениях в определении приоритетов для Windows 10 и Windows 10 раз.
> Эти объявления включают изменения приоритетов форм 10 раз Windows. [Дополнительные сведения см. здесь.](https://blogs.windows.com/windowsexperience/2020/05/04/accelerating-innovation-in-windows-10-to-meet-customers-where-they-are/)

Windows 10 раз — это линейка продуктов в семействе Windows, оптимизированных для использования на устройствах с двумя экранами. Как разработчик, вы можете достичь более широкой аудитории, оптимизируя свое приложение для Windows 10 раз, используя преимущества новых функций, предназначенных для мобильных и двухпроцессорных аудиторий, сохраняя при этом широкие возможности Windows 10 и широкую поддержку настольных систем. [Мы объявили о Windows 10 раз в конце 2019](https://blogs.windows.com/windowsexperience/2019/10/02/introducing-windows-10x-enabling-dual-screen-pcs-in-2020/#6qxkItE2XMPu24uw.97), и мы надеемся выпустить его в конце 2020.

![Устройства под 10 раз Windows](images/windows-10x-devices.png)
 
*[Отображается Предварительная версия продукта, экраны имитации и могут быть изменены]*

Дополнительные сведения о создании двухъядерных интерфейсов и 10 раз Windows см. в статье о виртуальных сеансах [Microsoft 365](https://developer.microsoft.com/microsoft-365/virtual-events)разработчика или на [двух экранах](https://docs.microsoft.com/dual-screen/). Для получения подробных сведений ниже приведены ответы на некоторые вопросы, которые могут возникнуть.

### <a name="how-is-this-different-from-developing-for-windows-10"></a>Как это отличается от разработки для Windows 10?

Для большинства приложений это не отличается. Написание приложений Windows 10 раз поддерживается с помощью пакета SDK для Windows 10. Как выражение Windows 10, Windows 10 раз поддерживает интерфейсы API среда выполнения Windows (WinRT) и запускает приложения Win32 с помощью собственного контейнера. Затем можно вызывать новые API, специально предназначенные для устройств с двумя экранами, из новых или существующих приложений UWP или Win32, что позволяет им получить доступ к функциям и преимуществам этой новой платформы.

### <a name="does-this-replace-desktop-windows-10"></a>Заменяет ли это настольный компьютер Windows 10?

Нет. Windows 10 раз будет выпускаться параллельно с настольными выпусками Windows 10. Настольные выпуски Windows 10 продолжают предоставлять усовершенствования и улучшения в истории современных настольных приложений. Windows 10 раз — это еще одна платформа, оптимизированная для поддержки двух экранных платформ.

### <a name="when-will-windows-10x-be-released"></a>Когда будет выпущена Windows 10 раз?

Windows 10 раз будет выпущена для работы с Neoом Surface и другими сторонними устройствами с двумя экранами в конце 2020.

### <a name="when-can-i-start-development-for-windows-10x"></a>Когда можно начать разработку для Windows 10 раз?

Вы можете скачать [эмулятор Microsoft Emulator и образ эмулятора Windows 10 раз](https://docs.microsoft.com/dual-screen/windows/get-dev-tools) уже сегодня. Мы будем улучшать этот эмулятор и дополнять его поддержкой других устройств с поддержкой 10 раз Windows. Эти Эмуляторы в сочетании с предварительными версиями Windows SDK позволяют разрабатывать для Windows 10 раз до того, как первое устройство с двумя экранами будет открыто общедоступным.

### <a name="will-my-universal-windows-platform-uwp-apps-run-on-windows-10x"></a>Будут ли приложения универсальная платформа Windows (UWP) запускаться в Windows 10 раз?

Большинство приложений UWP полностью поддерживаются в Windows 10 раз и работают на устройствах под Windows 10 раз без каких бы то ни было изменений. Поддерживаются все API-интерфейсы WinRT, как и большинство других функций, к которым имеют доступ приложения UWP. По мере продолжения разработки предварительной версии мы выпустим документацию с подробными сведениями о других неподдерживаемых функциях.

### <a name="will-my-win32-apps-run-on-windows-10x"></a>Будут ли приложения Win32 запускаться в Windows 10 раз?

Windows 10 раз предоставляет встроенную поддержку для запуска приложений Win32 в автономной среде. Большинство приложений Win32 можно запускать и отлаживать на устройстве Windows 10 раз без инцидентов. Кроме того, можно использовать новые API-интерфейсы Win32 для добавления в приложение поддержки с двумя экранами.

### <a name="are-there-any-features-of-my-app-that-wont-work-on-windows-10x"></a>Есть ли функции моего приложения, которые не работают в Windows 10 раз?

По мере того как Windows 10 раз пройдет разработку предварительной версии, мы выделим документацию, выделяя определенные ограничения. Однако автономная среда, используемая для запуска приложений Win32, не включает оболочку Windows, поэтому расширения оболочки и аналогичные функции не поддерживаются. Аналогично, устройства Windows 10 раз не поддерживают интерфейсы API, связанные с определенными параметрами системы, например параметры использования питания.

### <a name="if-i-enhance-my-app-with-windows-10x-features-will-it-still-run-on-devices-running-desktop-windows-10"></a>Если я улучшаю приложение с помощью функций Windows 10 раз, будет ли он работать на устройствах под управлением Windows 10 для настольных ПК?

Приложения, разработанные для Windows 10 раз, по-прежнему работают на устройствах под управлением классической версии Windows 10, хотя эти новые API Windows не будут добавляться в настольные версии Windows 10 до следующего обновления основной версии. Точно так же, как если бы вы разработали приложение, поддерживаемое на нескольких версиях настольных систем Windows 10, следуйте [рекомендациям адаптивного программирования](https://docs.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code) , чтобы обеспечить корректное функционирование приложения в 10 раз и настольных системах Windows 10. 
