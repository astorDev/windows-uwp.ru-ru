---
Description: Set how long a speech recognizer ignores silence or unrecognizable sounds (babble) and continues listening for speech input.
title: Установка времени ожидания распознавания речи
ms.assetid: 58F446AC-4A56-454D-8125-62A2C4DBFCC8
label: Speech recognition timeouts
template: detail.hbs
keywords: речь, голос, распознавание речи, естественный язык, диктовка, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 73c7e889b4633dae9e416cf7ccde13eb3f58e8ee
ms.sourcegitcommit: b11f305dbf7649c4b68550b666487c77ea30d98f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "7851955"
---
# <a name="set-speech-recognition-timeouts"></a>Установка времени ожидания распознавания речи


Вы можете задать время, в течение которого распознаватель речи будет игнорировать тишину или нераспознаваемые звуки (бормотание) и будет ожидать данные на речевом входе.

> **Важные API-интерфейсы**: [**Timeouts**](https://msdn.microsoft.com/library/windows/apps/dn653253), [**SpeechRecognizerTimeouts**](https://msdn.microsoft.com/library/windows/apps/dn653230)

## <a name="set-a-timeout"></a>Настройка времени ожидания


Здесь мы указываем различные значения свойства [**Timeouts**](https://msdn.microsoft.com/library/windows/apps/dn653253).

-   InitialSilenceTimeout— продолжительность периода времени, в течение которого объект SpeechRecognizer обнаруживает тишину (перед получением каких-либо результатов распознавания) и решает, что на вход не будут поступать никакие данные.
-   BabbleTimeout— продолжительность периода времени, в течение которого объект SpeechRecognizer продолжает "прослушивать" нераспознаваемые звуки (бормотание), прежде чем он решит, что речевой ввод закончен, и завершит операцию распознавания.
-   EndSilenceTimeout— продолжительность периода времени, в течение которого объект SpeechRecognizer обнаруживает тишину (после получения результатов распознавания) и решает, что речевой ввод закончен.

**Примечание**время ожидания можно задать отдельно для каждого распознавателя.

 

```CSharp
// Set timeout settings.
recognizer.Timeouts.InitialSilenceTimeout = TimeSpan.FromSeconds(6.0);
recognizer.Timeouts.BabbleTimeout = TimeSpan.FromSeconds(4.0);
recognizer.Timeouts.EndSilenceTimeout = TimeSpan.FromSeconds(1.2);
```

## <a name="related-articles"></a>Статьи по теме


* [Взаимодействие с помощью голосовых функций](speech-interactions.md)
**Примеры**
* [Пример распознавания и синтеза речи](http://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 




