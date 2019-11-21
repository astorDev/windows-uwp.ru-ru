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
ms.openlocfilehash: 200fe265390d10a12a8e1b3a1abf7cd8164238d6
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258233"
---
# <a name="specify-the-speech-recognizer-language"></a>Определение языка для распознавателя речи


Узнайте, как выбрать установленный язык для распознавания речи.

> **Важные API-интерфейсы**: [**SupportedTopicLanguages**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.supportedtopiclanguages), [**SupportedGrammarLanguages**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.supportedgrammarlanguages), [**Language**](https://docs.microsoft.com/uwp/api/Windows.Globalization.Language)


Здесь мы перечисляем языки, установленные в системе, определяем язык, используемый по умолчанию, и выбираем другой язык для распознавания.

**Требований**

В данной статье используются материалы статьи [Распознавание речи](speech-recognition.md).

От вас требуется понимание основных принципов распознавания речи и знание соответствующих ограничений.

Если вы — начинающий разработчик приложений универсальной платформы Windows (UWP), прочитайте указанные ниже статьи, чтобы ознакомиться с описанными здесь технологиями.

-   [Создание первого приложения](https://docs.microsoft.com/windows/uwp/get-started/your-first-app)
-   Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://docs.microsoft.com/windows/uwp/xaml-platform/events-and-routed-events-overview).

**Рекомендации по работе с пользователем:**

Полезные советы по разработке практичного и привлекательного приложения с поддержкой голосовых функций см. в разделе [Рекомендации по проектированию голосовых функций](https://docs.microsoft.com/windows/uwp/input-and-devices/speech-interactions).

## <a name="identify-the-default-language"></a>Определение языка, используемого по умолчанию


В качестве языка по умолчанию распознаватель речи использует язык для голосовых функций, установленный в системе. Этот язык устанавливает пользователь на устройстве, в разделе «Параметры» &gt; «Система» &gt; «Речь» &gt; «Язык голосовых функций».

Чтобы определить язык по умолчанию, нужно проверить статическое свойство [**SystemSpeechLanguage**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.systemspeechlanguage).

```CSharp
var language = SpeechRecognizer.SystemSpeechLanguage; 
```

## <a name="confirm-an-installed-language"></a>Подтверждение установленного языка


На разных устройствах установленные языки могут отличаться. Следует проверить наличие языка, если с ним связано какое-либо ограничение, которое имеет для вас значение.

**Обратите внимание** ,  после установки нового языкового пакета требуется перезагрузка. Исключение с кодом ошибки СПЕРР\_не\_найдено (0x8004503a) возникает, если указанный язык не поддерживается или не завершил установку.

 

Определите поддерживаемые языки на устройстве, проверив одно из двух статических свойств класса [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer):

-   [**Суппортедтопиклангуажес**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.supportedtopiclanguages)— коллекция объектов [**языка**](https://docs.microsoft.com/uwp/api/Windows.Globalization.Language) , используемых с предопределенными грамматиками диктовки и поиском в Интернете.

-   [**Суппортедграммарлангуажес**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.supportedgrammarlanguages)— коллекция объектов [**языка**](https://docs.microsoft.com/uwp/api/Windows.Globalization.Language) , используемых с ограничением списка или файлом спецификации распознавания речи (SRGS).

## <a name="specify-a-language"></a>Определение языка


Чтобы определить язык, передайте объект [**Language**](https://docs.microsoft.com/uwp/api/Windows.Globalization.Language) в конструктор [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer).

Здесь мы определяем en-US как язык для распознавания.


```CSharp
var language = new Windows.Globalization.Language("en-US"); 
var recognizer = new SpeechRecognizer(language); 
```

## <a name="remarks"></a>Замечания


Ограничение по одной теме можно настроить, добавив [**SpeechRecognitionTopicConstraint**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionTopicConstraint) в коллекцию [**Constraints**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.constraints) средства [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer), а затем вызвав [**CompileConstraintsAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.compileconstraintsasync). Если распознаватель не удалось инициализировать с поддерживаемым языком темы, возвращается состояние [**SpeechRecognitionResultStatus**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionResultStatus) для **TopicLanguageNotSupported**.

Ограничение по списку можно настроить, добавив [**SpeechRecognitionListConstraint**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionListConstraint) в коллекцию [**Constraints**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.constraints) средства [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer), а затем вызвав [**CompileConstraintsAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.compileconstraintsasync). Вы не можете задать язык пользовательского списка напрямую. Список будет обрабатываться с использованием языка распознавателя.

Грамматика SRGS – это открытый формат XML, представляемый классом [**SpeechRecognitionGrammarFileConstraint**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionGrammarFileConstraint). В отличие от пользовательских списков язык грамматики можно указать в разметке SRGS. [**Компилеконстраинтсасинк**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.compileconstraintsasync) завершается с [**спичрекогнитионресултстатус**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionResultStatus) **топиклангуаженотсуппортед** , если распознаватель не инициализирован на том же языке, что и разметка SRGS.

## <a name="related-articles"></a>Связанные статьи

**Разработчикам**

* [Взаимодействия с помощью речи](speech-interactions.md)

**Проектировщикам**

* [Рекомендации по проектированию голосовых функций](https://docs.microsoft.com/windows/uwp/input-and-devices/speech-interactions)

**Примеры**

* [Пример распознавания речи и синтеза речи](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/SpeechRecognitionAndSynthesis)
 

 




