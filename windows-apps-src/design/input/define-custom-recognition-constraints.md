---
Description: Из этой статьи вы узнаете, как определять и использовать настраиваемые ограничения для распознавания речи.
title: Определение настраиваемых ограничений распознавания
ms.assetid: 26289DE5-6AC9-42C3-A160-E522AE62D2FC
label: Define custom recognition constraints
template: detail.hbs
keywords: речь, голос, распознавание речи, естественный язык, диктовка, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 5b94c946222f510c7f1b1f7619b67ee83e6c2256
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258005"
---
# <a name="define-custom-recognition-constraints"></a>Определение настраиваемых ограничений распознавания

Из этой статьи вы узнаете, как определять и использовать настраиваемые ограничения для распознавания речи.

> **Важные API-интерфейсы**: [**SpeechRecognitionTopicConstraint**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionTopicConstraint), [**SpeechRecognitionListConstraint**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionListConstraint), [**SpeechRecognitionGrammarFileConstraint**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionGrammarFileConstraint)

Для распознавания речи требуется по крайней мере одно ограничение, чтобы определить распознаваемый словарь. Если не задано ни одно ограничение, будет использоваться предопределенная грамматика речевого ввода универсальных приложений для Windows. См. [Распознавание речи](speech-recognition.md).

## <a name="add-constraints"></a>Добавление ограничений

Для добавления ограничений в распознаватель речи используйте свойство [**SpeechRecognizer.Constraints**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.constraints).

