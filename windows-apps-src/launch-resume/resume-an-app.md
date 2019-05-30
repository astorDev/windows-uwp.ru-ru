---
title: Обработка возобновления работы приложения
description: Узнайте, как обновить отображаемое содержимое, когда система возобновляет работу приложения.
ms.assetid: DACCC556-B814-4600-A10A-90B82664EA15
ms.date: 07/06/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- vb
- cppwinrt
- cpp
ms.openlocfilehash: d7f26e7a4ae05aaf3e197843e18273cb765754a0
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371463"
---
# <a name="handle-app-resume"></a>Обработка возобновления работы приложения

**Важные API**

- [**Возобновление**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.resuming)

Узнайте, где обновить свой ИП, когда система возобновляет работу приложения. В примере этого раздела регистрируется обработчик событий для события [**Resuming**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.resuming).

## <a name="register-the-resuming-event-handler"></a>Регистрация обработчика события возобновления

Выполните регистрацию для обработки события [**Resuming**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.resuming), которое сигнализирует о том, что пользователь переключился с вашего приложения на другую задачу, а затем вернулся к нему.

```csharp
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

```cppwinrt
MainPage::MainPage()
{
    InitializeComponent();
    Windows::UI::Xaml::Application::Current().Resuming({ this, &MainPage::App_Resuming });
}
```

```cpp
MainPage::MainPage()
{
    InitializeComponent();
    Application::Current->Resuming +=
        ref new EventHandler<Platform::Object^>(this, &MainPage::App_Resuming);
}
```

## <a name="refresh-displayed-content-and-reacquire-resources"></a>Обновление отображаемого содержимого и восстановление ресурсов

Система приостанавливает работу вашего приложения через несколько секунд после того, как пользователь переключается на другое приложение или на рабочий стол. Система возобновляет работу приложения, когда пользователь возвращается в него. Когда система возобновляет работу приложения, содержимое переменных и структур данных остается таким же, каким оно было до приостановки приложения. Система восстанавливает работу приложения с места приостановки. Для пользователя это выглядит так, как если бы приложение все это время выполнялось в фоновом режиме.

При обработке приложением события [**Resuming**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.resuming) работа приложения может быть приостановлена на несколько часов ли дней. Оно должно обновить любое содержимое, которое могло устареть, пока приложение было приостановлено, например каналы новостей или расположение пользователя.

В это же время можно восстановить все монопольные ресурсы, освобожденные на время приостановки приложения, такие как дескрипторы файлов, камеры, устройства ввода-вывода, внешние и сетевые устройства.

```csharp
partial class MainPage
{
    private void App_Resuming(Object sender, Object e)
    {
        // TODO: Refresh network data, perform UI updates, and reacquire resources like cameras, I/O devices, etc.
    }
}
```

```vb
Public NonInheritable Class MainPage

    Private Sub App_Resuming(sender As Object, e As Object)
 
        ' TODO: Refresh network data, perform UI updates, and reacquire resources like cameras, I/O devices, etc.

    End Sub
>
End Class
```

```cppwinrt
void MainPage::App_Resuming(
    Windows::Foundation::IInspectable const& /* sender */,
    Windows::Foundation::IInspectable const& /* e */)
{
    // TODO: Refresh network data, perform UI updates, and reacquire resources like cameras, I/O devices, etc.
}
```

```cpp
void MainPage::App_Resuming(Object^ sender, Object^ e)
{
    // TODO: Refresh network data, perform UI updates, and reacquire resources like cameras, I/O devices, etc.
}
```

> [!NOTE]
> Так как [ **возобновление** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.resuming) событие не вызывается из потока пользовательского интерфейса, необходимо использовать диспетчер в обработчике для распределения вызовов любого пользовательского интерфейса.

## <a name="remarks"></a>Примечания

Если приложение подключено к отладчику Visual Studio, оно не будет приостановлено. Однако его можно приостановить из отладчика, а затем отправить ему событие **Resume**, чтобы выполнить отладку кода. Убедитесь, что отображается панель инструментов **Место отладки**, и нажмите раскрывающий список рядом со значком **Приостановить**. Затем выберите **Возобновить**.

Для приложений из Магазина Windows Phone за событием [**Resuming**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.resuming) всегда следует событие [**OnLaunched**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched), даже если ваше приложение в данный момент приостановлено, а пользователь заново запускает приложение с основной плитки или из списка приложений. Приложения могут пропустить инициализацию, если содержимое уже задано в текущем окне. Можно проверить свойство [**LaunchActivatedEventArgs.TileId**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.launchactivatedeventargs.tileid), чтобы определить, было ли приложение запущено с основной или вспомогательной плитки, и на основе этих данных решить, нужно ли запускать приложение заново или можно возобновить его.

## <a name="related-topics"></a>См. также

* [Жизненный цикл приложения](app-lifecycle.md)
* [Обработка активации приложения](activate-an-app.md)
* [Обработка приостановки работы приложения](suspend-an-app.md)
