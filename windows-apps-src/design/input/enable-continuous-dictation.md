---
Description: Узнайте, как записать и распознать длительный непрерывный речевой ввод.
title: Включение непрерывной диктовки
ms.assetid: 383B3E23-1678-4FBB-B36E-6DE2DA9CA9DC
label: Continuous dictation
template: detail.hbs
keywords: речь, голос, распознавание речи, естественный язык, диктовка, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 3709a9076ce1d258ce2eca7f97aa1478088a9044
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66363569"
---
# <a name="continuous-dictation"></a>Непрерывная диктовка

Узнайте, как записать и распознать длительный непрерывный речевой ввод.

> **Важные API**: [**SpeechContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechContinuousRecognitionSession), [ **ContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.continuousrecognitionsession)

В разделе [Распознавание речи](speech-recognition.md) вы узнали, как записать и распознать относительно короткий речевой ввод с помощью методов [**RecognizeAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognizeasync) или [**RecognizeWithUIAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognizewithuiasync) объекта [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer), например при составлении сообщения SMS или вопроса.

Чтобы записывать более длительные сеансы распознавания речи, например диктовку или сообщение электронной почты, используйте свойство [**ContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.continuousrecognitionsession)[**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer) для получения объекта [**SpeechContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechContinuousRecognitionSession).

