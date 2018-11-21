---
author: Xansky
ms.assetid: cf0d2709-21a1-4d56-9341-d4897e405f5d
description: Узнайте, как перехватывать ошибки AdControl в приложении.
title: Пошаговое руководство по обработке ошибок XAML/C#
ms.author: mhopkins
ms.date: 05/11/2018
ms.topic: article
keywords: windows 10, uwp, рекламные объявления, реклама, обработка ошибок, XAML, c#
ms.localizationpriority: medium
ms.openlocfilehash: be101f5ec189d822bc9704b435f4a098b61f57ac
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7442353"
---
# <a name="error-handling-in-xamlc-walkthrough"></a>Пошаговое руководство по обработке ошибок XAML/C#

В этом руководстве рассматривается перехват связанных с рекламой ошибок в приложении. В этом руководстве используется элемент управления [AdControl](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol) для отображения баннера, но изложенные в нем общие принципы также применимы к межстраничным и собственным объявлениям.

Эти примеры составлены с учетом того, что у вас есть приложение XAML/C#, которое содержит элемент управления **AdControl**. Пошаговые инструкции по добавлению элемента **AdControl** в приложение см. в разделе [AdControl в XAML и .NET](adcontrol-in-xaml-and--net.md). 

1.  В файле MainPage.xaml найдите определение **AdControl**. Код выглядит следующим образом.
    ``` xml
    <UI:AdControl
      ApplicationId="3f83fe91-d6be-434d-a0ae-7351c5a997f1"
      AdUnitId="test"
      HorizontalAlignment="Left"
      Height="250"
      Margin="10,10,0,0"
      VerticalAlignment="Top"
      Width="300" />
    ```

2.   Назначьте имя обработчика события ошибки событию [ErrorOccurred](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.erroroccurred) после свойства **Width**, но до закрывающего тега. В этом пошаговом руководстве используется следующее имя обработчика события ошибки: **OnAdError**.
    ``` xml
    <UI:AdControl
      ApplicationId="3f83fe91-d6be-434d-a0ae-7351c5a997f1"
      AdUnitId="test"
      HorizontalAlignment="Left"
      Height="250"
      Margin="10,10,0,0"
      VerticalAlignment="Top"
      Width="300"
      ErrorOccurred="OnAdError"/>
    ```

3.  Чтобы создать ошибку во время выполнения, создайте второй элемент **AdControl** с другим ИД приложения. Поскольку все объекты **AdControl** в приложении должны использовать тот же ИД приложения, при создании дополнительного элемента **AdControl** с другим ИД приложения будет создана ошибка.

    Определите второй элемент **AdControl** в файле MainPage.xaml сразу после первого **AdControl** и задайте для свойства [ApplicationId](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.applicationid) значение 0 (нуль).
    ``` xml
    <UI:AdControl
        ApplicationId="0"
        AdUnitId="test"
        HorizontalAlignment="Left"
        Height="250"
        Margin="10,265,0,0"
        VerticalAlignment="Top"
        Width="300"
        ErrorOccurred="OnAdError" />
    ```

4.  В файле MainPage.xaml.cs добавьте в класс **MainPage** следующий обработчик событий **OnAdError**. Этот обработчик событий записывает информацию в окно **вывода** Visual Studio.
    ``` csharp
    private void OnAdError(object sender, AdErrorEventArgs e)
    {
        System.Diagnostics.Debug.WriteLine("AdControl error (" + ((AdControl)sender).Name +
            "): " + e.ErrorMessage + " ErrorCode: " + e.ErrorCode.ToString());
    }
    ```

4.  Сборка и запуск проекта. После запуска приложения отобразится сообщение, похожее на отображаемое в окне **вывода** в Visual Studio.
    ```
    AdControl error (): MicrosoftAdvertising.Shared.AdException: all ad requests must use the same application ID within a single application (0, d25517cb-12d4-4699-8bdc-52040c712cab) ErrorCode: ClientConfiguration
    ```

## <a name="related-topics"></a>Связанные разделы

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
