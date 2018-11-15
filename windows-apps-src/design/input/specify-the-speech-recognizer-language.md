---
author: Karl-Bridge-Microsoft
Description: Learn how to select an installed language to use for speech recognition.
title: Определение языка для распознавателя речи
ms.assetid: 4C463A1B-AF6A-46FD-A839-5D6724955B38
label: Specify the speech recognizer language
template: detail.hbs
keywords: речь, голос, распознавание речи, естественный язык, диктовка, ввод, взаимодействие с пользователем
ms.author: kbridge
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 7e042a9bbedee3ded0601eda06da8e349c4b788c
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6668337"
---
# <a name="specify-the-speech-recognizer-language"></a>Определение языка для распознавателя речи


Узнайте, как выбрать установленный язык для распознавания речи.

> **Важные API-интерфейсы**: [**SupportedTopicLanguages**](https://msdn.microsoft.com/library/windows/apps/dn653251), [**SupportedGrammarLanguages**](https://msdn.microsoft.com/library/windows/apps/dn653250), [**Language**](https://msdn.microsoft.com/library/windows/apps/br206804)


Здесь мы перечисляем языки, установленные в системе, определяем язык, используемый по умолчанию, и выбираем другой язык для распознавания.

**Необходимые условия**

В данной статье используются материалы статьи [Распознавание речи](speech-recognition.md).

От вас требуется понимание основных принципов распознавания речи и знание соответствующих ограничений.

Если вы— начинающий разработчик приложений универсальной платформы Windows (UWP), прочитайте указанные ниже статьи, чтобы ознакомиться с описанными здесь технологиями.

-   [Создание первого приложения](https://msdn.microsoft.com/library/windows/apps/bg124288)
-   Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

**Рекомендации по взаимодействию с пользователем.**

Полезные советы по разработке практичного и привлекательного приложения с поддержкой голосовых функций см. в разделе [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121).

## <a name="identify-the-default-language"></a>Определение языка, используемого по умолчанию


В качестве языка по умолчанию распознаватель речи использует язык для голосовых функций, установленный в системе. Этот язык устанавливает пользователь на устройстве, в разделе «Параметры» &gt; «Система» &gt; «Речь» &gt; «Язык голосовых функций».

Чтобы определить язык по умолчанию, нужно проверить статическое свойство [**SystemSpeechLanguage**](https://msdn.microsoft.com/library/windows/apps/dn653252).

```CSharp
var language = SpeechRecognizer.SystemSpeechLanguage; 
```

## <a name="confirm-an-installed-language"></a>Подтверждение установленного языка


На разных устройствах установленные языки могут отличаться. Следует проверить наличие языка, если с ним связано какое-либо ограничение, которое имеет для вас значение.

**Примечание**перезагрузка необходима после установки нового языкового пакета. Если указанный язык не поддерживается или его установка не завершена, возникает исключение с кодом ошибки SPERR\_NOT\_FOUND (0x8004503a).

 

Определите поддерживаемые языки на устройстве, проверив одно из двух статических свойств класса [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226):

-   [**SupportedTopicLanguages**](https://msdn.microsoft.com/library/windows/apps/dn653251) — коллекция объектов [**Language**](https://msdn.microsoft.com/library/windows/apps/br206804), используемых с предустановленными правилами грамматики для диктовки и поиска в Интернете.

-   [**SupportedGrammarLanguages**](https://msdn.microsoft.com/library/windows/apps/dn653250) — коллекция объектов [**Language**](https://msdn.microsoft.com/library/windows/apps/br206804), используемых с ограничением по списку или файлом определения грамматики для распознавания речи (SRGS).

## <a name="specify-a-language"></a>Определение языка


Чтобы определить язык, передайте объект [**Language**](https://msdn.microsoft.com/library/windows/apps/br206804) в конструктор [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226).

Здесь мы определяем en-US как язык для распознавания.


```CSharp
var language = new Windows.Globalization.Language(“en-US”); 
var recognizer = new SpeechRecognizer(language); 
```

## <a name="remarks"></a>Комментарии


Ограничение по одной теме можно настроить, добавив [**SpeechRecognitionTopicConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631446) в коллекцию [**Constraints**](https://msdn.microsoft.com/library/windows/apps/dn653241) средства [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226), а затем вызвав [**CompileConstraintsAsync**](https://msdn.microsoft.com/library/windows/apps/dn653240). Если распознаватель не удалось инициализировать с поддерживаемым языком темы, возвращается состояние [**SpeechRecognitionResultStatus**](https://msdn.microsoft.com/library/windows/apps/dn631433) для **TopicLanguageNotSupported**.

Ограничение по списку можно настроить, добавив [**SpeechRecognitionListConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631421) в коллекцию [**Constraints**](https://msdn.microsoft.com/library/windows/apps/dn653241) средства [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226), а затем вызвав [**CompileConstraintsAsync**](https://msdn.microsoft.com/library/windows/apps/dn653240). Вы не можете задать язык пользовательского списка напрямую. Список будет обрабатываться с использованием языка распознавателя.

Грамматика SRGS–это открытый формат XML, представляемый классом [**SpeechRecognitionGrammarFileConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631412). В отличие от пользовательских списков язык грамматики можно указать в разметке SRGS. Выполнение [**CompileConstraintsAsync**](https://msdn.microsoft.com/library/windows/apps/dn653240) завершается ошибкой с состоянием [**SpeechRecognitionResultStatus**](https://msdn.microsoft.com/library/windows/apps/dn631433) для **TopicLanguageNotSupported**, если распознаватель не удалось инициализировать на языке разметки SRGS.

## <a name="related-articles"></a>Связанные разделы

**Разработчикам**

* [Взаимодействие с помощью голосовых функций](speech-interactions.md)

**Проектировщикам**

* [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121)

**Примеры**

* [Пример распознавания и синтеза речи](http://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 




