---
Description: Узнайте, как решать проблемы с точностью распознавания речи, связанные с качеством голосового ввода.
title: Решение проблем голосового ввода
ms.assetid: 3E36C683-C96A-4FEE-AD52-FDB87E0CC299
label: Manage audio input issues
template: detail.hbs
keywords: речь, голос, распознавание речи, естественный язык, диктовка, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 6281165d64b8e6e3f77807dbafd6bfff1dd0704f
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258331"
---
# <a name="manage-issues-with-audio-input"></a>Решение проблем голосового ввода


Узнайте, как решать проблемы с точностью распознавания речи, связанные с качеством голосового ввода.

> **Важные API-интерфейсы**: [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer), [**RecognitionQualityDegrading**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognitionqualitydegrading), [**SpeechRecognitionAudioProblem**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionAudioProblem)


## <a name="assess-audio-input-quality"></a>Оценка качества голосового ввода


Если распознавание речи включено, используйте событие [**RecognitionQualityDegrading**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognitionqualitydegrading) в распознавателе речи, чтобы определить, могут ли одна или несколько звуковых проблем мешать речевому вводу. Аргумент события ([**SpeechRecognitionQualityDegradingEventArgs**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionQualityDegradingEventArgs)) предоставляет свойство [**Problem**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionqualitydegradingeventargs.problem), в котором описываются обнаруженные проблемы голосового ввода.

На распознавание может повлиять чрезмерный фоновый шум, отключенный микрофон, а также громкость или скорость говорящего.

Здесь мы настраиваем распознаватель речи и начинаем прослушивание события [**RecognitionQualityDegrading**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognitionqualitydegrading).

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
    speechRecognizer.UIOptions.ExampleText = "Ex. 'weather for London'";
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

## <a name="manage-the-speech-recognition-experience"></a>Управление распознаванием речи


Используйте описание, предоставленное свойством [**Problem**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionqualitydegradingeventargs.problem), чтобы обеспечить улучшенные условия для распознавания.

Здесь мы создаем обработчик события [**RecognitionQualityDegrading**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognitionqualitydegrading), который отслеживает низкий уровень громкости. Затем мы используем объект [**SpeechSynthesizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechSynthesis.SpeechSynthesizer), чтобы попросить пользователя говорить громче.

```CSharp
private async void speechRecognizer_RecognitionQualityDegrading(
    Windows.Media.SpeechRecognition.SpeechRecognizer sender,
    Windows.Media.SpeechRecognition.SpeechRecognitionQualityDegradingEventArgs args)
{
    // Create an instance of a speech synthesis engine (voice).
    var speechSynthesizer =
        new Windows.Media.SpeechSynthesis.SpeechSynthesizer();

    // If input speech is too quiet, prompt the user to speak louder.
    if (args.Problem == Windows.Media.SpeechRecognition.SpeechRecognitionAudioProblem.TooQuiet)
    {
        // Generate the audio stream from plain text.
        Windows.Media.SpeechSynthesis.SpeechSynthesisStream stream;
        try
        {
            stream = await speechSynthesizer.SynthesizeTextToStreamAsync("Try speaking louder");
            stream.Seek(0);
        }
        catch (Exception)
        {
            stream = null;
        }

        // Send the stream to the MediaElement declared in XAML.
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.High, () =>
        {
            this.media.SetSource(stream, stream.ContentType);
        });
    }
}
```

## <a name="related-articles"></a>Связанные статьи


* [Взаимодействия с помощью речи](speech-interactions.md)

**Примеры**
* [Пример распознавания речи и синтеза речи](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/SpeechRecognitionAndSynthesis)
 

 