> [!NOTE]
> Зависит от языковой поддержки режима диктовки [устройства](https://docs.microsoft.com/windows/uwp/design/devices/) где выполняется приложение. Для компьютеров и ноутбуков распознается только en US, хотя Xbox и телефоны может распознать все языки, поддерживаемые распознавания речи. Дополнительные сведения см. в разделе [указать язык распознаватель речи](specify-the-speech-recognizer-language.md).

## <a name="set-up"></a>Настройка

Для управления непрерывным сеансом диктовки вашему приложению понадобятся несколько объектов:

- экземпляр объекта [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer);
- ссылка на диспетчера пользовательского интерфейса для обновления пользовательского интерфейса во время диктовки;
- способ отслеживания всей совокупности слов, сказанных пользователем.

Здесь мы объявляем экземпляр [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer) как закрытое поле класса кода программной части. Ваше приложение должно сохранить ссылку в любом другом месте, если непрерывная диктовка должна продолжиться дальше отдельной XAML-страницы.

```CSharp
private SpeechRecognizer speechRecognizer;
```

Во время диктовки распознаватель создает события из фонового потока. Так как фоновый поток не может напрямую обновлять пользовательский интерфейс в XAML, ваше приложение должно использовать диспетчер для обновления пользовательского интерфейса в ответ на события распознавания.

Здесь мы объявляем закрытое поле, которое будет инициализировано позже с помощью диспетчера пользовательского интерфейса.

```CSharp
// Speech events may originate from a thread other than the UI thread.
// Keep track of the UI thread dispatcher so that we can update the
// UI in a thread-safe manner.
private CoreDispatcher dispatcher;
```

Для отслеживания речи пользователя необходимо обрабатывать события распознавания, которые создаются распознавателем речи. Эти события предоставляют результаты распознавания отрывков реплик пользователя.

Здесь мы используем объект [**StringBuilder**](https://docs.microsoft.com/dotnet/api/system.text.stringbuilder?redirectedfrom=MSDN), чтобы сохранить все предыдущие результаты распознавания, полученные в течение сеанса. Новые результаты добавляются в **StringBuilder** по мере обработки.

```CSharp
private StringBuilder dictatedTextBuilder;
```

## <a name="initialization"></a>Инициализация

Во время инициализации непрерывного распознавания речи необходимо выполнить следующие действия.

- Получить диспетчер для потока пользовательского интерфейса, если пользовательский интерфейс приложения обновляется в обработчиках событий непрерывных речи.
- Инициализировать распознаватель речи.
- Скомпилировать встроенную грамматику диктовки.
    **Примечание**    распознавания речи требуется по крайней мере одно ограничение для определения запоминающееся словаря. Если не задано ни одно ограничение, будет использоваться предопределенная грамматика речевого ввода. См. [Распознавание речи](speech-recognition.md).
- Настроить прослушиватели событий распознавания.

В данном примере мы инициализируем распознавание речи в событии страницы [**OnNavigatedTo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto).

1. Поскольку события, создаваемые распознавателем речи, возникают в фоновом потоке, создайте ссылку на диспетчер для обновления потока пользовательского интерфейса. [**OnNavigatedTo** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto) всегда вызывается в потоке пользовательского интерфейса.
```csharp
this.dispatcher = CoreWindow.GetForCurrentThread().Dispatcher;
```

2.  Затем мы инициализируем экземпляр [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer).
```csharp
this.speechRecognizer = new SpeechRecognizer();
```

3.  Затем мы добавляем и компилируем грамматику, которая определяет все слова и фразы, распознаваемые [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer).

    Если вы не укажете грамматику явным образом, по умолчанию используется предопределенная грамматика для диктовки. Как правило, грамматика по умолчанию лучше всего подходит для общей диктовки.

    В данном случае мы сразу вызываем [**CompileConstraintsAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.compileconstraintsasync) без добавления грамматики.

    
```csharp
SpeechRecognitionCompilationResult result =
      await speechRecognizer.CompileConstraintsAsync();
```

## <a name="handle-recognition-events"></a>Обработка событий распознавания

Можно записать отдельную краткую реплику или фразу путем вызова [**RecognizeAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognizeasync) или [**RecognizeWithUIAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognizewithuiasync). 

А чтобы записать более длительный сеанс непрерывного распознавания, мы определяем прослушиватели событий для работы в фоновом режиме во время речи пользователя, а также определяем обработчики для формирования строки диктовки.

Затем мы используем свойство [**ContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.continuousrecognitionsession) нашего распознавателя, чтобы получить объект [**SpeechContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechContinuousRecognitionSession), который предоставляет методы и события для управления сеансом непрерывного распознавания.

Два события являются особенно важными.

- [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated), которое присваивается, когда распознаватель создал некоторые результаты.
- [**Завершено**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.completed), что происходит при завершении сеанса непрерывной распознавания.

Событие [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) возникает, когда пользователь говорит. Распознаватель непрерывно слушает пользователя и периодически вызывает событие, которое передает реплики речевого ввода. Необходимо изучить речевой ввод с помощью свойства [**Result**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionresultgeneratedeventargs.result) аргумента события и принять необходимые меры в обработчике событий, например добавить текст в объект StringBuilder.

В качестве экземпляра [**SpeechRecognitionResult**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionResult) свойство [**Result**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionresultgeneratedeventargs.result) полезно для определения необходимости принятия речевого ввода. [  **SpeechRecognitionResult**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionResult) предоставляет два таких свойства:

- [**Состояние** ](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionresult.status) указывает, успешно ли распознавания. Распознавание может завершиться с ошибкой по различным причинам.
- [**Достоверности** ](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionresult.confidence) указывает относительный уверенность, что распознаватель понятны правильный слова.

Далее описаны основные действия, обеспечивающие поддержку непрерывного распознавания.  

1. Здесь мы регистрируем обработчик для события непрерывного распознавания [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) в событии страницы [**OnNavigatedTo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto).
```csharp
speechRecognizer.ContinuousRecognitionSession.ResultGenerated +=
        ContinuousRecognitionSession_ResultGenerated;
```

2.  Затем мы проверяем свойство [**Confidence**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionresult.confidence). Если значение уверенности равно [**Medium**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionConfidence) или выше, мы добавляем текст в StringBuilder. Также мы обновляем пользовательский интерфейс по мере сбора ввода.

    **Примечание**   [ **ResultGenerated** ](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) событие фоновый поток, который не может напрямую обновлять пользовательский Интерфейс. Если обработчик должен обновить пользовательский Интерфейс (как \[пример распознавания речи и преобразования текста в РЕЧЬ\] does), необходимо отправлять обновления в поток пользовательского интерфейса через [ **RunAsync** ](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.windows) метод диспетчера.
```csharp
private async void ContinuousRecognitionSession_ResultGenerated(
      SpeechContinuousRecognitionSession sender,
      SpeechContinuousRecognitionResultGeneratedEventArgs args)
      {

        if (args.Result.Confidence == SpeechRecognitionConfidence.Medium ||
          args.Result.Confidence == SpeechRecognitionConfidence.High)
          {
            dictatedTextBuilder.Append(args.Result.Text + " ");

            await dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
              dictationTextBox.Text = dictatedTextBuilder.ToString();
              btnClearText.IsEnabled = true;
            });
          }
        else
        {
          await dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
              dictationTextBox.Text = dictatedTextBuilder.ToString();
            });
        }
      }
```

3.  Затем мы обрабатываем событие [**Completed**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.completed), которое указывает на конец непрерывной диктовки.

    Сеанс заканчивается, когда вы вызываете методы [**StopAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.stopasync) или [**CancelAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.cancelasync) (описано в следующем разделе). Сеанс также завершается, когда возникает ошибка или когда пользователь перестает говорить. Проверьте свойство [**Status**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionresult.status) аргумента события, чтобы определить причину завершения сеанса ([**SpeechRecognitionResultStatus**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionResultStatus)).

    Здесь мы регистрируем обработчик для события непрерывного распознавания [**Completed**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.completed) в событии страницы [**OnNavigatedTo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto).
```csharp
speechRecognizer.ContinuousRecognitionSession.Completed +=
      ContinuousRecognitionSession_Completed;
```

4.  Обработчик событий проверяет свойства Status, чтобы определить, было ли распознавание успешным. Он также обрабатывает те случаи, когда пользователь перестает говорить. Часто [**TimeoutExceeded**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionResultStatus) считается успешным распознаванием, так как это означает, что пользователь перестал говорить. Вы должны обрабатывать эти случаи в своем коде, обеспечивая удобную работу пользователя.

    **Примечание**   [ **ResultGenerated** ](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) событие фоновый поток, который не может напрямую обновлять пользовательский Интерфейс. Если обработчик должен обновить пользовательский Интерфейс (как \[пример распознавания речи и преобразования текста в РЕЧЬ\] does), необходимо отправлять обновления в поток пользовательского интерфейса через [ **RunAsync** ](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.windows) метод диспетчера.
```csharp
private async void ContinuousRecognitionSession_Completed(
      SpeechContinuousRecognitionSession sender,
      SpeechContinuousRecognitionCompletedEventArgs args)
      {
        if (args.Status != SpeechRecognitionResultStatus.Success)
        {
          if (args.Status == SpeechRecognitionResultStatus.TimeoutExceeded)
          {
            await dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
              rootPage.NotifyUser(
                "Automatic Time Out of Dictation",
                NotifyType.StatusMessage);

              DictationButtonText.Text = " Continuous Recognition";
              dictationTextBox.Text = dictatedTextBuilder.ToString();
            });
          }
          else
          {
            await dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
              rootPage.NotifyUser(
                "Continuous Recognition Completed: " + args.Status.ToString(),
                NotifyType.StatusMessage);

              DictationButtonText.Text = " Continuous Recognition";
            });
          }
        }
      }
```

## <a name="provide-ongoing-recognition-feedback"></a>Отображение реакции в процессе распознавания


Когда люди беседуют, они, как правило, полагаются на контекст, чтобы полностью понять произносимое. Аналогичным образом распознаватель речи часто нуждается в контексте, чтобы обеспечить высокодостоверные результаты распознавания. Например, сами по себе слова «кот» и «код» неразличимы, пока за счет других слов в предложении или фразе не будет понятен контекст. Пока распознаватель не получит определенной степени уверенности, что слово или слова были распознаны надлежащим образом, событие [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) не будет создано.

Это может привести к некоторым неудобствам для пользователя, так как он будет продолжать говорить, но не увидит результата, пока степень уверенности в распознавателе не будет достаточной для вызова события [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated).

Для исправления этого кажущегося отсутствия какой-либо реакции следует обработать событие [**HypothesisGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.hypothesisgenerated). Это событие возникает, когда распознаватель создает новый набор потенциальных соответствий для обрабатываемого слова. Аргумент события предоставляет свойство [**Hypothesis**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionhypothesisgeneratedeventargs.hypothesis), содержащее текущие соответствия. Покажите это пользователю по мере того, как он продолжает говорить, чтобы уверить его в том, что обработка все еще продолжается. После того как уверенность будет достаточно высокой и результат распознавания будет определен, замените промежуточные результаты **Hypothesis** окончательным результатом [**Result**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionresultgeneratedeventargs.result), предоставленным в событии [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated).

Здесь мы добавляем гипотетический текст и многоточие (...) в текущее значение поля вывода [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox). Содержимое текстового поля обновляется по мере формирования новых гипотез и до получения окончательных результатов из события [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated).

```CSharp
private async void SpeechRecognizer_HypothesisGenerated(
  SpeechRecognizer sender,
  SpeechRecognitionHypothesisGeneratedEventArgs args)
  {

    string hypothesis = args.Hypothesis.Text;
    string textboxContent = dictatedTextBuilder.ToString() + " " + hypothesis + " ...";

    await dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
      dictationTextBox.Text = textboxContent;
      btnClearText.IsEnabled = true;
    });
  }
```

## <a name="start-and-stop-recognition"></a>Запуск и остановка распознавания


Перед запуском сеанса распознавания проверьте значение свойства [**State**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.state) распознавателя речи. Распознаватель речи должен находиться в состоянии [**Idle**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizerState).

После проверки состояния распознавателя речи мы начинаем сеанс, вызвав метод [**StartAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.startasync) свойства [**ContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.continuousrecognitionsession) распознавателя речи.

```CSharp
if (speechRecognizer.State == SpeechRecognizerState.Idle)
{
  await speechRecognizer.ContinuousRecognitionSession.StartAsync();
}
```

Распознавание может быть остановлено двумя способами:

-   [**StopAsync** ](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.stopasync) позволяет все ожидающие события распознавания полный ([**ResultGenerated** ](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) продолжает возникать, пока все ожидающие распознавания операции завершены).
-   [**CancelAsync** ](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.cancelasync) завершает сеанс распознавания немедленно и удаляет все ожидающие результаты.

После проверки состояния распознавателя речи мы останавливаем сеанс, вызвав метод [**CancelAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.cancelasync) свойства [**ContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.continuousrecognitionsession) распознавателя речи.

```CSharp
if (speechRecognizer.State != SpeechRecognizerState.Idle)
{
  await speechRecognizer.ContinuousRecognitionSession.CancelAsync();
}
```

> [!NOTE]
> Событие [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) может возникнуть после вызова [**CancelAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.cancelasync).  
> Из-за многопоточности событие [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) может по-прежнему оставаться в стеке при вызове [**CancelAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.cancelasync). В этом случае событие **ResultGenerated** все равно возникает.  
> Если заданы какие-либо закрытые поля при отмене сеанса распознавания, всегда проверяйте их значения в обработчике [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated). Например, не думайте, что поле будет инициализироваться в обработчике, если ему задано значение NULL при отмене сеанса.

 

## <a name="related-articles"></a>Связанные статьи


* [Взаимодействия с помощью речи](speech-interactions.md)

**Примеры**
* [Распознавание речи и синтеза речи-пример](https://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 




