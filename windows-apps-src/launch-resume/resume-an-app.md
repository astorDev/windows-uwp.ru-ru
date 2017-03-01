---
author: TylerMSFT
title: "Обработка возобновления работы приложения"
description: "Узнайте, как обновить отображаемое содержимое, когда система возобновляет работу приложения."
ms.assetid: DACCC556-B814-4600-A10A-90B82664EA15
ms.author: twhitney
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 113f0ce8e59bab716443c0a74ca39649a1bb83ac
ms.lasthandoff: 02/07/2017

---

# <a name="handle-app-resume"></a>Обработка возобновления работы приложения

\[ Обновлено для приложений UWP в Windows 10. Статьи по Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

-   [**Возобновление**](https://msdn.microsoft.com/library/windows/apps/br242339)

Узнайте, где обновить свой ИП, когда система возобновляет работу приложения. В примере этого раздела регистрируется обработчик событий для события [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339).

## <a name="register-the-resuming-event-handler"></a>Регистрация обработчика события возобновления

Выполните регистрацию для обработки события [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339), которое сигнализирует о том, что пользователь переключился с вашего приложения на другую задачу, а затем вернулся к нему.

> [!div class="tabbedCodeSnippets"]
> ```cs
> partial class MainPage
> {
>    public MainPage()
>    {
>       InitializeComponent();
>       Application.Current.Resuming += new EventHandler<Object>(App_Resuming);
>    }
> }
> ```
> ```vb
> Public NonInheritable Class MainPage
>
>    Public Sub New()
>       InitializeComponent()
>       AddHandler Application.Current.Resuming, AddressOf App_Resuming
>    End Sub
>
> End Class
> ```
> ```cpp
> MainPage::MainPage()
> {
>     InitializeComponent();
>     Application::Current->Resuming +=
>         ref new EventHandler<Platform::Object^>(this, &MainPage::App_Resuming);
> }
> ```

## <a name="refresh-displayed-content-and-reacquire-resources"></a>Обновление отображаемого содержимого и восстановление ресурсов

Система приостанавливает работу вашего приложения через несколько секунд после того, как пользователь переключается на другое приложение или на рабочий стол. Система возобновляет работу приложения, когда пользователь возвращается в него. Когда система возобновляет работу приложения, содержимое переменных и структур данных остается таким же, каким оно было до приостановки приложения. Система восстанавливает работу приложения с места приостановки. Для пользователя это выглядит так, как если бы приложение все это время выполнялось в фоновом режиме.

При обработке приложением события [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339) работа приложения может быть приостановлена на несколько часов ли дней. Оно должно обновить любое содержимое, которое могло устареть, пока приложение было приостановлено, например каналы новостей или расположение пользователя.

В это же время можно восстановить все монопольные ресурсы, освобожденные на время приостановки приложения, такие как дескрипторы файлов, камеры, устройства ввода-вывода, внешние и сетевые устройства.

> [!div class="tabbedCodeSnippets"]
> ```cs
> partial class MainPage
> {
>     private void App_Resuming(Object sender, Object e)
>     {
>         // TODO: Refresh network data, perform UI updates, and reacquire resources like cameras, I/O devices, etc.
>     }
> }
> ```
> ```vb
> Public NonInheritable Class MainPage
>
>     Private Sub App_Resuming(sender As Object, e As Object)
>  
>         ' TODO: Refresh network data, perform UI updates, and reacquire resources like cameras, I/O devices, etc.
>
>     End Sub
>
> End Class
> ```
> ```cpp
> void MainPage::App_Resuming(Object^ sender, Object^ e)
> {
>     // TODO: Refresh network data, perform UI updates, and reacquire resources like cameras, I/O devices, etc.
> }
> ```

> **Примечание.** Поскольку событие [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339) не вызывается из потока пользовательского интерфейса, для диспетчеризации любых вызовов ИП необходимо использовать диспетчер.

## <a name="remarks"></a>Комментарии

Если приложение подключено к отладчику Visual Studio, оно не будет приостановлено. Однако его можно приостановить из отладчика, а затем отправить ему событие **Resume**, чтобы выполнить отладку кода. Убедитесь, что отображается панель инструментов **Место отладки**, и нажмите раскрывающий список рядом со значком **Приостановить**. Затем выберите **Возобновить**.

Для приложений из Магазина Windows Phone за событием [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339) всегда следует событие [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335), даже если ваше приложение в данный момент приостановлено, а пользователь заново запускает приложение с основной плитки или из списка приложений. Приложения могут пропустить инициализацию, если содержимое уже задано в текущем окне. Можно проверить свойство [**LaunchActivatedEventArgs.TileId**](https://msdn.microsoft.com/library/windows/apps/br224736), чтобы определить, было ли приложение запущено с основной или вспомогательной плитки, и на основе этих данных решить, нужно ли запускать приложение заново или можно возобновить его.

## <a name="related-topics"></a>Связанные статьи

* [Жизненный цикл приложения](app-lifecycle.md)
* [Обработка активации приложения](activate-an-app.md)
* [Обработка приостановки работы приложения](suspend-an-app.md)

