---
author: vladimp
Description: "Классические приложения для Windows могут закреплять вспомогательные плитки с помощью моста для классических приложений."
title: "Закрепление вспомогательных плиток из классических приложений"
label: Pin secondary tiles from desktop application
template: detail.hbs
ms.author: wdg-dev-content
ms.date: 05/25/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, мост для классических приложений, вспомогательные плитки, закрепить, закрепление, краткое руководство, пример кода, пример, secondarytile, классическое приложение, win32, winforms, wpf"
ms.openlocfilehash: dea17368a21983b9ca800aac2efa6410dab06958
ms.sourcegitcommit: 6396a69aab081f5c7a9a59739c83538616d3b1c7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2017
---
# <a name="pin-secondary-tiles-from-desktop-application"></a>Закрепление вспомогательных плиток из классических приложений
<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Классические приложения для Windows (Win32, Windows Forms и WPF) могут закреплять вспомогательные плитки благодаря [мосту для классических приложений](https://developer.microsoft.com/en-us/windows/bridges/desktop).

> [!IMPORTANT]
> **ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ | Требуется осеннее обновление Creators Update**. Чтобы использовать вспомогательные плитки в классическом приложении, необходима [сборка программы предварительной оценки 16199](https://blogs.windows.com/windowsexperience/2017/05/17/announcing-windows-10-insider-preview-build-16199-pc-build-15215-mobile/#bDqf2Ah3Gd7FM66g.97) или более поздней версии.

![Снимок экрана: вспомогательные плитки](images/secondarytiles.png)

Добавление вспомогательной плитки из приложения WPF и WinForms похоже на тот же процесс в чистом приложении UWP. Единственное отличие заключается в том, что вам необходимо указать дескриптор главного окна (HWND). Это связано с тем, что при закреплении плитки Windows отображает модальное диалоговое окно с запросом на подтверждение закрепления плитки, который должен принять пользователь. Если в классическом приложении не настроен объект SecondaryTile с окном владельца, Windows не узнает, где отображать диалоговое окно, а операция завершится ошибкой.


## <a name="package-your-app-with-desktop-bridge"></a>Создание пакета приложения с помощью моста для классических приложений

Если вы еще не создали пакет приложения с помощью моста для классических приложений, [сделайте это в первую очередь](https://docs.microsoft.com/en-us/windows/uwp/porting/desktop-to-uwp-root), прежде чем использовать API-интерфейсы UWP.


## <a name="enable-access-to-iinitializewithwindow-interface"></a>Предоставление доступа к интерфейсу IInitializeWithWindow

Если приложение написано на управляемом языке, например C# или Visual Basic, объявите интерфейс IInitializeWithWindow в коде приложения с атрибутом [ComImport](https://msdn.microsoft.com/library/system.runtime.interopservices.comimportattribute.aspx) и Guid, как показано в следующем примере кода на C#. В этом примере предполагается, что файл кода имеет оператор using для пространства имен System.Runtime.InteropServices.

```csharp
[ComImport]
[Guid("3E68D4BD-7135-4D10-8018-9FB6D9F33FA1")]
[InterfaceType(ComInterfaceType.InterfaceIsIUnknown)]
public interface IInitializeWithWindow
{
    void Initialize(IntPtr hwnd);
}
```

Или же, если приложения написано на языке C++, добавьте в код ссылку на файл заголовка **shobjidl.h**. Этот файл заголовка содержит объявление интерфейса *IInitializeWithWindow*.


## <a name="initialize-the-secondary-tile"></a>Инициализация вспомогательной плитки

Инициализируйте новый объект вспомогательной плитки, как и в обычном приложении UWP. Дополнительные сведения о создании и закреплении вспомогательных плиток см. в разделе [Закрепление вспомогательных плиток](tiles-and-notifications-secondary-tiles-pinning.md).

```csharp
// Initialize the tile with required arguments
SecondaryTile tile = new SecondaryTile(
    "myTileId5391",
    "Display name",
    "myActivationArgs",
    new Uri("ms-appx:///Assets/Square150x150Logo.png"),
    TileSize.Default);
```


## <a name="assign-the-window-handle"></a>Назначение дескриптора окна

Этот этап необходим для классических приложений. Приведите объект к типу объекта [IInitializeWithWindow](https://msdn.microsoft.com/library/windows/desktop/hh706981.aspx). Затем вызовите метод [IInitializeWithWindow.Initialize](https://msdn.microsoft.com/library/windows/desktop/hh706982.aspx) и передайте дескриптор окна, которое вы хотите сделать владельцем любых модального диалогового окна. В следующем примере кода на C# показана передача дескриптора главного окна приложения методу.

```csharp
// Assign the window handle
IInitializeWithWindow initWindow = (IInitializeWithWindow)(object)tile;
initWindow.Initialize(System.Diagnostics.Process.GetCurrentProcess().MainWindowHandle);
```


## <a name="pin-the-tile"></a>Закрепление плитки

Наконец, отправьте запрос на закрепление плитки, как и в обычном приложении UWP.

```csharp
// Pin the tile
bool isPinned = await tile.RequestCreateAsync();

// TODO: Update UI to reflect whether user can now either unpin or pin
```


## <a name="resources"></a>Ресурсы

* [Полный пример кода](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SecondaryTileSample)
* [Обзор вспомогательных плиток](tiles-and-notifications-secondary-tiles.md)
* [Закрепление вспомогательных плиток (UWP)](tiles-and-notifications-secondary-tiles-pinning.md)
* [Мост для классических приложений](https://developer.microsoft.com/en-us/windows/bridges/desktop)
* [Примеры кода для моста для классических приложений](https://github.com/Microsoft/DesktopBridgeToUWP-Samples)