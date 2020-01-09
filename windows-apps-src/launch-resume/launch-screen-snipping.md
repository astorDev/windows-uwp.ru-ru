---
title: Запуск фрагмента экрана
description: В этом разделе описываются схемы универсального кода ресурса (URI) MS-скринклип и MS-скринскетч. Приложение может использовать эти схемы URI для запуска фрагмента & эскиза или для открытия нового фрагмента.
ms.date: 08/09/2017
ms.topic: article
keywords: Windows 10, UWP, URI, фрагмент, эскиз
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: d9469dd6efd3598ab7abd9791a976385f4dfce49
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684661"
---
# <a name="launch-screen-snipping"></a>Запуск фрагмента экрана

Схемы **MS-скринклип:** и **MS-скринскетч:** URI позволяют запускать ножницы или изменять снимки экрана.

## <a name="open-a-new-snip-from-your-app"></a>Открытие нового фрагмента из приложения

**MS-скринклип:** URI позволяет приложению автоматически открывать и запускать новый фрагмент. Полученный фрагмент копируется в буфер обмена пользователя, но не возвращается в открытое приложение автоматически.

**MS-скринклип:** принимает следующие параметры:

| Параметр | Введите | Обязательный | Описание |
| --- | --- | --- | --- |
| источник | Строка | Нет | Произвольная строка, указывающая источник, который запустил URI. |
| delayInSeconds | int | Нет | Целочисленное значение от 1 до 30. Указывает задержку в полной секунде между вызовом URI и началом выделения. |
| каллбаккформат | Строка | Нет | Этот параметр недоступен. |

## <a name="launching-the-snip--sketch-app"></a>Запуск приложения фрагмента & наброска

**MS-скринскетч:** универсальный код ресурса (URI) позволяет программным путем запустить приложение фрагмента & наброска и открыть в нем конкретный образ для аннотации.

**MS-скринскетч:** принимает следующие параметры:

| Параметр | Введите | Обязательный | Описание |
| --- | --- | --- | --- |
| шаредакцесстокен | Строка | Нет | Токен, определяющий файл, который должен быть открыт в приложении & эскиза. Получено из [шаредсторажеакцессманажер. AddFile](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.sharedstorageaccessmanager.addfile). Если этот параметр не указан, приложение будет запущено без открытия файла. |
| секондаришаредакцесстокен | Строка | Нет | Строка, идентифицирующая JSON-файл с метаданными о фрагменте. Метаданные могут содержать поле **клиппоинтс** с массивом координат x, y и (или) [усерактивити](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity). |
| источник | Строка | Нет | Произвольная строка, указывающая источник, который запустил URI. |
| IsTemporary задано | bool | Нет | Если задано значение true, эскиз экрана будет пытаться удалить файл после его открытия. |

В следующем примере вызывается метод [лаунчуриасинк](https://docs.microsoft.com/uwp/api/Windows.System.Launcher#Windows_System_Launcher_LaunchUriAsync_Windows_Foundation_Uri_) для отправки изображения в фрагмент & эскиза из приложения пользователя.

```csharp

bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-screensketch:edit?source=MyApp&isTemporary=false&sharedAccessToken=2C37ADDA-B054-40B5-8B38-11CED1E1A2D"));

```

В следующем примере показано, что может содержать файл, заданный параметром **секондаришаредакцесстокен** **MS-скринскетч** :

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
  "userActivity": "{\"$schema\":\"http://activity.windows.com/user-activity.json\",\"UserActivity\":\"type\",\"1.0\":\"version\",\"cross-platform-identifiers\":[{\"platform\":\"windows_universal\",\"application\":\"Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge\"},{\"platform\":\"host\",\"application\":\"edge.activity.windows.com\"}],\"activationUrl\":\"microsoft-edge:https://support.microsoft.com/help/13776/windows-use-snipping-tool-to-capture-screenshots\",\"contentUrl\":\"https://support.microsoft.com/help/13776/windows-use-snipping-tool-to-capture-screenshots\",\"visualElements\":{\"attribution\":{\"iconUrl\":\"https://www.microsoft.com/favicon.ico?v2\",\"alternateText\":\"microsoft.com\"},\"description\":\"https://support.microsoft.com/help/13776/windows-use-snipping-tool-to-capture-screenshots\",\"backgroundColor\":\"#FF0078D7\",\"displayText\":\"Use snipping tool to capture screenshots - Windows Help\",\"content\":{\"$schema\":\"http://adaptivecards.io/schemas/adaptive-card.json\",\"type\":\"AdaptiveCard\",\"version\":\"1.0\",\"body\":[{\"type\":\"Container\",\"items\":[{\"type\":\"TextBlock\",\"text\":\"Use snipping tool to capture screenshots - Windows Help\",\"weight\":\"bolder\",\"size\":\"large\",\"wrap\":true,\"maxLines\":3},{\"type\":\"TextBlock\",\"text\":\"https://support.microsoft.com/help/13776/windows-use-snipping-tool-to-capture-screenshots\",\"size\":\"normal\",\"wrap\":true,\"maxLines\":3}]}]}},\"isRoamable\":true,\"appActivityId\":\"https://support.microsoft.com/help/13776/windows-use-snipping-tool-to-capture-screenshots\"}"
}

```
