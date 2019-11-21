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
ms.openlocfilehash: 0df5f6c2e12b3b2e761ce45f95930dc179ef367f
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258253"
---
# <a name="set-speech-recognition-timeouts"></a>Установка времени ожидания распознавания речи


Вы можете задать время, в течение которого распознаватель речи будет игнорировать тишину или нераспознаваемые звуки (бормотание) и будет ожидать данные на речевом входе.

> **Важные API-интерфейсы**: [**Timeouts**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.timeouts), [**SpeechRecognizerTimeouts**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizerTimeouts)

## <a name="set-a-timeout"></a>Настройка времени ожидания


Здесь мы указываем различные значения свойства [**Timeouts**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.timeouts).

-   InitialSilenceTimeout — продолжительность периода времени, в течение которого объект SpeechRecognizer обнаруживает тишину (перед получением каких-либо результатов распознавания) и решает, что на вход не будут поступать никакие данные.
-   BabbleTimeout — продолжительность периода времени, в течение которого объект SpeechRecognizer продолжает "прослушивать" нераспознаваемые звуки (бормотание), прежде чем он решит, что речевой ввод закончен, и завершит операцию распознавания.
-   EndSilenceTimeout — продолжительность периода времени, в течение которого объект SpeechRecognizer обнаруживает тишину (после получения результатов распознавания) и решает, что речевой ввод закончен.

**Обратите внимание** ,  время ожидания может быть задано для каждого распознавателя.

 

```CSharp
// Set timeout settings.
recognizer.Timeouts.InitialSilenceTimeout = TimeSpan.FromSeconds(6.0);
recognizer.Timeouts.BabbleTimeout = TimeSpan.FromSeconds(4.0);
recognizer.Timeouts.EndSilenceTimeout = TimeSpan.FromSeconds(1.2);
```

## <a name="related-articles"></a>Связанные статьи


* [Взаимодействие с помощью голосовых функций](speech-interactions.md)
**Примеры**
* [Пример распознавания речи и синтеза речи](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/SpeechRecognitionAndSynthesis)
 

 




