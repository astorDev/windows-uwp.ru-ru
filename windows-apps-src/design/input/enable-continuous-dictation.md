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
ms.openlocfilehash: 0865b229faad646901ab76f46982e738b2830035
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57606259"
---
# <a name="continuous-dictation"></a>Непрерывная диктовка

Узнайте, как записать и распознать длительный непрерывный речевой ввод.

> **Важные API**: [**SpeechContinuousRecognitionSession**](https://msdn.microsoft.com/library/windows/apps/dn913896), [ **ContinuousRecognitionSession**](https://msdn.microsoft.com/library/windows/apps/dn913913)

В разделе [Распознавание речи](speech-recognition.md) вы узнали, как записать и распознать относительно короткий речевой ввод с помощью методов [**RecognizeAsync**](https://msdn.microsoft.com/library/windows/apps/dn653244) или [**RecognizeWithUIAsync**](https://msdn.microsoft.com/library/windows/apps/dn653245) объекта [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226), например при составлении сообщения SMS или вопроса.

Чтобы записывать более длительные сеансы распознавания речи, например диктовку или сообщение электронной почты, используйте свойство [**ContinuousRecognitionSession**](https://msdn.microsoft.com/library/windows/apps/dn913913)[**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226) для получения объекта [**SpeechContinuousRecognitionSession**](https://msdn.microsoft.com/library/windows/apps/dn913896).

> [!NOTE]
> Зависит от языковой поддержки режима диктовки [устройства](https://docs.microsoft.com/windows/uwp/design/devices/) где выполняется приложение. Для компьютеров и ноутбуков распознается только en US, хотя Xbox и телефоны может распознать все языки, поддерживаемые распознавания речи. Дополнительные сведения см. в разделе [указать язык распознаватель речи](specify-the-speech-recognizer-language.md).

## <a name="set-up"></a>Настройка

Для управления непрерывным сеансом диктовки вашему приложению понадобятся несколько объектов:

- экземпляр объекта [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226);
- ссылка на диспетчера пользовательского интерфейса для обновления пользовательского интерфейса во время диктовки;
- способ отслеживания всей совокупности слов, сказанных пользователем.

Здесь мы объявляем экземпляр [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226) как закрытое поле класса кода программной части. Ваше приложение должно сохранить ссылку в любом другом месте, если непрерывная диктовка должна продолжиться дальше отдельной XAML-страницы.

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

Здесь мы используем объект [**StringBuilder**](https://msdn.microsoft.com/library/system.text.stringbuilder.aspx), чтобы сохранить все предыдущие результаты распознавания, полученные в течение сеанса. Новые результаты добавляются в **StringBuilder** по мере обработки.

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

В данном примере мы инициализируем распознавание речи в событии страницы [**OnNavigatedTo**](https://msdn.microsoft.com/library/windows/apps/br227508).

1. Поскольку события, создаваемые распознавателем речи, возникают в фоновом потоке, создайте ссылку на диспетчер для обновления потока пользовательского интерфейса. [**OnNavigatedTo** ](https://msdn.microsoft.com/library/windows/apps/br227508) всегда вызывается в потоке пользовательского интерфейса.
```csharp
this.dispatcher = CoreWindow.GetForCurrentThread().Dispatcher;
```

2.  Затем мы инициализируем экземпляр [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226).
```csharp
this.speechRecognizer = new SpeechRecognizer();
```

3.  Затем мы добавляем и компилируем грамматику, которая определяет все слова и фразы, распознаваемые [**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226).

    Если вы не укажете грамматику явным образом, по умолчанию используется предопределенная грамматика для диктовки. Как правило, грамматика по умолчанию лучше всего подходит для общей диктовки.

    В данном случае мы сразу вызываем [**CompileConstraintsAsync**](https://msdn.microsoft.com/library/windows/apps/dn653240) без добавления грамматики.

    
```csharp
SpeechRecognitionCompilationResult result =
      await speechRecognizer.CompileConstraintsAsync();
```

## <a name="handle-recognition-events"></a>Обработка событий распознавания

Можно записать отдельную краткую реплику или фразу путем вызова [**RecognizeAsync**](https://msdn.microsoft.com/library/windows/apps/dn653244) или [**RecognizeWithUIAsync**](https://msdn.microsoft.com/library/windows/apps/dn653245). 

А чтобы записать более длительный сеанс непрерывного распознавания, мы определяем прослушиватели событий для работы в фоновом режиме во время речи пользователя, а также определяем обработчики для формирования строки диктовки.

Затем мы используем свойство [**ContinuousRecognitionSession**](https://msdn.microsoft.com/library/windows/apps/dn913913) нашего распознавателя, чтобы получить объект [**SpeechContinuousRecognitionSession**](https://msdn.microsoft.com/library/windows/apps/dn913896), который предоставляет методы и события для управления сеансом непрерывного распознавания.

Два события являются особенно важными.

- [**ResultGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913900), которое присваивается, когда распознаватель создал некоторые результаты.
- [**Завершено**](https://msdn.microsoft.com/library/windows/apps/dn913899), что происходит при завершении сеанса непрерывной распознавания.

Событие [**ResultGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913900) возникает, когда пользователь говорит. Распознаватель непрерывно слушает пользователя и периодически вызывает событие, которое передает реплики речевого ввода. Необходимо изучить речевой ввод с помощью свойства [**Result**](https://msdn.microsoft.com/library/windows/apps/dn913895) аргумента события и принять необходимые меры в обработчике событий, например добавить текст в объект StringBuilder.

В качестве экземпляра [**SpeechRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/dn631432) свойство [**Result**](https://msdn.microsoft.com/library/windows/apps/dn913895) полезно для определения необходимости принятия речевого ввода. [  **SpeechRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/dn631432) предоставляет два таких свойства:

- [**Состояние** ](https://msdn.microsoft.com/library/windows/apps/dn631440) указывает, успешно ли распознавания. Распознавание может завершиться с ошибкой по различным причинам.
- [**Достоверности** ](https://msdn.microsoft.com/library/windows/apps/dn631434) указывает относительный уверенность, что распознаватель понятны правильный слова.

Далее описаны основные действия, обеспечивающие поддержку непрерывного распознавания.  

1. Здесь мы регистрируем обработчик для события непрерывного распознавания [**ResultGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913900) в событии страницы [**OnNavigatedTo**](https://msdn.microsoft.com/library/windows/apps/br227508).
```csharp
speechRecognizer.ContinuousRecognitionSession.ResultGenerated +=
        ContinuousRecognitionSession_ResultGenerated;
```

2.  Затем мы проверяем свойство [**Confidence**](https://msdn.microsoft.com/library/windows/apps/dn631434). Если значение уверенности равно [**Medium**](https://msdn.microsoft.com/library/windows/apps/dn631409) или выше, мы добавляем текст в StringBuilder. Также мы обновляем пользовательский интерфейс по мере сбора ввода.

    **Примечание**   [ **ResultGenerated** ](https://msdn.microsoft.com/library/windows/apps/dn913900) событие фоновый поток, который не может напрямую обновлять пользовательский Интерфейс. Если обработчик должен обновить пользовательский Интерфейс (как \[пример распознавания речи и преобразования текста в РЕЧЬ\] does), необходимо отправлять обновления в поток пользовательского интерфейса через [ **RunAsync** ](https://msdn.microsoft.com/library/windows/apps/hh750317) метод диспетчера.
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

3.  Затем мы обрабатываем событие [**Completed**](https://msdn.microsoft.com/library/windows/apps/dn913899), которое указывает на конец непрерывной диктовки.

    Сеанс заканчивается, когда вы вызываете методы [**StopAsync**](https://msdn.microsoft.com/library/windows/apps/dn913908) или [**CancelAsync**](https://msdn.microsoft.com/library/windows/apps/dn913898) (описано в следующем разделе). Сеанс также завершается, когда возникает ошибка или когда пользователь перестает говорить. Проверьте свойство [**Status**](https://msdn.microsoft.com/library/windows/apps/dn631440) аргумента события, чтобы определить причину завершения сеанса ([**SpeechRecognitionResultStatus**](https://msdn.microsoft.com/library/windows/apps/dn631433)).

    Здесь мы регистрируем обработчик для события непрерывного распознавания [**Completed**](https://msdn.microsoft.com/library/windows/apps/dn913899) в событии страницы [**OnNavigatedTo**](https://msdn.microsoft.com/library/windows/apps/br227508).
```csharp
speechRecognizer.ContinuousRecognitionSession.Completed +=
      ContinuousRecognitionSession_Completed;
```

4.  Обработчик событий проверяет свойства Status, чтобы определить, было ли распознавание успешным. Он также обрабатывает те случаи, когда пользователь перестает говорить. Часто [**TimeoutExceeded**](https://msdn.microsoft.com/library/windows/apps/dn631433) считается успешным распознаванием, так как это означает, что пользователь перестал говорить. Вы должны обрабатывать эти случаи в своем коде, обеспечивая удобную работу пользователя.

    **Примечание**   [ **ResultGenerated** ](https://msdn.microsoft.com/library/windows/apps/dn913900) событие фоновый поток, который не может напрямую обновлять пользовательский Интерфейс. Если обработчик должен обновить пользовательский Интерфейс (как \[пример распознавания речи и преобразования текста в РЕЧЬ\] does), необходимо отправлять обновления в поток пользовательского интерфейса через [ **RunAsync** ](https://msdn.microsoft.com/library/windows/apps/hh750317) метод диспетчера.
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


Когда люди беседуют, они, как правило, полагаются на контекст, чтобы полностью понять произносимое. Аналогичным образом распознаватель речи часто нуждается в контексте, чтобы обеспечить высокодостоверные результаты распознавания. Например, сами по себе слова «кот» и «код» неразличимы, пока за счет других слов в предложении или фразе не будет понятен контекст. Пока распознаватель не получит определенной степени уверенности, что слово или слова были распознаны надлежащим образом, событие [**ResultGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913900) не будет создано.

Это может привести к некоторым неудобствам для пользователя, так как он будет продолжать говорить, но не увидит результата, пока степень уверенности в распознавателе не будет достаточной для вызова события [**ResultGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913900).

Для исправления этого кажущегося отсутствия какой-либо реакции следует обработать событие [**HypothesisGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913914). Это событие возникает, когда распознаватель создает новый набор потенциальных соответствий для обрабатываемого слова. Аргумент события предоставляет свойство [**Hypothesis**](https://msdn.microsoft.com/library/windows/apps/dn913911), содержащее текущие соответствия. Покажите это пользователю по мере того, как он продолжает говорить, чтобы уверить его в том, что обработка все еще продолжается. После того как уверенность будет достаточно высокой и результат распознавания будет определен, замените промежуточные результаты **Hypothesis** окончательным результатом [**Result**](https://msdn.microsoft.com/library/windows/apps/dn913895), предоставленным в событии [**ResultGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913900).

Здесь мы добавляем гипотетический текст и многоточие (...) в текущее значение поля вывода [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683). Содержимое текстового поля обновляется по мере формирования новых гипотез и до получения окончательных результатов из события [**ResultGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913900).

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


Перед запуском сеанса распознавания проверьте значение свойства [**State**](https://msdn.microsoft.com/library/windows/apps/dn913915) распознавателя речи. Распознаватель речи должен находиться в состоянии [**Idle**](https://msdn.microsoft.com/library/windows/apps/dn653227).

После проверки состояния распознавателя речи мы начинаем сеанс, вызвав метод [**StartAsync**](https://msdn.microsoft.com/library/windows/apps/dn913901) свойства [**ContinuousRecognitionSession**](https://msdn.microsoft.com/library/windows/apps/dn913913) распознавателя речи.

```CSharp
if (speechRecognizer.State == SpeechRecognizerState.Idle)
{
  await speechRecognizer.ContinuousRecognitionSession.StartAsync();
}
```

Распознавание может быть остановлено двумя способами:

-   [**StopAsync** ](https://msdn.microsoft.com/library/windows/apps/dn913908) позволяет все ожидающие события распознавания полный ([**ResultGenerated** ](https://msdn.microsoft.com/library/windows/apps/dn913900) продолжает возникать, пока все ожидающие распознавания операции завершены).
-   [**CancelAsync** ](https://msdn.microsoft.com/library/windows/apps/dn913898) завершает сеанс распознавания немедленно и удаляет все ожидающие результаты.

После проверки состояния распознавателя речи мы останавливаем сеанс, вызвав метод [**CancelAsync**](https://msdn.microsoft.com/library/windows/apps/dn913898) свойства [**ContinuousRecognitionSession**](https://msdn.microsoft.com/library/windows/apps/dn913913) распознавателя речи.

```CSharp
if (speechRecognizer.State != SpeechRecognizerState.Idle)
{
  await speechRecognizer.ContinuousRecognitionSession.CancelAsync();
}
```

> [!NOTE]
> Событие [**ResultGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913900) может возникнуть после вызова [**CancelAsync**](https://msdn.microsoft.com/library/windows/apps/dn913898).  
> Из-за многопоточности событие [**ResultGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913900) может по-прежнему оставаться в стеке при вызове [**CancelAsync**](https://msdn.microsoft.com/library/windows/apps/dn913898). В этом случае событие **ResultGenerated** все равно возникает.  
> Если заданы какие-либо закрытые поля при отмене сеанса распознавания, всегда проверяйте их значения в обработчике [**ResultGenerated**](https://msdn.microsoft.com/library/windows/apps/dn913900). Например, не думайте, что поле будет инициализироваться в обработчике, если ему задано значение NULL при отмене сеанса.

 

## <a name="related-articles"></a>Связанные статьи


* [Взаимодействия с помощью речи](speech-interactions.md)

**Примеры**
* [Распознавание речи и синтеза речи-пример](https://go.microsoft.com/fwlink/p/?LinkID=619897)
 

 




