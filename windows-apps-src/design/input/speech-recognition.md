---
author: Karl-Bridge-Microsoft
Description: Use speech recognition to provide input, specify an action or command, and accomplish tasks.
title: Распознавание речи
ms.assetid: 553C0FB7-35BC-4894-9EF1-906139E17552
label: Speech recognition
template: detail.hbs
keywords: речь, голос, распознавание речи, естественный язык, диктовка, ввод, взаимодействие с пользователем
ms.author: kbridge
ms.date: 10/25/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: b9148b2d57c55bdff09be9a9d6bb8a6b65d93f12
ms.sourcegitcommit: bdc40b08cbcd46fc379feeda3c63204290e055af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "6161313"
---
# <a name="speech-recognition"></a>Распознавание речи


С помощью функции распознавания речи можно вводить данные, указывать действия или команды и выполнять задачи.

> **Важные API-интерфейсы**: [**Windows.Media.SpeechRecognition**](https://msdn.microsoft.com/library/windows/apps/dn653262)

Для распознавания речи используется специальная среда выполнения, API распознавания для программирования среды выполнения, готовые грамматики для диктовки и веб-поиска, а также системный пользовательский интерфейс по умолчанию, который помогает пользователям обнаруживать и использовать функции распознавания речи.

## <a name="configure-speech-recognition"></a>Настройка распознавания речи

Для поддержки функции распознавания речи с вашим приложением, пользователь должен подключиться и включить микрофон на своем устройстве и принять политика конфиденциальности Майкрософт предоставления разрешений для вашего приложения использовать его.

Автоматически попросить пользователя с помощью диалогового окна системы, запрашивающих разрешение на доступ и использовать микрофон пользователя аудио канал (пример [распознавания речи и синтеза речи пример](http://go.microsoft.com/fwlink/p/?LinkID=619897) ниже), просто набор **микрофон** [устройства возможность](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-devicecapability) в [манифесте пакета приложения](https://docs.microsoft.com/uwp/schemas/appxpackage/appx-package-manifest). Дополнительные сведения см. в разделе [объявления возможностей приложения](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).

![Политика конфиденциальности для микрофона доступа](images/speech/privacy.png)

Если пользователь нажимает кнопку Да, чтобы предоставить доступ к микрофону, ваше приложение будет добавлен в список утвержденных приложений на параметры "->" Конфиденциальность "->" микрофон страницы. Тем не менее как пользователь может выбрать отключить этот параметр в любое время, вы убедитесь, что ваше приложение имеет доступ к микрофону прежде чем использовать его.

Если вы хотите поддерживать диктовки, Кортана, или других распознавания речи служб (например, [предварительно заданной грамматики](#predefined-grammars) определены в ограничение по одной теме), необходимо также подтвердить, что **распознавания речи Online** (параметры "->" Конфиденциальность "->" голосовые функции) — включен.

Этот фрагмент кода показывает, как ваше приложение может проверить, если присутствует микрофона и если он имеет разрешение использовать его.

```csharp
public class AudioCapturePermissions
{
    // If no microphone is present, an exception is thrown with the following HResult value.
    private static int NoCaptureDevicesHResult = -1072845856;

    /// <summary>
    /// Note that this method only checks the Settings->Privacy->Microphone setting, it does not handle
    /// the Cortana/Dictation privacy check.
    ///
    /// You should perform this check every time the app gets focus, in case the user has changed
    /// the setting while the app was suspended or not in focus.
    /// </summary>
    /// <returns>True, if the microphone is available.</returns>
    public async static Task<bool> RequestMicrophonePermission()
    {
        try
        {
            // Request access to the audio capture device.
            MediaCaptureInitializationSettings settings = new MediaCaptureInitializationSettings();
            settings.StreamingCaptureMode = StreamingCaptureMode.Audio;
            settings.MediaCategory = MediaCategory.Speech;
            MediaCapture capture = new MediaCapture();

            await capture.InitializeAsync(settings);
        }
        catch (TypeLoadException)
        {
            // Thrown when a media player is not available.
            var messageDialog = new Windows.UI.Popups.MessageDialog("Media player components are unavailable.");
            await messageDialog.ShowAsync();
            return false;
        }
        catch (UnauthorizedAccessException)
        {
            // Thrown when permission to use the audio capture device is denied.
            // If this occurs, show an error or disable recognition functionality.
            return false;
        }
        catch (Exception exception)
        {
            // Thrown when an audio capture device is not present.
            if (exception.HResult == NoCaptureDevicesHResult)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog("No Audio Capture devices are present on this system.");
                await messageDialog.ShowAsync();
                return false;
            }
            else
            {
                throw;
            }
        }
        return true;
    }
}
```

```cpp
/// <summary>
/// Note that this method only checks the Settings->Privacy->Microphone setting, it does not handle
/// the Cortana/Dictation privacy check.
///
/// You should perform this check every time the app gets focus, in case the user has changed
/// the setting while the app was suspended or not in focus.
/// </summary>
/// <returns>True, if the microphone is available.</returns>
IAsyncOperation<bool>^  AudioCapturePermissions::RequestMicrophonePermissionAsync()
{
    return create_async([]() 
    {
        try
        {
            // Request access to the audio capture device.
            MediaCaptureInitializationSettings^ settings = ref new MediaCaptureInitializationSettings();
            settings->StreamingCaptureMode = StreamingCaptureMode::Audio;
            settings->MediaCategory = MediaCategory::Speech;
            MediaCapture^ capture = ref new MediaCapture();

            return create_task(capture->InitializeAsync(settings))
                .then([](task<void> previousTask) -> bool
            {
                try
                {
                    previousTask.get();
                }
                catch (AccessDeniedException^)
                {
                    // Thrown when permission to use the audio capture device is denied.
                    // If this occurs, show an error or disable recognition functionality.
                    return false;
                }
                catch (Exception^ exception)
                {
                    // Thrown when an audio capture device is not present.
                    if (exception->HResult == AudioCapturePermissions::NoCaptureDevicesHResult)
                    {
                        auto messageDialog = ref new Windows::UI::Popups::MessageDialog("No Audio Capture devices are present on this system.");
                        create_task(messageDialog->ShowAsync());
                        return false;
                    }

                    throw;
                }
                return true;
            });
        }
        catch (Platform::ClassNotRegisteredException^ ex)
        {
            // Thrown when a media player is not available. 
            auto messageDialog = ref new Windows::UI::Popups::MessageDialog("Media Player Components unavailable.");
            create_task(messageDialog->ShowAsync());
            return create_task([] {return false; });
        }
    });
}
```

```js
var AudioCapturePermissions = WinJS.Class.define(
    function () { }, {},
    {
        requestMicrophonePermission: function () {
            /// <summary>
            /// Note that this method only checks the Settings->Privacy->Microphone setting, it does not handle
            /// the Cortana/Dictation privacy check.
            ///
            /// You should perform this check every time the app gets focus, in case the user has changed
            /// the setting while the app was suspended or not in focus.
            /// </summary>
            /// <returns>True, if the microphone is available.</returns>
            return new WinJS.Promise(function (completed, error) {

                try {
                    // Request access to the audio capture device.
                    var captureSettings = new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    captureSettings.streamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.audio;
                    captureSettings.mediaCategory = Windows.Media.Capture.MediaCategory.speech;

                    var capture = new Windows.Media.Capture.MediaCapture();
                    capture.initializeAsync(captureSettings).then(function () {
                        completed(true);
                    },
                    function (error) {
                        // Audio Capture can fail to initialize if there's no audio devices on the system, or if
                        // the user has disabled permission to access the microphone in the Privacy settings.
                        if (error.number == -2147024891) { // Access denied (microphone disabled in settings)
                            completed(false);
                        } else if (error.number == -1072845856) { // No recording device present.
                            var messageDialog = new Windows.UI.Popups.MessageDialog("No Audio Capture devices are present on this system.");
                            messageDialog.showAsync();
                            completed(false);
                        } else {
                            error(error);
                        }
                    });
                } catch (exception) {
                    if (exception.number == -2147221164) { // REGDB_E_CLASSNOTREG
                        var messageDialog = new Windows.UI.Popups.MessageDialog("Media Player components not available on this system.");
                        messageDialog.showAsync();
                        return false;
                    }
                }
            });
        }
    })
```

## <a name="recognize-speech-input"></a>Распознавание речевого ввода

В *ограничении* определяются слова и фразы (словарь), которые приложение распознает в речевом вводе. Ограничения являются основой распознавания речи и дают вашему приложению возможность управлять точностью распознавания.

Можно использовать следующие типы ограничений для распознавания речевого ввода.

### <a name="predefined-grammars"></a>Предопределенные грамматики

Предопределенные грамматики диктовки и веб-поиска обеспечивают распознавание речи в приложении без необходимости создавать грамматику. Когда используются эти грамматики, распознавание речи выполняется удаленной веб-службой, а результаты возвращаются на устройство.

Стандартная грамматика для диктовки в свободной форме может распознавать большинство слов и фраз, произносимых пользователем на данном языке, и оптимизирована для распознавания коротких фраз. Предопределенная грамматика для диктовки используется, если для объекта [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226) не заданы никакие ограничения. Диктовка в свободной форме удобна, если не нужно ограничивать область высказываний пользователя. Обычно она используется для создания текстов заметок и диктовки сообщений.

Грамматика веб-поиска, например грамматика диктовки, содержит большое количество слов и фраз, которые пользователь может произнести. Однако она оптимизирована для распознавания терминов, которыми люди обычно используются, выполняя поиск в Интернете.

**Примечание**поскольку предопределенные грамматики диктовки и веб поиска могут иметь большой размер, и они находятся в оперативном режиме (а не на устройстве), уступать в производительности так же быстро, как и в случае с настраиваемую грамматику, установленных на устройстве.     

Эти предопределенные грамматики можно использовать для распознавания до ввода речи продолжительностью до 10секунд, и для этого не потребуется никаких доработок с вашей стороны. Однако потребуется подключение к сети.

Чтобы использовать ограничения веб-службы, в меню **Параметры** необходимо включить поддержку речевого ввода и диктовки в разделе "Познакомьтесь со мной" в разделе **Параметры -> Конфиденциальность -> Голосовые функции, рукописный ввод и ввод с клавиатуры**.

Здесь показано, как проверить, включен ли голосовой ввод, и если нет, как открыть страницу "Параметры" -> "Конфиденциальность" -> "Голосовые функции, рукописный ввод и ввод с клавиатуры".

Сначала мы инициализируем глобальную переменную (HResultPrivacyStatementDeclined) до значения HResult 0x80045509. См. раздел [Обработка исключений в C\# или Visual Basic](https://msdn.microsoft.com/library/windows/apps/dn532194).

```csharp
private static uint HResultPrivacyStatementDeclined = 0x80045509;
```

Затем мы отберем все стандартные исключения во время распознавания и проверим, равно ли значение [**HResult**](https://msdn.microsoft.com/library/windows/apps/br206579) значению переменной HResultPrivacyStatementDeclined. При положительном результате мы отобразим предупреждение и вызовем `await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-accounts"));`, чтобы открыть страницу "Параметры".

```csharp
catch (Exception exception)
{
  // Handle the speech privacy policy error.
  if ((uint)exception.HResult == HResultPrivacyStatementDeclined)
  {
    resultTextBlock.Visibility = Visibility.Visible;
    resultTextBlock.Text = "The privacy statement was declined." + 
      "Go to Settings -> Privacy -> Speech, inking and typing, and ensure you" +
      "have viewed the privacy policy, and 'Get To Know You' is enabled.";
    // Open the privacy/speech, inking, and typing settings page.
    await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-accounts")); 
  }
  else
  {
    var messageDialog = new Windows.UI.Popups.MessageDialog(exception.Message, "Exception");
    await messageDialog.ShowAsync();
  }
}
```

См. в разделе [**SpeechRecognitionTopicConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631446).

### <a name="programmatic-list-constraints"></a>Программные ограничения 

Программные ограничения-списки представляют упрощенный подход к созданию простой грамматики с использованием списка слов или фраз. Для распознавания коротких четких фраз удобно использовать ограничения-списки. Явно указание всех слов в грамматике также повышается точность распознавания, так как подсистема распознавания речи должна обрабатывать голосовые данные только в рамках подтверждения соответствия. Список можно также обновлять программными средствами.

Ограничение-список состоит из массива строк, представляющих ввод речи, принимаемый приложением для операции распознавания. Чтобы создать ограничение-список в приложении, создайте объект ограничения-списка для распознавания речи и передайте ему массив строк. Затем добавьте этот объект в коллекцию ограничений распознавателя. Когда распознаватель речи распознает любую из строк в массиве, распознавание завершается успешно.

См. в разделе [**SpeechRecognitionListConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631421).

### <a name="srgs-grammars"></a>Грамматики SRGS

Грамматика SRGS–это статический документ, который, в отличие от программного ограничения-списка, использует формат XML, определенный в спецификации [SRGS Version1.0](http://go.microsoft.com/fwlink/p/?LinkID=262302). Грамматика SRGS предоставляет больший контроль над распознаванием речи и позволяет создавать несколько семантических значений в одном распознавании.

 См. в разделе [**SpeechRecognitionGrammarFileConstraint**](https://msdn.microsoft.com/library/windows/apps/dn631412).

### <a name="voice-command-constraints"></a>Об ограничениях голосовых команд

С помощью XML-файлов определения голосовых команд можно задать команды, которые пользователь может произносить, чтобы выполнять определенные действия при активации вашего приложения. Дополнительные сведения см. в статье [Запуск приложения переднего плана с помощью голосовых команд в Кортане (XAML)](https://msdn.microsoft.com/cortana/voicecommands/launch-a-foreground-app-with-voice-commands-in-cortana).

См. в разделе [ **SpeechRecognitionVoiceCommandDefinitionConstraint**](https://msdn.microsoft.com/library/windows/apps/dn653220)/

**Примечание**тип используется тип ограничения зависит от сложности создаваемого взаимодействия с распознаванием, вы хотите создать. Каждый может оказаться наилучшим для конкретной задачи распознавания, и в приложении может найтись место всем типам ограничений.
Сведения об ограничениях см. в статье [Определение настраиваемых ограничений распознавания](define-custom-recognition-constraints.md).

Предопределенная грамматика универсального приложения для Windows для диктовки распознает большинство слов и коротких фраз в заданном языке. По умолчанию она активируется, когда создается экземпляр объекта распознавателя речи без настраиваемых ограничений.

В этом разделе мы покажем, как:

- Создать распознаватель речи.
- Скомпилировать ограничения универсального приложения для Windows по умолчанию (в набор грамматик распознавателя речи не добавлены грамматики).
- Начать прослушивание речи с помощью простого интерфейса распознавания и результатов преобразования текста в речь, передаваемых методом [**RecognizeWithUIAsync**](https://msdn.microsoft.com/library/windows/apps/dn653245). Если пользовательский интерфейс по умолчанию не требуется, используйте метод [**RecognizeAsync**](https://msdn.microsoft.com/library/windows/apps/dn653244).

```CSharp
private async void StartRecognizing_Click(object sender, RoutedEventArgs e)
{
    // Create an instance of SpeechRecognizer.
    var speechRecognizer = new Windows.Media.SpeechRecognition.SpeechRecognizer();

    // Compile the dictation grammar by default.
    await speechRecognizer.CompileConstraintsAsync();

    // Start recognition.
    Windows.Media.SpeechRecognition.SpeechRecognitionResult speechRecognitionResult = await speechRecognizer.RecognizeWithUIAsync();

    // Do something with the recognition result.
    var messageDialog = new Windows.UI.Popups.MessageDialog(speechRecognitionResult.Text, "Text spoken");
    await messageDialog.ShowAsync();
}
```

## <a name="customize-the-recognition-ui"></a>Настройка пользовательского интерфейса распознавания


Когда ваше приложение пытается распознать речь при помощи вызова [**SpeechRecognizer.RecognizeWithUIAsync**](https://msdn.microsoft.com/library/windows/apps/dn653245), отображаются несколько экранов в следующем порядке.

Если вы используете ограничение на базе предварительно заданной грамматики (диктовки или веб-поиска):

-   Экран **Слушаю**.
-   Экран **Думаю**.
-   Экран **Я услышал** или экран ошибки.

Если вы используете ограничение на базе списка слов или фраз или ограничение на базе грамматического файла SRGS:

-   Экран **Слушаю**.
-   Экран **Вы сказали**, если сказанное пользователем можно интерпретировать по-разному.
-   Экран **Я услышал** или экран ошибки.

На следующем изображении представлен пример потока между экранами распознавателя речи, использующего ограничение на базе грамматического файла SRGS. В этом примере распознавание речи прошло успешно.

![начальный экран распознавания для ограничения на базе грамматического файла SGRS](images/speech-listening-initial.png)

![промежуточный экран распознавания для ограничения на базе грамматического файла SGRS](images/speech-listening-intermediate.png)

![конечный экран распознавания для ограничения на базе грамматического файла SGRS](images/speech-listening-complete.png)

Экран **Слушаю** может предоставлять примеры слов или фраз, которые приложение может распознать. Здесь мы покажем, как использовать свойства класса [**SpeechRecognizerUIOptions**](https://msdn.microsoft.com/library/windows/apps/dn653234) (его можно получить, вызвав свойство [**SpeechRecognizer.UIOptions**](https://msdn.microsoft.com/library/windows/apps/dn653254)) для настройки содержимого на экране **Слушаю**.

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

## <a name="related-articles"></a>Статьи по теме


**Разработчикам**
* [Взаимодействие с помощью голосовых функций](speech-interactions.md)
**Проектировщикам**
* [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121)
**Примеры**
* [Пример распознавания и синтеза речи](http://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 




