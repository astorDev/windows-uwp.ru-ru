---
author: QuinnRadich
title: Запуск выделения экрана
description: В этом разделе описываются схемы URI ms-screenclip и ms-screensketch. Ваше приложение может использовать эти схемы URI для запуска приложения фрагмент & эскиза или открытие нового фрагмент.
ms.author: quradic
ms.date: 8/1/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, uri, фрагмент, эскиза
ms.localizationpriority: medium
ms.openlocfilehash: e18662125ef72051a289b3f1d0f3dc09b452d256
ms.sourcegitcommit: 933e71a31989f8063b020746fdd16e9da94a44c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "4530996"
---
# <a name="launch-screen-snipping"></a>Запуск выделения экрана

**Ms-screenclip:** и **ms-screensketch:** схемы URI позволяет инициировать выделения или изменении снимки экрана.

## <a name="open-a-new-snip-from-your-app"></a>Откройте новый фрагмент из вашего приложения

**Ms-screenclip:** URI позволяет вашему приложению, чтобы автоматически открывать и начать новый фрагмент. Полученный фрагмент копируется в буфер обмена пользователя, но не передается автоматически возвращается открывающего приложения.

**ms-screenclip:** принимает следующие параметры:

| Параметр | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| источник | string | нет | Свободный строка для указания источника, который запустил URI. |
| delayInSeconds | int | нет | Целое число, от 1 до 30. Задержка в полной секунд, вызов URI, когда начинается выделения. |

## <a name="launching-the-snip--sketch-app"></a>Запуск фрагмент и эскиз приложений

**Ms-screensketch:** URI позволяет программными средствами запуска приложения фрагмент и эскиз и откройте определенного изображения из этого приложения для заметок.

**ms-screensketch:** принимает следующие параметры:

| Параметр | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| sharedAccessToken | string | нет | Маркер, определяющий файл, чтобы открыть в приложении фрагмент и эскиз. Полученный [SharedStorageAccessManager.AddFile](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.sharedstorageaccessmanager.addfile). Если этот параметр задан, приложение будет запущено без открыть файл. |
| источник | string | нет | Свободный строка для указания источника, который запустил URI. |
| isTemporary | bool | нет | Если задано значение True, наброска экрана пытается удалить файл после его открытия. |

Следующий пример вызывает метод [LaunchUriAsync](https://docs.microsoft.com/uwp/api/Windows.System.Launcher#Windows_System_Launcher_LaunchUriAsync_Windows_Foundation_Uri_) для отправки образа фрагмент & эскиза из приложения для пользователя.

```csharp

bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-screensketch:edit?source=MyApp&isTemporary=false&sharedAccessToken=2C37ADDA-B054-40B5-8B38-11CED1E1A2D"));

```