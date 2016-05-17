---
author: Karl-Bridge-Microsoft
Description: Узнайте, как выбрать установленный язык для распознавания речи.
title: Определение языка для распознавателя речи
ms.assetid: 4C463A1B-AF6A-46FD-A839-5D6724955B38
label: Specify the speech recognizer language
template: detail.hbs
---

# Определение языка для распознавателя речи


Узнайте, как выбрать установленный язык для распознавания речи.




**Важные API**

-   [**SupportedTopicLanguages**](https://msdn.microsoft.com/library/windows/apps/dn653251)
-   [**SupportedGrammarLanguages**](https://msdn.microsoft.com/library/windows/apps/dn653250)
-   [**Язык**](https://msdn.microsoft.com/library/windows/apps/br206804)


Здесь мы перечисляем языки, установленные в системе, определяем язык, используемый по умолчанию, и выбираем другой язык для распознавания.

**Необходимые условия  **

В данной статье используются материалы статьи [Распознавание речи](speech-recognition.md).

От вас требуется понимание основных принципов распознавания речи и знание соответствующих ограничений.

Если вы — начинающий разработчик приложений универсальной платформы Windows (UWP), прочитайте указанные ниже статьи, чтобы ознакомиться с описанными здесь технологиями.

-   [Создание первого приложения](https://msdn.microsoft.com/library/windows/apps/bg124288)
-   Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

**Рекомендации по взаимодействию с пользователем.  **

Полезные подсказки по разработке практичного и привлекательного приложения с поддержкой голосовых функций см. в разделе [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121).

## <span id="Identify_the_default_language"></span><span id="identify_the_default_language"></span><span id="IDENTIFY_THE_DEFAULT_LANGUAGE"></span>Определение языка, используемого по умолчанию


В качестве языка по умолчанию распознаватель речи использует язык для голосовых функций, установленный в системе. Этот язык устанавливает пользователь на устройстве, в разделе «Параметры» &gt; «Система» &gt; «Речь» &gt; «Язык голосовых функций».

Чтобы определить язык по умолчанию, нужно проверить статическое свойство [**SystemSpeechLanguage**](https://msdn.microsoft.com/library/windows/apps/dn653252).

```CSharp
var language = SpeechRecognizer.SystemSpeechLanguage; </code></pre></td>
</tr>
</tbody>
</table>
```

## <span id="Confirm_an_installed_language"></span><span id="confirm_an_installed_language"></span><span id="CONFIRM_AN_INSTALLED_LANGUAGE"></span>Подтверждение установленного языка


На разных устройствах установленные языки могут отличаться. Следует проверить наличие языка, если с ним связано какое-либо ограничение, которое имеет для вас значение.

**Примечание.**  После установки нового языкового пакета требуется перезагрузка. Если указанный язык не поддерживается или его установка не завершена, возникает исключение с кодом ошибки SPERR\_NOT\_FOUND (0x8004503a).

 

Определите поддерживаемые языки на устройстве, проверив одно из двух статических свойств класса [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226):

-   [
              **SupportedTopicLanguages**
            ](https://msdn.microsoft.com/library/windows/apps/dn653251) — коллекция объектов [**Language**](https://msdn.microsoft.com/library/windows/apps/br206804), используемых с предустановленными правилами грамматики для диктовки и поиска в Интернете.

-   [
              **SupportedGrammarLanguages**
            ](https://msdn.microsoft.com/library/windows/apps/dn653250) — коллекция объектов [**Language**](https://msdn.microsoft.com/library/windows/apps/br206804), используемых с ограничением по списку или файлом определения грамматики для распознавания речи (SRGS).

## <span id="Specify_a_language"></span><span id="specify_a_language"></span><span id="SPECIFY_A_LANGUAGE"></span>Определение языка


Чтобы определить язык, передайте объект [**Language**](https://msdn.microsoft.com/library/windows/apps/br206804) в конструктор [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226).

Здесь мы определяем en-US как язык для распознавания.

<span codelanguage="CSharp"></span>
```CSharp
<colgroup>
<col width="100%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">C#</th>
</tr>
</thead>
<tbody>
<tr class="odd">
var language = new Windows.Globalization.Language(“en-US”); 
var recognizer = new SpeechRecognizer(language); 
```

## <span id="Remarks"></span><span id="remarks"></span><span id="REMARKS"></span>Комментарии


Ограничение по одной теме можно настроить, добавив [**SpeechRecognitionTopicConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631446) в коллекцию [**Constraints**](https://msdn.microsoft.com/library/windows/apps/dn653241) средства [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226), а затем вызвав [**CompileConstraintsAsync**](https://msdn.microsoft.com/library/windows/apps/dn653240). Если распознаватель не удалось инициализировать с поддерживаемым языком темы, возвращается состояние [**SpeechRecognitionResultStatus**](https://msdn.microsoft.com/library/windows/apps/dn631433) для **TopicLanguageNotSupported**.

Ограничение по списку можно настроить, добавив [**SpeechRecognitionListConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631421) в коллекцию [**Constraints**](https://msdn.microsoft.com/library/windows/apps/dn653241) средства [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226), а затем вызвав [**CompileConstraintsAsync**](https://msdn.microsoft.com/library/windows/apps/dn653240). Вы не можете задать язык пользовательского списка напрямую. Список будет обрабатываться с использованием языка распознавателя.

Грамматика SRGS – это открытый формат XML, представляемый классом [**SpeechRecognitionGrammarFileConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631412). В отличие от пользовательских списков язык грамматики можно указать в разметке SRGS. Выполнение [
              **CompileConstraintsAsync**
            ](https://msdn.microsoft.com/library/windows/apps/dn653240) завершается ошибкой с состоянием [**SpeechRecognitionResultStatus**](https://msdn.microsoft.com/library/windows/apps/dn631433) для **TopicLanguageNotSupported**, если распознаватель не удалось инициализировать на языке разметки SRGS.

## <span id="related_topics"></span>Связанные разделы


**Разработчикам**
* Взаимодействие с помощью голосовых функций
* Проектировщикам
* [Рекомендации по проектированию голосовых функций](http://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 






<!--HONumber=May16_HO2-->


