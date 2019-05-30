---
Description: Вы можете задать время, в течение которого распознаватель речи будет игнорировать тишину или нераспознаваемые звуки (бормотание) и будет ожидать данные на речевом входе.
title: Установка времени ожидания распознавания речи
ms.assetid: 58F446AC-4A56-454D-8125-62A2C4DBFCC8
label: Speech recognition timeouts
template: detail.hbs
keywords: речь, голос, распознавание речи, естественный язык, диктовка, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: f321b9ec43f2c844854600b8260a7fdc189c0446
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66365391"
---
# <a name="set-speech-recognition-timeouts"></a>Установка времени ожидания распознавания речи


Вы можете задать время, в течение которого распознаватель речи будет игнорировать тишину или нераспознаваемые звуки (бормотание) и будет ожидать данные на речевом входе.

> **Важные API**: [**Время ожидания**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.timeouts), [ **SpeechRecognizerTimeouts**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizerTimeouts)

## <a name="set-a-timeout"></a>Настройка времени ожидания


Здесь мы указываем различные значения свойства [**Timeouts**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.timeouts).

-   InitialSilenceTimeout — продолжительность периода времени, в течение которого объект SpeechRecognizer обнаруживает тишину (перед получением каких-либо результатов распознавания) и решает, что на вход не будут поступать никакие данные.
-   BabbleTimeout — продолжительность периода времени, в течение которого объект SpeechRecognizer продолжает "прослушивать" нераспознаваемые звуки (бормотание), прежде чем он решит, что речевой ввод закончен, и завершит операцию распознавания.
-   EndSilenceTimeout — продолжительность периода времени, в течение которого объект SpeechRecognizer обнаруживает тишину (после получения результатов распознавания) и решает, что речевой ввод закончен.

**Примечание**  можно задать значения времени ожидания на основе распознаватель.

 

```CSharp
// Set timeout settings.
recognizer.Timeouts.InitialSilenceTimeout = TimeSpan.FromSeconds(6.0);
recognizer.Timeouts.BabbleTimeout = TimeSpan.FromSeconds(4.0);
recognizer.Timeouts.EndSilenceTimeout = TimeSpan.FromSeconds(1.2);
```

## <a name="related-articles"></a>Связанные статьи


* [Взаимодействие с помощью голосовых функций](speech-interactions.md)
**Примеры**
* [Распознавание речи и синтеза речи-пример](https://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 




