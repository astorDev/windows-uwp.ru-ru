---
Description: Узнайте, как выбрать установленный язык для распознавания речи.
title: Определение языка для распознавателя речи
ms.assetid: 4C463A1B-AF6A-46FD-A839-5D6724955B38
label: Specify the speech recognizer language
template: detail.hbs
keywords: речь, голос, распознавание речи, естественный язык, диктовка, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 9e23cb9c01178640bfa1519d8df369ec76ed2a6c
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57593839"
---
# <a name="specify-the-speech-recognizer-language"></a>Определение языка для распознавателя речи


Узнайте, как выбрать установленный язык для распознавания речи.

> **Важные API-интерфейсы**: [**SupportedTopicLanguages**](https://msdn.microsoft.com/library/windows/apps/dn653251), [ **SupportedGrammarLanguages**](https://msdn.microsoft.com/library/windows/apps/dn653250), [ **языка**](https://msdn.microsoft.com/library/windows/apps/br206804)


Здесь мы перечисляем языки, установленные в системе, определяем язык, используемый по умолчанию, и выбираем другой язык для распознавания.

**Предварительные требования:**

В данной статье используются материалы статьи [Распознавание речи](speech-recognition.md).

От вас требуется понимание основных принципов распознавания речи и знание соответствующих ограничений.

Если вы — начинающий разработчик приложений универсальной платформы Windows (UWP), прочитайте указанные ниже статьи, чтобы ознакомиться с описанными здесь технологиями.

-   [Создание первого приложения](https://msdn.microsoft.com/library/windows/apps/bg124288)
-   Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

**Рекомендации по пользовательскому интерфейсу:**

Полезные советы по разработке практичного и привлекательного приложения с поддержкой голосовых функций см. в разделе [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121).

## <a name="identify-the-default-language"></a>Определение языка, используемого по умолчанию


В качестве языка по умолчанию распознаватель речи использует язык для голосовых функций, установленный в системе. Этот язык устанавливает пользователь на устройстве, в разделе «Параметры» &gt; «Система» &gt; «Речь» &gt; «Язык голосовых функций».

Чтобы определить язык по умолчанию, нужно проверить статическое свойство [**SystemSpeechLanguage**](https://msdn.microsoft.com/library/windows/apps/dn653252).

```CSharp
var language = SpeechRecognizer.SystemSpeechLanguage; 
```

## <a name="confirm-an-installed-language"></a>Подтверждение установленного языка


На разных устройствах установленные языки могут отличаться. Следует проверить наличие языка, если с ним связано какое-либо ограничение, которое имеет для вас значение.

**Примечание**  требуется перезагрузка после установки нового языкового пакета. Исключение с кодом ошибки SPERR\_не\_FOUND (0x8004503a) происходит в том случае, если указанный язык не поддерживается или не завершило установку.

 

Определите поддерживаемые языки на устройстве, проверив одно из двух статических свойств класса [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226):

-   [**SupportedTopicLanguages**](https://msdn.microsoft.com/library/windows/apps/dn653251)— коллекция [ **языка** ](https://msdn.microsoft.com/library/windows/apps/br206804) объекты, используемые с предопределенные диктовки и грамматики поиска web.

-   [**SupportedGrammarLanguages**](https://msdn.microsoft.com/library/windows/apps/dn653250)— коллекция [ **языка** ](https://msdn.microsoft.com/library/windows/apps/br206804) объекты, используемые с ограничением списка или файл спецификации грамматики распознавания речи (SRGS).

## <a name="specify-a-language"></a>Определение языка


Чтобы определить язык, передайте объект [**Language**](https://msdn.microsoft.com/library/windows/apps/br206804) в конструктор [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226).

Здесь мы определяем en-US как язык для распознавания.


```CSharp
var language = new Windows.Globalization.Language(“en-US”); 
var recognizer = new SpeechRecognizer(language); 
```

## <a name="remarks"></a>Замечания


Ограничение по одной теме можно настроить, добавив [**SpeechRecognitionTopicConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631446) в коллекцию [**Constraints**](https://msdn.microsoft.com/library/windows/apps/dn653241) средства [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226), а затем вызвав [**CompileConstraintsAsync**](https://msdn.microsoft.com/library/windows/apps/dn653240). Если распознаватель не удалось инициализировать с поддерживаемым языком темы, возвращается состояние [**SpeechRecognitionResultStatus**](https://msdn.microsoft.com/library/windows/apps/dn631433) для **TopicLanguageNotSupported**.

Ограничение по списку можно настроить, добавив [**SpeechRecognitionListConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631421) в коллекцию [**Constraints**](https://msdn.microsoft.com/library/windows/apps/dn653241) средства [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226), а затем вызвав [**CompileConstraintsAsync**](https://msdn.microsoft.com/library/windows/apps/dn653240). Вы не можете задать язык пользовательского списка напрямую. Список будет обрабатываться с использованием языка распознавателя.

Грамматика SRGS – это открытый формат XML, представляемый классом [**SpeechRecognitionGrammarFileConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631412). В отличие от пользовательских списков язык грамматики можно указать в разметке SRGS. [**CompileConstraintsAsync** ](https://msdn.microsoft.com/library/windows/apps/dn653240) завершается сбоем с [ **SpeechRecognitionResultStatus** ](https://msdn.microsoft.com/library/windows/apps/dn631433) из **TopicLanguageNotSupported** Если распознаватель не инициализирован на том же языке, как разметку SRGS.

## <a name="related-articles"></a>Связанные статьи

**Разработчикам**

* [Взаимодействия с помощью речи](speech-interactions.md)

**Проектировщикам**

* [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121)

**Примеры**

* [Распознавание речи и синтеза речи-пример](https://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 




