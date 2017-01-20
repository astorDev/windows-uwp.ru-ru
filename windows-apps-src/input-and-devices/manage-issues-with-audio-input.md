---
author: Karl-Bridge-Microsoft
Description: "Узнайте, как решать проблемы с точностью распознавания речи, связанные с качеством голосового ввода."
title: "Решение проблем голосового ввода"
ms.assetid: 3E36C683-C96A-4FEE-AD52-FDB87E0CC299
label: Manage audio input issues
template: detail.hbs
keywords: "речь, голос, распознавание речи, естественный язык, диктовка, ввод, взаимодействие с пользователем"
ms.author: kbridge
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: 482530931fe5764f65d2564107318c272c5c7b7f
ms.openlocfilehash: a6f6e4c04f608898d2871113f38fe647b62bf8a5

---

# <a name="manage-issues-with-audio-input"></a>Решение проблем голосового ввода
<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Узнайте, как решать проблемы с точностью распознавания речи, связанные с качеством голосового ввода.

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226)</li>
<li>[**RecognitionQualityDegrading**](https://msdn.microsoft.com/library/windows/apps/dn653243)</li>
<li>[**SpeechRecognitionAudioProblem**](https://msdn.microsoft.com/library/windows/apps/dn631406)</li>

</ul>
</div>


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
    speechRecognizer.UIOptions.ExampleText = @"Ex. &#39;weather for London&#39;";
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


* [Взаимодействия с помощью голосовых функций](speech-interactions.md)

**Примеры**
* [Пример распознавания и синтеза речи](http://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 







<!--HONumber=Dec16_HO3-->


