---
author: Karl-Bridge-Microsoft
Description: "Вы можете задать время, в течение которого распознаватель речи будет игнорировать тишину или нераспознаваемые звуки (бормотание) и будет ожидать данные на речевом входе."
title: "Установка времени ожидания распознавания речи"
ms.assetid: 58F446AC-4A56-454D-8125-62A2C4DBFCC8
label: Speech recognition timeouts
template: detail.hbs
ms.sourcegitcommit: a4e9a90edd2aae9d2fd5d7bead948422d43dad59
ms.openlocfilehash: e0dbc43cba4556674a4d96013b65837d95044622

---

# Установка времени ожидания распознавания речи
Вы можете задать время, в течение которого распознаватель речи будет игнорировать тишину или нераспознаваемые звуки (бормотание) и будет ожидать данные на речевом входе.

**Важные API**

-   [**Время ожидания**](https://msdn.microsoft.com/library/windows/apps/dn653253)
-   [**SpeechRecognizerTimeouts**](https://msdn.microsoft.com/library/windows/apps/dn653230)


## <span id="Set_a_timeout"></span><span id="set_a_timeout"></span><span id="SET_A_TIMEOUT"></span>Настройка времени ожидания


Здесь мы указываем различные значения свойства [**Timeouts**](https://msdn.microsoft.com/library/windows/apps/dn653253).

-   InitialSilenceTimeout — продолжительность периода времени, в течение которого объект SpeechRecognizer обнаруживает тишину (перед получением каких-либо результатов распознавания) и решает, что на вход не будут поступать никакие данные.
-   BabbleTimeout — продолжительность периода времени, в течение которого объект SpeechRecognizer продолжает "прослушивать" нераспознаваемые звуки (бормотание), прежде чем он решит, что речевой ввод закончен, и завершит операцию распознавания.
-   EndSilenceTimeout — продолжительность периода времени, в течение которого объект SpeechRecognizer обнаруживает тишину (после получения результатов распознавания) и решает, что речевой ввод закончен.

**Примечание.**  Время ожидания можно задать отдельно для каждого распознавателя.

 

```CSharp
// Set timeout settings.
recognizer.Timeouts.InitialSilenceTimeout = TimeSpan.FromSeconds(6.0);
recognizer.Timeouts.BabbleTimeout = TimeSpan.FromSeconds(4.0);
recognizer.Timeouts.EndSilenceTimeout = TimeSpan.FromSeconds(1.2);
```

## <span id="related_topics"></span>Статьи по теме


* [Взаимодействие с помощью голосовых функций](speech-interactions.md)
            
          
            **Примеры**
* [Пример распознавания и синтеза речи](http://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 







<!--HONumber=Jun16_HO3-->


