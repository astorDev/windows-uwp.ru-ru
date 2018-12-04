---
Description: Learn how to manage issues with speech-recognition accuracy caused by audio-input quality.
title: Решение проблем голосового ввода
ms.assetid: 3E36C683-C96A-4FEE-AD52-FDB87E0CC299
label: Manage audio input issues
template: detail.hbs
keywords: речь, голос, распознавание речи, естественный язык, диктовка, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 5c33e2890ce962f1321ef40ca0e0605e0f4e1f5f
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8467482"
---
# <a name="manage-issues-with-audio-input"></a>Решение проблем голосового ввода


Узнайте, как решать проблемы с точностью распознавания речи, связанные с качеством голосового ввода.

> **Важные API-интерфейсы**: [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226), [**RecognitionQualityDegrading**](https://msdn.microsoft.com/library/windows/apps/dn653243), [**SpeechRecognitionAudioProblem**](https://msdn.microsoft.com/library/windows/apps/dn631406)


## <a name="assess-audio-input-quality"></a>Оценка качества голосового ввода


Если распознавание речи включено, используйте событие [**RecognitionQualityDegrading**](https://msdn.microsoft.com/library/windows/apps/dn653243) в распознавателе речи, чтобы определить, могут ли одна или несколько звуковых проблем мешать речевому вводу. Аргумент события ([**SpeechRecognitionQualityDegradingEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn631430)) предоставляет свойство [**Problem**](https://msdn.microsoft.com/library/windows/apps/dn631431), в котором описываются обнаруженные проблемы голосового ввода.

На распознавание может повлиять чрезмерный фоновый шум, отключенный микрофон, а также громкость или скорость говорящего.

Здесь мы настраиваем распознаватель речи и начинаем прослушивание события [**RecognitionQualityDegrading**](https://msdn.microsoft.com/library/windows/apps/dn653243).

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


Используйте описание, предоставленное свойством [**Problem**](https://msdn.microsoft.com/library/windows/apps/dn631431), чтобы обеспечить улучшенные условия для распознавания.

Здесь мы создаем обработчик события [**RecognitionQualityDegrading**](https://msdn.microsoft.com/library/windows/apps/dn653243), который отслеживает низкий уровень громкости. Затем мы используем объект [**SpeechSynthesizer**](https://msdn.microsoft.com/library/windows/apps/dn298152), чтобы попросить пользователя говорить громче.

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

## <a name="related-articles"></a>Связанные разделы


* [Взаимодействие с помощью голосовых функций](speech-interactions.md)

**Примеры**
* [Пример распознавания и синтеза речи](http://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 




