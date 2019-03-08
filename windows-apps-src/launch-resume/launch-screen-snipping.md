---
title: Запуск функции "Фрагмент экрана"
description: В этом разделе описываются схемы URI ms-screenclip и ms-screensketch. Приложение может использовать эти схемы URI для запуска приложения фрагмент & эскиза или, чтобы открыть новый фрагмент.
ms.date: 08/09/2017
ms.topic: article
keywords: Windows 10, uwp, uri, фрагмент, эскиза
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 06e988387f574b74d511b14a2ebca24b0a149158
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57595389"
---
# <a name="launch-screen-snipping"></a>Запуск функции "Фрагмент экрана"

**Ms screenclip:** и **ms screensketch:** Схемы URI позволяет инициировать выделения или изменение снимков экрана.

## <a name="open-a-new-snip-from-your-app"></a>Откройте новый фрагмент из приложения

**Ms screenclip:** URI позволяет приложению автоматически открыться и начать новый фрагмент. Результирующий фрагмент копируется в буфер обмена пользователя, но не передается автоматически открытия приложения.

**MS-screenclip:** принимает следующие параметры:

| Параметр | Тип | Обязательно | Описание |
| --- | --- | --- | --- |
| Источник | Строка | Нет | Строковое значение свободной формы для указания источника, который запустил URI. |
| delayInSeconds | int | Нет | Целочисленное значение от 1 до 30. Задержка в полной секунды, между URI и начала выделения. |
| callbackformat | Строка | Нет | Этот параметр недоступен. |

## <a name="launching-the-snip--sketch-app"></a>Запуск фрагмент & эскиз приложения

**Ms screensketch:** URI позволяет программным путем запуска приложения фрагмент & эскиз и открыть конкретный образ в таком приложении для заметки.

**MS-screensketch:** принимает следующие параметры:

| Параметр | Тип | Обязательно | Описание |
| --- | --- | --- | --- |
| sharedAccessToken | Строка | Нет | Маркер идентификации файл, чтобы открыть в приложении фрагмент & эскиза. Полученный из [SharedStorageAccessManager.AddFile](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.sharedstorageaccessmanager.addfile). Если этот параметр опущен, приложение запускается без применительно к открытому файлу. |
| secondarySharedAccessToken | Строка | Нет | Строка, определяющая JSON-файл с метаданными о фрагмента. Метаданные могут включать **clipPoints** поле с массивом координаты x и y, и/или [userActivity](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity). |
| Источник | Строка | Нет | Строковое значение свободной формы для указания источника, который запустил URI. |
| IsTemporary | bool | Нет | Если задано значение True, эскиз экрана попытается удалить файл после его открытия. |

В следующем примере вызывается [LaunchUriAsync](https://docs.microsoft.com/uwp/api/Windows.System.Launcher#Windows_System_Launcher_LaunchUriAsync_Windows_Foundation_Uri_) метод для отправки изображения фрагмент & Sketch из приложения пользователя.

```csharp

bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-screensketch:edit?source=MyApp&isTemporary=false&sharedAccessToken=2C37ADDA-B054-40B5-8B38-11CED1E1A2D"));

```

В следующем примере показано, какой файл, указанный параметром **secondarySharedAccessToken** параметр **ms-screensketch** может содержать:

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
