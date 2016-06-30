---
author: mcleanbyron
ms.assetid: cf0d2709-21a1-4d56-9341-d4897e405f5d
description: "Узнайте, как перехватывать ошибки AdControl в приложении."
title: "Пошаговое руководство по обработке ошибок XAML/C#"
translationtype: Human Translation
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: 7cb827b4269afb55f0326eec0a0ee25b93119eb0


---

# Пошаговое руководство по обработке ошибок XAML/C#


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом разделе рассматривается перехват ошибок [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) в приложении.

Эти примеры составлены с учетом того, что у вас есть приложение XAML/C#, которое содержит элемент управления **AdControl**. Пошаговые инструкции по добавлению элемента **AdControl** в приложение см. в разделе [AdControl в XAML и .NET](adcontrol-in-xaml-and--net.md). Полный пример с проектом, демонстрирующим способы добавления баннерной рекламы в приложение на XAML с использованием C# и C++, см. в разделе [примеров рекламы на GitHub](http://aka.ms/githubads).

1.  В файле MainPage.xaml найдите определение **AdControl**. Код выглядит следующим образом.

    ``` syntax
    <UI:AdControl
       ApplicationId="3f83fe91-d6be-434d-a0ae-7351c5a997f1"
       AdUnitId="10865270"
       HorizontalAlignment="Left"
       Height="250"
       Margin="10,10,0,0"
       VerticalAlignment="Top"
       Width="300" />
    ```

2.   Назначьте имя обработчика события ошибки событию [ErrorOccurred](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.erroroccurred.aspx) после свойства **Width**, но до закрывающего тега. В этом пошаговом руководстве используется следующее имя обработчика события ошибки: **OnAdError**.

    ``` syntax
    ErrorOccurred="OnAdError"
    ```

    Полный код XAML, который определяет элемент **AdControl**, выглядит следующим образом.

    ``` syntax
    <UI:AdControl
       ApplicationId="3f83fe91-d6be-434d-a0ae-7351c5a997f1"
       AdUnitId="10865270"
       HorizontalAlignment="Left"
       Height="250"
       Margin="10,10,0,0"
       VerticalAlignment="Top"
       Width="300"
       ErrorOccurred="OnAdError"/>
    ```

2.  Чтобы создать ошибку во время выполнения, создайте второй элемент **AdControl** с другим ИД приложения. Поскольку все объекты **AdControl** в приложении должны использовать тот же ИД приложения, при создании дополнительного элемента **AdControl** с другим ИД приложения будет создана ошибка.

    Определите второй элемент **AdControl** в файле MainPage.xaml сразу после первого **AdControl** и задайте для свойства [ApplicationId](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.applicationid.aspx) значение 0 (нуль).

    ``` syntax
    <UI:AdControl
      ApplicationId="0"
      AdUnitId="10865270"
      HorizontalAlignment="Left"
      Height="250"
      Margin="10,265,0,0"
      VerticalAlignment="Top"
      Width="300"
      ErrorOccurred="OnAdError" />
    ```

3.  В файле MainPage.xaml.cs добавьте в класс **MainPage** следующий обработчик событий **OnAdError**. Этот обработчик событий записывает информацию в окно **вывода** Visual Studio.

    ``` syntax
    private void OnAdError(object sender, AdErrorEventArgs e)
    {
        System.Diagnostics.Debug.WriteLine("AdControl error (" + ((AdControl)sender).Name + "): " + e.Error + " ErrorCode: " + e.ErrorCode.ToString());
    }
    ```

4.  Сборка и запуск проекта.

После запуска приложения отобразится сообщение, похожее на отображаемое в окне **вывода** в Visual Studio.

``` syntax
AdControl error (): MicrosoftAdvertising.Shared.AdException: all ad requests must use the same application ID within a single application (0, d25517cb-12d4-4699-8bdc-52040c712cab) ErrorCode: ClientConfiguration
```

## Связанные разделы

* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 



<!--HONumber=Jun16_HO4-->


