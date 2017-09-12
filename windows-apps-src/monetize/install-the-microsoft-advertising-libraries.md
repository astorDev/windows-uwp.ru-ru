---
author: mcleanbyron
ms.assetid: 3aeddb83-5314-447b-b294-9fc28273cd39
description: "Узнайте о том, как устанавливать Microsoft Advertising SDK."
title: "Установка Microsoft Advertising SDK"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, реклама, рекламные объявления, установка, SDK, рекламная библиотека"
ms.openlocfilehash: e953b327a32bc8385cc45190e5fd11dd5acee4b8
ms.sourcegitcommit: c5c96ec4b6ccef57f69eb341b06e6280994c9767
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2017
---
# <a name="install-the-microsoft-advertising-sdk"></a>Установка Microsoft Advertising SDK

Для показа рекламы в приложениях Windows установите один из следующих пакетов SDK.

* Для приложений UWP для Windows 10 установите [Microsoft Advertising SDK](http://aka.ms/ads-sdk-uwp). Этот пакет SDK является расширением Visual Studio 2015 и последующих версий.
* Для приложений для Windows 8.1 и Windows Phone 8.x установите [Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk). Этот пакет SDK является расширением Visual Studio 2015 или Visual Studio 2013.

> [!NOTE]
> Если вы разрабатываете приложение UWP на JavaScript или HTML и установили пакет Windows 10 SDK (14393) или более новую версию, необходимо также установить библиотеку WinJS. Эта библиотека ранее включалась в предыдущие версии SDK для Windows 10, но начиная с пакета SDK для Windows 10 (14393) ее необходимо устанавливать отдельно. Сведения по установке WinJS см. в разделе [Скачать WinJS](http://try.buildwinjs.com/download/GetWinJS/).

<span id="install-msi" />
## <a name="install-via-msi"></a>Установка с помощью MSI

Установка Microsoft Advertising SDK с помощью установщика MSI.

1.  Закройте все экземпляры Visual Studio.

2. Если вы ранее устанавливали какую-либо из предыдущих версий пакетов Microsoft Advertising SDK, Universal Ad Client SDK, расширения Ad Mediator или Microsoft Store Engagement and Monetization SDK, теперь необходимо удалить эти версии пакетов SDK. Если есть такая необходимость, откройте окно **командной строки** и выполните эти команды для удаления всех более ранних версий пакетов рекламных SDK, которые могли быть установлены вместе с Visual Studio, но, возможно, не отображаются в списке установленных программ на компьютере:
  ```
  MsiExec.exe /x{5C87A4DB-31C7-465E-9356-71B485B69EC8}
  MsiExec.exe /x{6AB13C21-C3EC-46E1-8009-6FD5EBEE515B}
  MsiExec.exe /x{6AC81125-8485-463D-9352-3F35A2508C11}
  ```

3.  Скачайте и установите [Microsoft Advertising SDK](http://aka.ms/ads-sdk-uwp) (для приложений UWP для Windows 10) или [Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk) (для приложений на XAML и JavaScript или HTML для Windows 8.1 и Windows Phone 8.x). Установка может занять несколько минут. Обязательно дождитесь завершения процесса.

4.  Перезапустите Visual Studio.

5.  Если у вас есть существующий проект, который ссылается на рекламные библиотеки из какой-либо более ранней версии пакетов Microsoft Advertising SDK, Universal Ad Client SDK или Microsoft Store Engagement and Monetization SDK, рекомендуется открыть этот проект в Visual Studio, очистить и заново собрать проект (в **Обозревателе решений** щелкните правой кнопкой мыши по узлу проекта и выберите пункт **Очистить**, затем снова щелкните правой кнопкой мыши по узлу проекта и выберите **Пересобрать**).

  В противном случае, если вы используете пакет Microsoft Advertising SDK в первый раз в своем проекте, теперь вы готовы [добавить ссылку на Microsoft Advertising SDK](#reference).

<span id="install-nuget" />
## <a name="install-via-nuget-uwp-only"></a>Установка с помощью NuGet (только UWP)

Установка библиотек Microsoft Advertising SDK в конкретный проект UWP с помощью NuGet.

1.  Закройте все экземпляры Visual Studio.

2.  Если вы ранее устанавливали какую-либо из предыдущих версий пакетов Microsoft Advertising SDK, Universal Ad Client SDK, расширения Ad Mediator или Microsoft Store Engagement and Monetization SDK, теперь необходимо удалить эти версии пакетов SDK. Другой вариант: откройте окно **командной строки** и выполните эти команды для удаления всех более ранних версий пакетов рекламных SDK, которые могли быть установлены вместе с Visual Studio, но, возможно, не отображаются в списке установленных программ на компьютере:
  ```
  MsiExec.exe /x{5C87A4DB-31C7-465E-9356-71B485B69EC8}
  MsiExec.exe /x{6AB13C21-C3EC-46E1-8009-6FD5EBEE515B}
  MsiExec.exe /x{6AC81125-8485-463D-9352-3F35A2508C11}
  ```

3.  Запустите Visual Studio и откройте проект, в котором вы хотите использовать библиотеки Microsoft Advertising SDK.
    > [!NOTE]
    > Если ваш проект уже содержит ссылки на библиотеки из предыдущей установки пакета SDK с помощью MSI, удалите эти ссылки из проекта. Рядом с этими ссылками будут расположены предупреждающие значки, поскольку библиотеки, на которые они ссылаются, были удалены ранее.

4. В Visual Studio щелкните **Проект** и выберите параметр **Управление пакетами NuGet**.

5. В поле поиска введите **Microsoft.Advertising.XAML** (для проектов на XAML) или **Microsoft.Advertising.JS** (для проектов на JavaScript и HTML) и установите соответствующий пакет. Когда установка пакета завершится, сохраните решение.
    > [!NOTE]
    > Если в окне **Вывод** содержится ошибка *Install-Package*, указывающая, что заданный путь слишком длинный, вам может потребоваться настроить NuGet, чтобы извлечь пакеты в альтернативное расположение с более коротким путем, чем расположение по умолчанию. Для этого добавьте значение ```repositoryPath``` в файл nuget.config на компьютере и задайте ему более короткий путь к папке, куда можно извлечь пакеты NuGet. Дополнительные сведения см. в [этой статье](http://docs.nuget.org/ndocs/consume-packages/configuring-nuget-behavior) в документации NuGet. Кроме того можно попробовать переместить проект Visual Studio в другую папку с более коротким путем.

6. Закройте решение и снова откройте его.

7.  Если проект уже содержит ссылки на библиотеки из более ранней версии Microsoft Advertising SDK, которая была установлена с помощью NuGet, и вы обновили свой проект до более нового выпуска SDK, рекомендуется очистить и пересобрать проект (в **Обозревателе решений** щелкните правой кнопкой мыши по узлу проекта и выберите пункт **Очистить**, затем снова щелкните правой кнопкой мыши по узлу проекта и выберите **Пересобрать**).

  В противном случае, если вы используете пакет SDK в первый раз в своем проекте, теперь вы готовы [добавить ссылку на Microsoft Advertising SDK](#reference).

<span id="reference" />
## <a name="add-a-reference-to-the-microsoft-advertising-sdk"></a>Добавление ссылки на Microsoft Advertising SDK

После установки пакета Microsoft Advertising SDK следуйте этим инструкциям, чтобы создать ссылку на SDK в своем проекте и иметь возможность использовать рекламные API-интерфейсы.

1. Откройте проект в Visual Studio.
    > [!NOTE]
    > Если ваш проект направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например, **x86**). Если ваш проект направлен на работу на **Любом ЦП**, вам не удастся надлежащим образом добавить ссылку на Microsoft Advertising SDK в приведенных ниже шагах. Дополнительные сведения см. в разделе [Ошибки, вызванные указанием варианта "Любой ЦП" как целевого в вашем проекте](known-issues-for-the-advertising-libraries.md#reference_errors).

2. В **Обозревателе решений** щелкните правой кнопкой мыши пункт **Ссылки** и выберите **Добавить ссылку...**

3. В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта:

    -   Для проекта универсальной платформы Windows (UWP): разверните **Universal Windows**, нажмите кнопку **Расширения** и затем установите флажок рядом с **Microsoft Advertising SDK для XAML** (для приложений, использующих XAML) или **Microsoft Advertising SDK для JavaScript** (для приложений, созданных с помощью JavaScript и HTML).

    -   Для проекта Windows 8.1: разверните **Windows 8.1**, нажмите кнопку **Расширения** и затем установите флажок рядом с **SDK Ad Mediator для Windows 8.1 XAML** (для приложений, использующих XAML) или **Microsoft Advertising SDK для Windows 8.1 (JS)** (для приложений, созданных с помощью JavaScript и HTML).

    -   Для проекта Windows Phone 8.1: разверните **Windows Phone 8.1**, нажмите кнопку **Расширения** и затем установите флажок рядом с **SDK Ad Mediator для Windows Phone 8.1 XAML** (для приложений, использующих XAML) или **Microsoft Advertising SDK для Windows Phone 8.1 (JS)** (для приложений, созданных с помощью JavaScript и HTML).

4.  В **Диспетчере ссылок** нажмите "ОК".

Руководства, в которых рассказывается, как приступить к использованию API рекламы, см. в следующих статьях.

* [Межстраничные объявления](interstitial-ads.md)
* [Собственные объявления](native-ads.md)
* [AdControl в XAML и .NET](adcontrol-in-xaml-and--net.md)
* [AdControl в HTML 5 и JavaScript](adcontrol-in-html-5-and-javascript.md)

<span id="framework" />
## <a name="understanding-framework-packages-in-the-microsoft-advertising-sdk-uwp-only"></a>Общие сведения о пакетах платформы в Microsoft Advertising SDK (только UWP)

Библиотека Microsoft.Advertising.dll в [Microsoft Advertising SDK](http://aka.ms/ads-sdk-uwp) (только UWP) настроена как *пакет платформы*. Эта библиотека содержит рекламные API-интерфейсы в пространствах имен [Microsoft.Advertising](https://msdn.microsoft.com/library/windows/apps/mt313187.aspx) и [Microsoft.Advertising.WinRT.UI](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.aspx).

Поскольку библиотека представляет собой пакет платформы, это означает, что после установки пользователем версии вашего приложения, которое применяет эту библиотеку, библиотека будет автоматически обновляться на устройстве пользователя через Центр обновления Windows, когда мы опубликуем новую версию библиотеки с исправлениями и улучшенной производительностью. Это позволяет гарантировать, что ваши клиенты всегда будут иметь последнюю доступную версию библиотеки на своих устройствах.

Если мы выпустим новую версию SDK с новыми API или функциями в этой библиотеке, вам придется установить последнюю версию пакета SDK, чтобы использовать их. В этом случае вам также понадобится опубликовать обновленное приложение в Магазине.
