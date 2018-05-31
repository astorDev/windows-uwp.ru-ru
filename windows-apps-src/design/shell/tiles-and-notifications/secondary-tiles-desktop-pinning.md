---
author: vladimp
Description: Windows desktop applications can pin secondary tiles thanks to the Desktop Bridge!
title: Закрепление вспомогательных плиток из классических приложений
label: Pin secondary tiles from desktop application
template: detail.hbs
ms.author: mijacobs
ms.date: 05/25/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, мост для классических приложений, вспомогательные плитки, закрепить, закрепление, краткое руководство, пример кода, пример, secondarytile, классическое приложение, win32, winforms, wpf
ms.localizationpriority: medium
ms.openlocfilehash: 0b0015a74750e08d575cad9d0ae78f8c864b7c09
ms.sourcegitcommit: d780e3a087ab5240ea643346480a1427bea9e29b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "1573211"
---
# <a name="pin-secondary-tiles-from-desktop-application"></a>Закрепление вспомогательных плиток из классических приложений


Классические приложения для Windows (Win32, Windows Forms и WPF) могут закреплять вспомогательные плитки благодаря [мосту для классических приложений](https://developer.microsoft.com/windows/bridges/desktop).

![Снимок экрана: вспомогательные плитки](images/secondarytiles.png)

> [!IMPORTANT]
> **Требуется обновление Fall Creators Update**: чтобы закреплять вспомогательные плитки приложений моста для классических приложений, необходимо выбрать целевой пакет SDK 16299 и использовать сборку 16299 или более поздней версии.

Добавление вспомогательной плитки из приложения WPF и WinForms похоже на тот же процесс в чистом приложении UWP. Единственное отличие заключается в том, что вам необходимо указать дескриптор главного окна (HWND). Это связано с тем, что при закреплении плитки Windows отображает модальное диалоговое окно с запросом на подтверждение закрепления плитки, который должен принять пользователь. Если в классическом приложении не настроен объект SecondaryTile с окном владельца, Windows не узнает, где отображать диалоговое окно, а операция завершится ошибкой.

> [!IMPORTANT]
> Уведомления на плитках в настоящее время не поддерживаются на вспомогательных плитках, созданных с помощью моста для классических приложений. Мы работаем над тем, чтобы реализовать эту функцию. 


## <a name="package-your-app-with-desktop-bridge"></a>Создание пакета приложения с помощью моста для классических приложений

Если вы еще не создали пакет приложения с помощью моста для классических приложений, [сделайте это в первую очередь](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-root), прежде чем использовать API-интерфейсы UWP.


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

Инициализируйте новый объект вспомогательной плитки, как и в обычном приложении UWP. Дополнительные сведения о создании и закреплении вспомогательных плиток см. в разделе [Закрепление вспомогательных плиток](secondary-tiles-pinning.md).

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


## <a name="send-tile-notifications"></a>Отправка уведомления на плитке

Уведомления, отправляемые вспомогательным плиткам, в настоящее время не поддерживаются приложениями моста для классических приложений. При попытке отправить уведомление плитки вспомогательной плитке, создается исключение *Элемент не найден* с кодом HResult 0x80070490. Мы работаем над тем, чтобы реализовать эту функцию.


## <a name="resources"></a>Ресурсы

* [Полный пример кода](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SecondaryTileSample)
* [Обзор вспомогательных плиток](secondary-tiles.md)
* [Закрепление вспомогательных плиток (UWP)](secondary-tiles-pinning.md)
* [Мост для классических приложений](https://developer.microsoft.com/windows/bridges/desktop)
* [Примеры кода для моста для классических приложений](https://github.com/Microsoft/DesktopBridgeToUWP-Samples)