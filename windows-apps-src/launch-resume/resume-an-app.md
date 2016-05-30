---
author: mcleblanc
title: Обработка возобновления работы приложения
description: Узнайте, как обновить отображаемое содержимое, когда система возобновляет работу приложения.
ms.assetid: DACCC556-B814-4600-A10A-90B82664EA15
---

# Обработка возобновления работы приложения


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**Возобновление**](https://msdn.microsoft.com/library/windows/apps/br242339)

Узнайте, как обновить отображаемое содержимое, когда система возобновляет работу приложения. В примере этого раздела регистрируется обработчик событий для события [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339).

**Схема:** каким образом эта тема связана с другими? См. следующие разделы:

-   [Стратегия создания приложений среды выполнения Windows на C# или Visual Basic](https://msdn.microsoft.com/library/windows/apps/br229583)
-   [Схема создания приложений среды выполнения Windows на C++](https://msdn.microsoft.com/library/windows/apps/hh700360)

## Регистрация обработчика события возобновления

Выполните регистрацию для обработки события [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339), которое сигнализирует о том, что пользователь переключился с вашего приложения на другую задачу, а затем вернулся к нему.

> [!div class="tabbedCodeSnippets"]
```cs
partial class MainPage
{
   public MainPage()
   {
      InitializeComponent();
      Application.Current.Resuming += new EventHandler<Object>(App_Resuming);
   }
}
```
```vb
Public NonInheritable Class MainPage

   Public Sub New()
      InitializeComponent() 
      AddHandler Application.Current.Resuming, AddressOf App_Resuming
   End Sub

End Class
```
```cpp
MainPage::MainPage()
{
    InitializeComponent();
    Application::Current->Resuming += 
        ref new EventHandler<Platform::Object^>(this, &MainPage::App_Resuming);
}
```

## Обновление отображаемого содержимого после приостановки

При обработке события [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339) приложение имеет возможность обновить отображаемое в нем содержимое.

> [!div class="tabbedCodeSnippets"]
```cs
partial class MainPage
{
    private void App_Resuming(Object sender, Object e)
    {
        // TODO: Refresh network data
    }
}
```
```vb
Public NonInheritable Class MainPage

    Private Sub App_Resuming(sender As Object, e As Object)
 
        ' TODO: Refresh network data

    End Sub

End Class
```
```cpp
void MainPage::App_Resuming(Object^ sender, Object^ e)
{
    // TODO: Refresh network data
}
```

> **Примечание.** Так как событие [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339) не вызывается из потока пользовательского интерфейса, необходимо использовать диспетчер, чтобы получить доступ к потоку пользовательского интерфейса и реализовать обновление пользовательского интерфейса, если вы хотите сделать это в обработчике.

## Комментарии


Система приостанавливает работу вашего приложения всякий раз, когда пользователь переключается на другое приложение или на рабочий стол. Система возобновляет работу вашего приложения всякий раз, когда пользователь возвращается в него. Когда система возобновляет работу приложения, содержимое переменных и структур данных остается таким же, каким оно было до приостановки приложения. Система восстанавливает работу приложения точно с места приостановки, поэтому пользователю кажется, что приложение выполнялось в фоновом режиме. Но если приложение было приостановлено на значительное время, оно должно обновить любое отображаемое содержимое, которое могло измениться за это время, например, пакеты новостей или данные о местоположении пользователя.

Если в вашем приложении нет отображаемого содержимого, которое нужно обновлять, событие [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339) обрабатывать не нужно.

**Замечание об отладке с использованием Visual Studio: ** когда приложение подключено к отладчику Visual Studio, ему можно отправить событие возобновления **Resume**. Убедитесь, что отображается панель инструментов **Место отладки**, и нажмите раскрывающий список рядом со значком **Приостановить**. Затем выберите **Возобновить**.

> **Примечание** Для приложений Магазина Windows Phone за событием [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339) всегда следует событие [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335), даже если работа приложения в настоящее время приостановлена и пользователь повторно запускает приложение с основной плитки или из списка приложений. Приложения могут пропустить инициализацию, если содержимое уже задано в текущем окне. Можно проверить свойство [**LaunchActivatedEventArgs.TileId**](https://msdn.microsoft.com/library/windows/apps/br224736), чтобы определить, было ли приложение запущено с основной или вспомогательной плитки, и на основе этих данных решить, нужно ли запускать приложение заново или можно возобновить его.

## Связанные разделы

* [Обработка активации приложения](activate-an-app.md)
* [Обработка приостановки работы приложения](suspend-an-app.md)
* [Руководство по приостановке и возобновлению работы приложения](https://msdn.microsoft.com/library/windows/apps/hh465088)
* [Жизненный цикл приложения](app-lifecycle.md)




<!--HONumber=May16_HO2-->


