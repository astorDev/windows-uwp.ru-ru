---
title: Запуск функции "Фрагмент экрана"
description: В этом разделе описываются схемы URI ms-screenclip и ms-screensketch. Ваше приложение может использовать эти схемы URI для запуска приложения эскиза & фрагмент или, чтобы открыть новый фрагмент.
ms.date: 8/1/2017
ms.topic: article
keywords: Windows 10, uwp, uri, фрагмент, эскиза
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 2bddea1dd2b5f21a145bde789f1ad760bb5e556a
ms.sourcegitcommit: b126940932935ebd2965ea68078798fb6e876b23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "9065991"
---
# <a name="launch-screen-snipping"></a>Запуск функции "Фрагмент экрана"

**Ms-screenclip:** и **ms-screensketch:** схемы URI позволяет инициировать выделения или изменении снимки экрана.

## <a name="open-a-new-snip-from-your-app"></a>Откройте новый фрагмент из вашего приложения

**Ms-screenclip:** URI позволяет вашему приложению, чтобы автоматически открывать и начать новый фрагмент. Полученный фрагмент копируется в буфер обмена пользователя, но не передается автоматически возвращается открывающего приложения.

**ms-screenclip:** принимает следующие параметры:

| Параметр | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| источник | string | нет | Свободный строка для указания источника, который запустил URI. |
| delayInSeconds | int | нет | Целое число, от 1 до 30. Задержка, в полной секунд, вызов URI, когда начинается выделения. |
| callbackformat | string | нет | Этот параметр недоступен. |

## <a name="launching-the-snip--sketch-app"></a>Запуск приложения эскиза & фрагмент

**Ms-screensketch:** URI позволяет программными средствами запуска приложения эскиза & фрагмент и откройте определенного изображения из этого приложения для заметок.

**ms-screensketch:** принимает следующие параметры:

| Параметр | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| sharedAccessToken | string | нет | Маркер, определяющий файл, чтобы открыть в приложении эскиза & фрагмент. Полученный [SharedStorageAccessManager.AddFile](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.sharedstorageaccessmanager.addfile). Если этот параметр задан, приложение будет запущено без открыть файл. |
| secondarySharedAccessToken | string | нет | Строка, указывающая JSON-файл метаданные о фрагмента. Метаданные могут включать поля **clipPoints** с массив x, y координаты и/или [userActivity](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity). |
| источник | string | нет | Свободный строка для указания источника, который запустил URI. |
| isTemporary | bool | нет | Если задано значение True, наброска экрана пытается удалить файл после его открытия. |

В следующем примере вызывается метод [LaunchUriAsync](https://docs.microsoft.com/uwp/api/Windows.System.Launcher#Windows_System_Launcher_LaunchUriAsync_Windows_Foundation_Uri_) , чтобы отправлять & фрагмент эскиза изображения из приложения пользователя.

```csharp

bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-screensketch:edit?source=MyApp&isTemporary=false&sharedAccessToken=2C37ADDA-B054-40B5-8B38-11CED1E1A2D"));

```

В следующем примере показано, что может содержать файл, указанный в параметре **secondarySharedAccessToken** **ms-screensketch** :

```json
{
  "clipPoints": [
    {
      "x": 0,
      "y": 0
    },
    {
      "x": 2080,
      "y": 0
    },
    {
      "x": 2080,
      "y": 780
    },
    {
      "x": 0,
      "y": 780
    }
  ],
  "userActivity": "{\"$schema\":\"http://activity.windows.com/user-activity.json\",\"UserActivity\":\"type\",\"1.0\":\"version\",\"cross-platform-identifiers\":[{\"platform\":\"windows_universal\",\"application\":\"Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge\"},{\"platform\":\"host\",\"application\":\"edge.activity.windows.com\"}],\"activationUrl\":\"microsoft-edge:https://support.microsoft.com/en-us/help/13776/windows-use-snipping-tool-to-capture-screenshots\",\"contentUrl\":\"https://support.microsoft.com/en-us/help/13776/windows-use-snipping-tool-to-capture-screenshots\",\"visualElements\":{\"attribution\":{\"iconUrl\":\"https://www.microsoft.com/favicon.ico?v2\",\"alternateText\":\"microsoft.com\"},\"description\":\"https://support.microsoft.com/en-us/help/13776/windows-use-snipping-tool-to-capture-screenshots\",\"backgroundColor\":\"#FF0078D7\",\"displayText\":\"Use snipping tool to capture screenshots - Windows Help\",\"content\":{\"$schema\":\"http://adaptivecards.io/schemas/adaptive-card.json\",\"type\":\"AdaptiveCard\",\"version\":\"1.0\",\"body\":[{\"type\":\"Container\",\"items\":[{\"type\":\"TextBlock\",\"text\":\"Use snipping tool to capture screenshots - Windows Help\",\"weight\":\"bolder\",\"size\":\"large\",\"wrap\":true,\"maxLines\":3},{\"type\":\"TextBlock\",\"text\":\"https://support.microsoft.com/en-us/help/13776/windows-use-snipping-tool-to-capture-screenshots\",\"size\":\"normal\",\"wrap\":true,\"maxLines\":3}]}]}},\"isRoamable\":true,\"appActivityId\":\"https://support.microsoft.com/en-us/help/13776/windows-use-snipping-tool-to-capture-screenshots\"}"
}

```
