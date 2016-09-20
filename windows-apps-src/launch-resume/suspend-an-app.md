---
author: TylerMSFT
title: "Обработка приостановки работы приложения"
description: "Узнайте, как сохранить важные данные приложения, когда система приостанавливает его работу."
ms.assetid: F84F1512-24B9-45EC-BF23-A09E0AC985B0
ms.sourcegitcommit: fb83213a4ce58285dae94da97fa20d397468bdc9
ms.openlocfilehash: 3ad58dc20a660d89622d215c46d263adf27a0542

---

# Обработка приостановки работы приложения


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**Suspending**](https://msdn.microsoft.com/library/windows/apps/br242341)

Узнайте, как сохранить важные данные приложения, когда система приостанавливает его работу. В примере регистрируется обработчик события для события [**Suspending**](https://msdn.microsoft.com/library/windows/apps/br242341) и сохраняется строка в файл.

## Регистрация обработчика события приостановки


Регистрируется обработка события [**Suspending**](https://msdn.microsoft.com/library/windows/apps/br242341), которое сигнализирует, что приложение должно сохранить свои данные перед тем, как система приостановит его работу.

> [!div class="tabbedCodeSnippets"]
> ```cs
> using System;
> using Windows.ApplicationModel;
> using Windows.ApplicationModel.Activation;
> using Windows.UI.Xaml;
>
> partial class MainPage
> {
>    public MainPage()
>    {
>       InitializeComponent();
>       Application.Current.Suspending += new SuspendingEventHandler(App_Suspending);
>    }
> }
> ```
> ```vb
> Public NotInheritable Class MainPage
>
>    Public Sub New()
>       InitializeComponent()
>       AddHandler Application.Current.Suspending, AddressOf App_Suspending
>    End Sub
>    
> End Class
> ```
> ```cpp
> using namespace Windows::ApplicationModel;
> using namespace Windows::ApplicationModel::Activation;
> using namespace Windows::Foundation;
> using namespace Windows::UI::Xaml;
> using namespace AppName;
>
> MainPage::MainPage()
> {
>    InitializeComponent();
>    Application::Current->Suspending +=
>        ref new SuspendingEventHandler(this, &MainPage::App_Suspending);
> }
> ```

## [!div class="tabbedCodeSnippets"]


Сохранение данных приложения перед приостановкой При обработке события [**Suspending**](https://msdn.microsoft.com/library/windows/apps/br242341) приложение имеет возможность сохранить свои важные данные в функции обработчика.

> [!div class="tabbedCodeSnippets"]
> ```cs
> partial class MainPage
> {
>     async void App_Suspending(
>         Object sender,
>         Windows.ApplicationModel.SuspendingEventArgs e)
>     {
>         // TODO: This is the time to save app data in case the process is terminated
>     }
> }
> ```
> ```vb
> Public NonInheritable Class MainPage
>
>     Private Sub App_Suspending(
>         sender As Object,
>         e As Windows.ApplicationModel.SuspendingEventArgs) Handles OnSuspendEvent.Suspending
>
>         ' TODO: This is the time to save app data in case the process is terminated
>     End Sub
>
> End Class
> ```
> ```cpp
> void MainPage::App_Suspending(Object^ sender, SuspendingEventArgs^ e)
> {
>     // TODO: This is the time to save app data in case the process is terminated
> }
> ```

## Приложение должно использовать для синхронного сохранения своих простых данных API хранилища [**LocalSettings**](https://msdn.microsoft.com/library/windows/apps/br241622).


[!div class="tabbedCodeSnippets"] Комментарии Система приостанавливает работу приложения всякий раз, когда пользователь переключается на другое приложение, рабочий стол или начальный экран. Система возобновляет работу приложения всякий раз, когда пользователь возвращается в него.

Когда система возобновляет работу приложения, содержимое переменных и структур данных остается таким же, каким оно было до приостановки приложения. Система восстанавливает работу приложения точно с места приостановки, поэтому пользователю кажется, что приложение выполнялось в фоновом режиме. Система старается сохранить ваше приложение и его данные в памяти, пока его выполнение приостановлено.

Тем не менее, если ресурсов системы для сохранения вашего приложения в памяти недостаточно, система завершает его работу.

> Когда пользователь снова переключается на приостановленное приложение, работа которого была завершена, система передает событие [**Activated**](https://msdn.microsoft.com/library/windows/apps/br225018) и должна восстановить данные приложения в методе [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335). Система не уведомляет приложение о завершении его работы, поэтому в случае приостановки приложение должно сохранять свои данные и освобождать монопольные ресурсы и дескрипторы файлов, а затем восстанавливать их во время активации после завершения работы.

> 
            **Примечание**. Если требуется выполнить асинхронную работу, а приложение было приостановлено, необходимо отложить приостановку до завершения работы. Метод [**GetDeferral**](https://msdn.microsoft.com/library/windows/apps/br224690) объекта [**SuspendingOperation**](https://msdn.microsoft.com/library/windows/apps/br224688) (доступного через аргументы события) позволяет отложить приостановку до момента, когда будет вызван метод [**Complete**](https://msdn.microsoft.com/library/windows/apps/br224685) возвращенного объекта [**SuspendingDeferral**](https://msdn.microsoft.com/library/windows/apps/br224684). 
            **Примечание**. Чтобы повысить скорость ответа системы, в Windows 8.1 приложениям после приостановки предоставляется доступ к ресурсам с низким приоритетом.

> Для поддержки нового приоритета увеличено время ожидания для операции приостановки, чтобы приложение получило время, эквивалентное 5-секундному ожиданию при обычном приоритете в Windows (от 1 до 10 секунд в Windows Phone). Этот период ожидания нельзя продлить или изменить. 
            **Замечание об отладке с использованием Microsoft Visual Studio:** с помощью Visual Studio можно предотвратить приостановку системой Windows работы программы, подключенной к отладчику. Это позволяет пользователю видеть пользовательский интерфейс отладчика Visual Studio во время выполнения приложения.

## При отладке программы с помощью Visual Studio вы можете отправить ей событие приостановки.


* [Убедитесь, что отображается панель инструментов **Место отладки**, а затем щелкните значок **Приостановить**.](activate-an-app.md)
* [Ссылки по теме](resume-an-app.md)
* [Обработка активации приложения](https://msdn.microsoft.com/library/windows/apps/dn611862)
* [Обработка возобновления работы приложения](app-lifecycle.md)

 

 



<!--HONumber=Jun16_HO5-->


