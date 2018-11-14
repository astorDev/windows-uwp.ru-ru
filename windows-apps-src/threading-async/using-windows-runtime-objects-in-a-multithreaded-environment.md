---
title: Использование объектов среды выполнения Windows во многопоточной среде | Документы Майкрософт
description: В статье рассматривается, как .NET Framework обрабатывает вызовы из кода C# и Visual Basic к объектам, предоставляемым средой выполнения Windows или компонентами этой среды.
ms.date: 01/14/2017
ms.topic: article
ms.assetid: 43ffd28c-c4df-405c-bf5c-29c94e0d142b
author: normesta
ms.author: normesta
keywords: windows 10, uwp, таймер, потоки
ms.localizationpriority: medium
ms.openlocfilehash: 9f4b8249a81cb7d71ba1f4775fd858ae87779c85
ms.sourcegitcommit: bdc40b08cbcd46fc379feeda3c63204290e055af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "6149338"
---
# <a name="using-windows-runtime-objects-in-a-multithreaded-environment"></a>Использование объектов среды выполнения Windows во многопоточной среде
В статье рассматривается, как .NET Framework обрабатывает вызовы из кода C# и Visual Basic к объектам, предоставляемым средой выполнения Windows или компонентами этой среды.

В .NET Framework по умолчанию можно обращаться к любому объекту из нескольких потоков без специальной обработки. Все, что для этого нужно— ссылка на объект. В среде выполнения Windows такие объекты называются *гибкими*. Большинство классов среды выполнения Windows являются гибкими, хотя и не все, и даже для гибких классов может потребоваться специальная обработка.

Везде, где возможно, среда CLR обрабатывает объекты из других источников, например из среды выполнения Windows, как объекты .NET Framework.