Здесь мы рассмотрим три вида ограничений распознавания речи, используемых в пределах приложения. (Для ограничений по голосовым командам Кортаны см. статью [Запуск приложения переднего плана с голосовыми командами в Кортане](https://docs.microsoft.com/cortana/voice-commands/launch-a-foreground-app-with-voice-commands-in-cortana).)

- [**Спичрекогнитионтопикконстраинт**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionTopicConstraint)— ограничение на основе предопределенной грамматики (Диктовка или поиск в Интернете).
- [**Спичрекогнитионлистконстраинт**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionListConstraint)— ограничение на основе списка слов или фраз.
- [**Спичрекогнитионграммарфилеконстраинт**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionGrammarFileConstraint)— ограничение, определенное в файле спецификации грамматики распознавания речи (SRGS).

Распознаватель речи может иметь одну коллекцию ограничений. Только эти комбинации ограничений являются допустимыми:

- Ограничение по одной теме (диктовка или веб-поиск)
- Для Windows 10 Fall Creators Update (версия 10.0.16299.15) и более поздних версий ограничение по одной теме можно использовать совместно с ограничением на основе списка
- Комбинация ограничений на базе списка и (или) ограничений на базе файла грамматики.

> [!Important]
> Перед запуском процесса распознавания вызовите метод **[SpeechRecognizer.CompileConstraintsAsync](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.compileconstraintsasync)** для компиляции ограничений.

## <a name="specify-a-web-search-grammar-speechrecognitiontopicconstraint"></a>Задание грамматики веб-поиска (SpeechRecognitionTopicConstraint)

Ограничения по теме (грамматика диктовки или веб-поиска) необходимо добавить к коллекции ограничений распознавателя речи.

> [!NOTE]
> Вы можете использовать [SpeechRecognitionListConstraint](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionListConstraint) вместе с [SpeechRecognitionTopicConstraint](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionTopicConstraint) для повышения точности диктовки путем предоставления набора ключевых слов конкретного домена, которые с большой вероятностью будут использоваться во время диктовки.

Здесь мы добавляем к коллекции ограничений грамматику веб-поиска.

```CSharp
private async void WeatherSearch_Click(object sender, RoutedEventArgs e)
{
    // Create an instance of SpeechRecognizer.
    var speechRecognizer = new Windows.Media.SpeechRecognition.SpeechRecognizer();

    // Listen for audio input issues.
    speechRecognizer.RecognitionQualityDegrading += speechRecognizer_RecognitionQualityDegrading;

    // Add a web search grammar to the recognizer.
    var webSearchGrammar = new Windows.Media.SpeechRecognition.SpeechRecognitionTopicConstraint(Windows.Media.SpeechRecognition.SpeechRecognitionScenario.WebSearch, "webSearch");


    speechRecognizer.UIOptions.AudiblePrompt = "Say what you want to search for...";
    speechRecognizer.UIOptions.ExampleText = @"Ex. 'weather for London'";
    speechRecognizer.Constraints.Add(webSearchGrammar);

    // Compile the constraint.
    await speechRecognizer.CompileConstraintsAsync();

    // Start recognition.
    Windows.Media.SpeechRecognition.SpeechRecognitionResult speechRecognitionResult = await speechRecognizer.RecognizeWithUIAsync();
    //await speechRecognizer.RecognizeWithUIAsync();

    // Do something with the recognition result.
    var messageDialog = new Windows.UI.Popups.MessageDialog(speechRecognitionResult.Text, "Text spoken");
    await messageDialog.ShowAsync();
}
```

## <a name="specify-a-programmatic-list-constraint-speechrecognitionlistconstraint"></a>Задание программного ограничения на базе списка (SpeechRecognitionListConstraint)

Ограничения на базе списка необходимо добавить к коллекции ограничений распознавателя речи.

Помните следующее.

- В коллекцию ограничений можно добавить несколько ограничений на базе списка.
- Вы можете использовать любую коллекцию, которая реализует **IIterable&lt;String&gt;** для значений строк.

Здесь мы программно задаем массив слов в качестве ограничения на базе списка и добавляем его к коллекции ограничений распознавателя речи.

```CSharp
private async void YesOrNo_Click(object sender, RoutedEventArgs e)
{
    // Create an instance of SpeechRecognizer.
    var speechRecognizer = new Windows.Media.SpeechRecognition.SpeechRecognizer();

    // You could create this array dynamically.
    string[] responses = { "Yes", "No" };


    // Add a list constraint to the recognizer.
    var listConstraint = new Windows.Media.SpeechRecognition.SpeechRecognitionListConstraint(responses, "yesOrNo");

    speechRecognizer.UIOptions.ExampleText = @"Ex. 'yes', 'no'";
    speechRecognizer.Constraints.Add(listConstraint);

    // Compile the constraint.
    await speechRecognizer.CompileConstraintsAsync();

    // Start recognition.
    Windows.Media.SpeechRecognition.SpeechRecognitionResult speechRecognitionResult = await speechRecognizer.RecognizeWithUIAsync();

    // Do something with the recognition result.
    var messageDialog = new Windows.UI.Popups.MessageDialog(speechRecognitionResult.Text, "Text spoken");
    await messageDialog.ShowAsync();
}
```

## <a name="specify-an-srgs-grammar-constraint-speechrecognitiongrammarfileconstraint"></a>Задание ограничения на базе грамматики SRGS (SpeechRecognitionGrammarFileConstraint)

Файлы грамматики SRGS необходимо добавить к коллекции ограничений распознавателя речи.

Спецификация грамматики распознавания речи (SRGS) версии 1.0 является стандартным для отрасли языком разметки для создания грамматик в формате XML для распознавания речи. Хотя универсальные приложения для Windows предоставляют альтернативы использованию SRGS для создания грамматик распознавания речи, для вас может оказаться так, что лучшие результаты дает создание грамматик с помощью SRGS, особенно для более сложных сценариев распознавания речи.

Грамматики SRGS предоставляют полный набор функций для разработки сложного голосового взаимодействия с вашими приложениями. Например, с помощью грамматики SRGS можно сделать следующее.

- Задать порядок, в котором слова и фразы должны произноситься для распознавания.
- Группировать слова из нескольких списков и фраз для распознавания.
- Добавлять ссылки на другие грамматики.
- Присваивать "вес" альтернативному слову или фразе, чтобы увеличить или уменьшить вероятность их использования в поиске соответствий речевому вводу.
- Включать необязательные слова или фразы.
- Использовать особые правила, помогающие отфильтровать неопределенный или непредвиденный ввод, например случайную речь, которая не соответствует грамматике, или фоновый шум.
- Использовать семантики, чтобы определить, что означает распознавание речи для вашего приложения.
- Задать произношение в соответствии с грамматикой или посредством ссылки на лексикон.

Подробнее об элементах и атрибутах SRGS см. в разделе [Справочные материалы по XML для грамматики SRGS](https://msdn.microsoft.com/library/hh361653). Начните создавать грамматику SRGS с изучения раздела о [создании основ грамматики XML](https://msdn.microsoft.com/library/hh361658).

Помните следующее.

- В коллекцию ограничений можно добавить несколько ограничений на базе файла грамматики.
- Используйте расширение GRXML для документов грамматик на базе XML, соответствующих правилам SRGS.

В данном примере используется грамматика SRGS, определенная в файле с именем srgs.grxml (описано далее). В свойствах файла для параметра **Действие пакета** задано значение **Содержимое**, а для параметра **Копировать в выходной каталог** — значение **Всегда копировать**:

```CSharp
private async void Colors_Click(object sender, RoutedEventArgs e)
{
    // Create an instance of SpeechRecognizer.
    var speechRecognizer = new Windows.Media.SpeechRecognition.SpeechRecognizer();

    // Add a grammar file constraint to the recognizer.
    var storageFile = await Windows.Storage.StorageFile.GetFileFromApplicationUriAsync(new Uri("ms-appx:///Colors.grxml"));
    var grammarFileConstraint = new Windows.Media.SpeechRecognition.SpeechRecognitionGrammarFileConstraint(storageFile, "colors");

    speechRecognizer.UIOptions.ExampleText = @"Ex. 'blue background', 'green text'";
    speechRecognizer.Constraints.Add(grammarFileConstraint);

    // Compile the constraint.
    await speechRecognizer.CompileConstraintsAsync();

    // Start recognition.
    Windows.Media.SpeechRecognition.SpeechRecognitionResult speechRecognitionResult = await speechRecognizer.RecognizeWithUIAsync();

    // Do something with the recognition result.
    var messageDialog = new Windows.UI.Popups.MessageDialog(speechRecognitionResult.Text, "Text spoken");
    await messageDialog.ShowAsync();
}
```

Этот файл SRGS (srgs.grxml) включает теги семантической интерпретации. Эти теги обеспечивают механизм возврата данных о проверке соответствия грамматике в ваше приложение. Грамматики должны соответствовать спецификации [семантической интерпретации консорциум W3C (W3C) для распознавания речи (СИСР) 1,0](https://www.w3.org/TR/semantic-interpretation/) .

Здесь мы ожидаем передачи данных для вариантов yes и no.

```CSharp
<grammar xml:lang="en-US" 
         root="yesOrNo"
         version="1.0" 
         tag-format="semantics/1.0"
         xmlns="http://www.w3.org/2001/06/grammar">

    <!-- The following rules recognize variants of yes and no. -->
      <rule id="yesOrNo">
         <one-of>
            <item>
              <one-of>
                 <item>yes</item>
                 <item>yeah</item>
                 <item>yep</item>
                 <item>yup</item>
                 <item>un huh</item>
                 <item>yay yus</item>
              </one-of>
              <tag>out="yes";</tag>
            </item>
            <item>
              <one-of>
                 <item>no</item>
                 <item>nope</item>
                 <item>nah</item>
                 <item>uh uh</item>
               </one-of>
               <tag>out="no";</tag>
            </item>
         </one-of>
      </rule>
</grammar>
```

## <a name="manage-constraints"></a>Управление ограничениями

После загрузки коллекции ограничений для распознавания ваше приложение может определять, какие ограничения нужно включать для операций распознавания, путем установки для свойства ограничения [**IsEnabled**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.ispeechrecognitionconstraint.isenabled) значения **true** или **false**. Значение по умолчанию — **true**.

Обычно однократная загрузка ограничений с последующим включением или выключением по необходимости более эффективна, чем загрузка, выгрузка и компиляция ограничений для каждой операции распознавания. Используйте свойство [**IsEnabled**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.ispeechrecognitionconstraint.isenabled) при необходимости.

С помощью нормирования числа ограничений можно задать предел для количества данных, которое необходимо распознавателю речи для поиска соответствия речевому вводу. Это может улучшить как производительность, так и точность распознавания речи.

Решите, какие ограничения включить, на основе фраз, которые ожидает ваше приложение в контексте текущей операции распознавания. Например, если в текущем контексте приложения отображается цвет, то, возможно, вам не потребуется включать ограничение, распознающее названия животных.

Чтобы предложить пользователю, что сказать, используйте свойства [**SpeechRecognizerUIOptions.AudiblePrompt**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizeruioptions.audibleprompt) и [**SpeechRecognizerUIOptions.ExampleText**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizeruioptions.exampletext), которые задаются с помощью свойства [**SpeechRecognizer.UIOptions**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.uioptions). Информирование пользователей о том, что они могут сказать во время операции распознавания, повышает вероятность произнесения фразы, которая может быть сочтена удовлетворяющей активному ограничению.

## <a name="related-articles"></a>Связанные статьи

- [Взаимодействия с помощью речи](speech-interactions.md)

### <a name="samples"></a>Примеры

- [Пример распознавания речи и синтеза речи](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/SpeechRecognitionAndSynthesis)