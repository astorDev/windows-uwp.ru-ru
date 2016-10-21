---
author: TylerMSFT
title: "Обработка приостановки работы приложения"
description: "Узнайте, как сохранить важные данные приложения, когда система приостанавливает его работу."
ms.assetid: F84F1512-24B9-45EC-BF23-A09E0AC985B0
translationtype: Human Translation
ms.sourcegitcommit: 231161ba576a140859952a7e9a4e8d3bd0ba4596
ms.openlocfilehash: 9d78ee8aceb40cacdb464a65c940ad13baf7bb81

---

# Обработка приостановки работы приложения

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

- [**Suspending**](https://msdn.microsoft.com/library/windows/apps/br242341)

Узнайте, как сохранить важные данные приложения, когда система приостанавливает его работу. В примере регистрируется обработчик события для события [**Suspending**](https://msdn.microsoft.com/library/windows/apps/br242341) и сохраняется строка в файл.

## Важное изменение, представленное в Windows 10 версии 1607

До Windows 10 версии 1607 для сохранения состояния в обработчике приостановки использовался код. Теперь рекомендуется сохранять состояние при входе в фоновое состояние, как описано в разделе [Жизненный цикл приложения универсальной платформы Windows в Windows 10](app-lifecycle.md).

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

## Сохранение данных приложения перед приостановкой

При обработке события [**Suspending**](https://msdn.microsoft.com/library/windows/apps/br242341) приложение имеет возможность сохранить свои важные данные в функции обработчика. Приложение должно использовать для синхронного сохранения своих простых данных API хранилища [**LocalSettings**](https://msdn.microsoft.com/library/windows/apps/br241622).

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

## Высвобождение ресурсов

Вам также нужно освободить монопольные ресурсы и дескрипторы файлов, чтобы другие приложения могли получать к ним доступ, пока приложение приостановлено. К примерам монопольных ресурсов относятся камеры, устройства ввода-вывода, внешние устройства и сетевые ресурсы. Явное освобождение монопольных ресурсов и дескрипторов файлов обеспечивает доступ к ним других приложений на то время, пока приложение приостановлено. После возобновления приложение должно вернуться к использованию тех же монопольных ресурсов и дескрипторов файлов.

## Комментарии

Система приостанавливает работу приложения всякий раз, когда пользователь переключается на другое приложение, рабочий стол или начальный экран. Система возобновляет работу приложения всякий раз, когда пользователь возвращается в него. Когда система возобновляет работу приложения, содержимое переменных и структур данных остается таким же, каким оно было до приостановки приложения. Система восстанавливает работу приложения точно с места приостановки, поэтому пользователю кажется, что приложение выполнялось в фоновом режиме.

Система старается сохранить ваше приложение и его данные в памяти, пока его выполнение приостановлено. Тем не менее, если ресурсов системы для сохранения вашего приложения в памяти недостаточно, система завершает его работу. Когда пользователь снова переключается на приостановленное приложение, работа которого была завершена, система передает событие [**Activated**](https://msdn.microsoft.com/library/windows/apps/br225018) и должна восстановить данные приложения в методе [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335).

Система не уведомляет приложение о завершении его работы, поэтому в случае приостановки приложение должно сохранять свои данные и освобождать монопольные ресурсы и дескрипторы файлов, а затем восстанавливать их во время активации после завершения работы.

Если вы используете асинхронный вызов в обработчике, контроль после его завершения возвращается немедленно. Это означает, что обработчик событий вернет управление приложению, которое сможет перейти в следующее состояние, даже если асинхронный вызов еще не завершен. Используйте метод [**GetDeferral**](http://aka.ms/Kt66iv) объекта [**EnteredBackgroundEventArgs**](http://aka.ms/Ag2yh4), который передается обработчику события, чтобы задержать приостановку после вызова метода [**Complete**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.foundation.deferral.complete.aspx) возвращенного объекта [**Windows.Foundation.Deferral**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.foundation.deferral.aspx).

Отсрочка не увеличивает время, необходимое для выполнения кода перед завершением работы приложения. Она только откладывает завершение до вызова метода *Complete* отсрочки или наступления крайнего срока (*в зависимости от того, происходит первым*).

> **Примечание**. Чтобы повысить скорость ответа системы, в Windows 8.1 приложениям после приостановки предоставляется доступ к ресурсам с низким приоритетом. Для поддержки нового приоритета увеличено время ожидания для операции приостановки, чтобы приложение получило время, эквивалентное 5-секундному ожиданию при обычном приоритете в Windows (от 1 до 10 секунд в Windows Phone). Этот период ожидания нельзя продлить или изменить.

> **Замечание об отладке с использованием Visual Studio:** с помощью Visual Studio можно предотвратить приостановку системой Windows работы программы, подключенной к отладчику. Это позволяет пользователю видеть пользовательский интерфейс отладчика Visual Studio во время выполнения приложения. При отладке программы с помощью Visual Studio вы можете отправить ей событие приостановки. Убедитесь, что отображается панель инструментов **Место отладки**, а затем щелкните значок **Приостановить**.

## Связанные статьи

* [Жизненный цикл приложения](app-lifecycle.md)
* [Обработка активации приложения](activate-an-app.md)
* [Обработка возобновления работы приложения](resume-an-app.md)
* [Руководство по взаимодействию с пользователем — запуск, приостановка и возобновление работы приложения](https://msdn.microsoft.com/library/windows/apps/dn611862)


 

 



<!--HONumber=Aug16_HO3-->