- Если объект реализует интерфейс [IAgileObject](http://msdn.microsoft.com/library/Hh802476.aspx) или имеет атрибут [MarshalingBehaviorAttribute](http://go.microsoft.com/fwlink/p/?LinkId=256022) с типом [MarshalingType.Agile](http://go.microsoft.com/fwlink/p/?LinkId=256023), среда CLR рассматривает его как гибкий.

- Если среда CLR может маршалировать вызов из потока, где он был произведен, к контексту потока целевого объекта, это происходит прозрачно.

- Если объект имеет атрибут [MarshalingBehaviorAttribute](http://go.microsoft.com/fwlink/p/?LinkId=256022) с типом [MarshalingType.None](http://go.microsoft.com/fwlink/p/?LinkId=256023), класс не предоставляет информацию о маршалинге. Среда CLR не может маршалировать вызов, поэтому создается исключение [InvalidCastException](/dotnet/api/system.invalidcastexception) с сообщением о том, что объект можно использовать только в том контексте потоков, в котором он был создан.

В следующих разделах описаны последствия такого поведения для объектов из различных источников.

## <a name="objects-from-a-windows-runtime-component-that-is-written-in-c-or-visual-basic"></a>Объекты из компонента среды выполнения Windows, написанного на языке C# или Visual Basic
Все типы в компоненте, которые можно активировать, гибкие по умолчанию.

> [!NOTE]
>  Гибкость не подразумевает потокобезопасность. Как в среде выполнения Windows, так и в .NET Framework большинство классов не являются потокобезопасными, поскольку потокобезопасность снижает производительность, а большинство объектов никогда не используются сразу несколькими потоками. Более эффективно синхронизировать доступ к отдельным объектам (или использовать потокобезопасные классы) только при необходимости.

При создании компонента среды выполнения Windows значения по умолчанию можно переопределить. См. статьи об интерфейсах [ICustomQueryInterface](/dotnet/api/system.runtime.interopservices.icustomqueryinterface) и [IAgileObject](http://msdn.microsoft.com/library/Hh802476.aspx).

## <a name="objects-from-the-windows-runtime"></a>Объекты из среды выполнения Windows
Большинство классов в среде выполнения Windows являются гибкими, и среда CLR обрабатывает их как гибкие. В документации к этим классам среди атрибутов классов указан MarshalingBehaviorAttribute(Agile). Однако члены некоторых из этих гибких классов, например элементы управления XAML, создают исключения, если они не вызываются в потоке пользовательского интерфейса. Например, следующий код пытается использовать фоновый поток для задания свойства кнопки, которую нажали. Свойство кнопки [Content](http://go.microsoft.com/fwlink/p/?LinkId=256025) вызывает исключение.

```csharp
private async void Button_Click_2(object sender, RoutedEventArgs e)
{
    Button b = (Button) sender;
    await Task.Run(() => {
        b.Content += ".";
    });
}
```

```vb
Private Async Sub Button_Click_2(sender As Object, e As RoutedEventArgs)
    Dim b As Button = CType(sender, Button)
    Await Task.Run(Sub()
                       b.Content &= "."
                   End Sub)
End Sub
```

Безопасный доступ к кнопке можно получить, воспользовавшись ее свойством [Dispatcher](http://go.microsoft.com/fwlink/p/?LinkId=256026) или свойством `Dispatcher` любого объекта, существующего в контексте потока пользовательского интерфейса (например, страницы, на которой находится эта кнопка). В приведенном ниже коде метод [RunAsync](http://go.microsoft.com/fwlink/p/?LinkId=256030) объекта [CoreDispatcher](http://go.microsoft.com/fwlink/p/?LinkId=256029) используется для перенаправления вызова в поток пользовательского интерфейса.

```csharp
private async void Button_Click_2(object sender, RoutedEventArgs e)
{
    Button b = (Button) sender;
    await b.Dispatcher.RunAsync(
        Windows.UI.Core.CoreDispatcherPriority.Normal,
        () => {
            b.Content += ".";
    });
}

```

```vb
Private Async Sub Button_Click_2(sender As Object, e As RoutedEventArgs)
    Dim b As Button = CType(sender, Button)
    Await b.Dispatcher.RunAsync(
        Windows.UI.Core.CoreDispatcherPriority.Normal,
        Sub()
            b.Content &= "."
        End Sub)
End Sub
```

> [!NOTE]
>  При вызове свойства `Dispatcher` из другого потока исключение не возникает.

Время существования объекта среды выполнения Windows, который создается в потоке пользовательского интерфейса, ограничено временем существования этого потока. Не пытайтесь обращаться к объектам в потоке пользовательского интерфейса после закрытия окна.

При создании собственного элемента управления путем наследования от элемента управления XAML или путем объединения нескольких элементов управления XAML полученный элемент управления будет гибким, поскольку он является объектом .NET Framework. Однако если он вызывает члены своего базового класса или классов-составляющих, а также при вызове унаследованных членов, эти члены будут создавать исключения, когда они вызываются из любого потока, отличного от потока пользовательского интерфейса.

### <a name="classes-that-cant-be-marshaled"></a>Классы, которые невозможно маршалировать
У классов среды выполнения Windows, которые не предоставляют данные о маршалинге, есть атрибут [MarshalingBehaviorAttribute](http://go.microsoft.com/fwlink/p/?LinkId=256022) с типом [MarshalingType.None](http://go.microsoft.com/fwlink/p/?LinkId=256023). В документации к этим классам среди атрибутов классов указан MarshalingBehaviorAttribute(None).

Приведенный ниже код создает объект [CameraCaptureUI](http://go.microsoft.com/fwlink/p/?LinkId=256027) в потоке пользовательского интерфейса, а затем пытается задать свойство объекта из потока пула потоков. Среда CLR не может маршалировать вызов, поэтому создается исключение [System.InvalidCastException](/dotnet/api/system.invalidcastexception) с сообщением о том, что объект можно использовать только в том контексте потоков, в котором он был создан.

```csharp
Windows.Media.Capture.CameraCaptureUI ccui;

private async void Button_Click_1(object sender, RoutedEventArgs e)
{
    ccui = new Windows.Media.Capture.CameraCaptureUI();

    await Task.Run(() => {
        ccui.PhotoSettings.AllowCropping = true;
    });
}

```

```vb
Private ccui As Windows.Media.Capture.CameraCaptureUI

Private Async Sub Button_Click_1(sender As Object, e As RoutedEventArgs)
    ccui = New Windows.Media.Capture.CameraCaptureUI()

    Await Task.Run(Sub()
                       ccui.PhotoSettings.AllowCropping = True
                   End Sub)
End Sub
```

В документации к [CameraCaptureUI](http://go.microsoft.com/fwlink/p/?LinkId=256027) среди атрибутов класса также указан ThreadingAttribute(STA), поскольку он должен создаваться в однопотоковом контексте, например в потоке пользовательского интерфейса.

Если необходимо обратиться к объекту [CameraCaptureUI](http://go.microsoft.com/fwlink/p/?LinkId=256027) из другого потока, можно кэшировать объект [CoreDispatcher](http://go.microsoft.com/fwlink/p/?LinkId=256029) потока пользовательского интерфейса и использовать его в дальнейшем для перенаправления вызова в этот поток. Либо можно получить диспетчер из объекта XAML, например страницы, как показано в следующем примере кода.

```csharp
Windows.Media.Capture.CameraCaptureUI ccui;

private async void Button_Click_3(object sender, RoutedEventArgs e)
{
    ccui = new Windows.Media.Capture.CameraCaptureUI();

    await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
        () => {
            ccui.PhotoSettings.AllowCropping = true;
        });
}

```

```vb
Dim ccui As Windows.Media.Capture.CameraCaptureUI

Private Async Sub Button_Click_3(sender As Object, e As RoutedEventArgs)

    ccui = New Windows.Media.Capture.CameraCaptureUI()

    Await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                                Sub()
                                    ccui.PhotoSettings.AllowCropping = True
                                End Sub)
End Sub
```

## <a name="objects-from-a-windows-runtime-component-that-is-written-in-c"></a>Объекты из компонента среды выполнения Windows, написанного на C++
Классы в компоненте, который может быть активирован, являются гибкими по умолчанию. Однако C++ предоставляет значительные возможности контроля над моделями потоков и маршалингом. Как указано выше в этой статье, среда CLR распознает гибкие классы, пытается маршалировать вызовы, когда классы не являются гибкими, и создает исключение [System.InvalidCastException](/dotnet/api/system.invalidcastexception), когда класс не содержит данных о маршалинге.

Для объектов, которые работают в потоке пользовательского интерфейса и создают исключения при вызове из потоков, отличных от потоков пользовательского интерфейса, можно использовать объект потока пользовательского интерфейса [CoreDispatcher](http://go.microsoft.com/fwlink/p/?LinkId=256029) для перенаправления вызова.

## <a name="see-also"></a>См. также
[Руководство по C#](/dotnet/articles/csharp/)

[Руководство по Visual Basic](/dotnet/articles/visual-basic/)
