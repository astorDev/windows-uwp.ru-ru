---
author: Karl-Bridge-Microsoft
Description: "Узнайте, как пользователь может взаимодействовать с фоновым приложением через голосовые функции и холст Кортаны во время выполнения голосовой команды."
title: "Взаимодействие с фоновым приложением"
ms.assetid: 6C60F03C-A242-435D-96BB-736892CC1CA6
label: Interact with a background app
template: detail.hbs
ms.sourcegitcommit: 7d9f5eff0f6561b18024658fe99d1e11bbe3309f
ms.openlocfilehash: 675553f5c3954597982360900e965b2a756d7f63

---

# Взаимодействие с фоновым приложением в Кортане

Включите взаимодействие пользователя с фоновым приложением с помощью голосового и текстового ввода на холсте **Кортаны**, продолжая выполнять голосовую команду.



**Важные API**

-   [**Windows.ApplicationModel.VoiceCommands**](https://msdn.microsoft.com/library/windows/apps/dn706594)
-   [**Элементы и атрибуты определения голосовых команд (VCD), версия 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593)


Кортана поддерживает полноценный поэтапный процесс взаимодействия с вашим приложением. Этот процесс определяется приложением и может поддерживать следующие функциональные возможности: 

-   Успешное завершение
-   Передача
-   Ход выполнения
-   Подтверждение
-   Уточнение
-   Ошибка

**Необходимые условия:**

В этом разделе используются сведения из раздела [Запуск фонового приложения с помощью голосовых команд в Кортане](launch-a-background-app-with-voice-commands-in-cortana.md). Мы продолжим демонстрацию этих функций на примере приложения для планирования поездок и управления ими под названием **Adventure Works**.

Если вы — начинающий разработчик приложений универсальной платформы Windows (UWP), прочитайте указанные ниже статьи, чтобы ознакомиться с описанными здесь технологиями.

-   [Создание первого приложения](https://msdn.microsoft.com/library/windows/apps/bg124288)
-   Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

**Рекомендации по взаимодействию с пользователем.  **

Сведения об интеграции приложения с **Кортаной** см. в [рекомендациях по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233), а полезные советы по проектированию удобного и привлекательного приложения с поддержкой распознавания речи — в [рекомендациях по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121).

## <span id="Feedback_strings"></span><span id="feedback_strings"></span><span id="FEEDBACK_STRINGS"></span>Строки отзыва

Составьте строки отзыва, которые отображаются и произносятся **Кортаной**.

Раздел [Рекомендации по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233) содержит рекомендации по составлению строк для **Кортаны**.

## <span id="Feedback_strings"></span><span id="feedback_strings"></span><span id="FEEDBACK_STRINGS"></span>Строки отзыва

Карточки содержимого могут предоставить пользователю дополнительный контекст и обеспечивают краткость строк обратной связи.

**Кортана** поддерживает следующие шаблоны карточек содержимого (на экране завершения может использоваться только один шаблон).

    -   Title only
    -   Title with up to three lines of text
    -   Title with image
    -   Title with image and up to three lines of text

В качестве изображения можно использовать:

    -   68w x 68h
    -   68w x 92h
    -   280w x 140h

Вы также можете позволить пользователям запускать ваше приложение на переднем плане, щелкая карточку или текстовую ссылку на приложение.

## <span id="Completion_screen"></span><span id="completion_screen"></span><span id="COMPLETION_SCREEN"></span>Экран завершения

Экран завершения предоставляет пользователю информацию о завершенной задаче голосовой команды.

Задачи, на которые приложение отвечает менее, чем за 500 мс, и которые не требуют от пользователя дополнительной информации, выполняются без дальнейшего взаимодействия с **Кортаной**. В Кортане просто отображается экран завершения.

Здесь используется приложение **Adventure Works**, чтобы показать экран завершения вызванной голосовой командой операции по отображению предстоящих поездок в Лондон. 

![экран завершения фонового приложения в Кортане](images/cortana-completion-screen-upcomingtrip-small.png)

Голосовая команда определяется в файле AdventureWorksCommands.xml:
```
<Command Name="whenIsTripToDestination">
  <Example> When is my trip to Las Vegas?</Example>
  <ListenFor RequireAppName="BeforeOrAfterPhrase"> when is [my] trip to {destination}</ListenFor>
  <ListenFor RequireAppName="ExplicitlySpecified"> when is [my] {builtin:AppName} trip to {destination} </ListenFor>
  <Feedback> Looking for trip to {destination}</Feedback>
  <VoiceCommandService Target="AdventureWorksVoiceCommandService"/>
</Command>
```

AdventureWorksVoiceCommandService.cs содержит метод вывода сообщения о завершении операции:

```csharp
/// <summary>
/// Show details for a single trip, if the trip can be found. 
/// This demonstrates a simple response flow in Cortana.
/// </summary>
/// <param name="destination">The destination specified in the voice command.</param>
private async Task SendCompletionMessageForDestination(string destination)
{
    // If this operation is expected to take longer than 0.5 seconds, the task must
    // supply a progress response to Cortana before starting the operation, and
    // updates must be provided at least every 5 seconds.
    string loadingTripToDestination = string.Format(
               cortanaResourceMap.GetValue("LoadingTripToDestination", cortanaContext).ValueAsString,
               destination);
    await ShowProgressScreen(loadingTripToDestination);
    Model.TripStore store = new Model.TripStore();
    await store.LoadTrips();

    // Query for the specified trip. 
    // The destination should be in the phrase list. However, there might be  
    // multiple trips to the destination. We pick the first.
    IEnumerable<Model.Trip> trips = store.Trips.Where(p => p.Destination == destination);

    var userMessage = new VoiceCommandUserMessage();
    var destinationsContentTiles = new List<VoiceCommandContentTile>();
    if (trips.Count() == 0)
    {
        string foundNoTripToDestination = string.Format(
               cortanaResourceMap.GetValue("FoundNoTripToDestination", cortanaContext).ValueAsString,
               destination);
        userMessage.DisplayMessage = foundNoTripToDestination;
        userMessage.SpokenMessage = foundNoTripToDestination;
    }
    else
    {
        // Set plural or singular title.
        string message = "";
        if (trips.Count() > 1)
        {
            message = cortanaResourceMap.GetValue("PluralUpcomingTrips", cortanaContext).ValueAsString;
        }
        else
        {
            message = cortanaResourceMap.GetValue("SingularUpcomingTrip", cortanaContext).ValueAsString;
        }
        userMessage.DisplayMessage = message;
        userMessage.SpokenMessage = message;

        // Define a tile for each destination.
        foreach (Model.Trip trip in trips)
        {
            int i = 1;
            
            var destinationTile = new VoiceCommandContentTile();

            destinationTile.ContentTileType = VoiceCommandContentTileType.TitleWith68x68IconAndText;
            destinationTile.Image = await StorageFile.GetFileFromApplicationUriAsync(new Uri("ms-appx:///AdventureWorks.VoiceCommands/Images/GreyTile.png"));

            destinationTile.AppLaunchArgument = trip.Destination;
            destinationTile.Title = trip.Destination;
            if (trip.StartDate != null)
            {
                destinationTile.TextLine1 = trip.StartDate.Value.ToString(dateFormatInfo.LongDatePattern);
            }
            else
            {
                destinationTile.TextLine1 = trip.Destination + " " + i;
            }

            destinationsContentTiles.Add(destinationTile);
            i++;
        }
    }

    var response = VoiceCommandResponse.CreateResponse(userMessage, destinationsContentTiles);

    if (trips.Count() > 0)
    {
        response.AppLaunchArgument = destination;
    }

    await voiceServiceConnection.ReportSuccessAsync(response);
}
```

## <span id="Hand-off_screen"></span><span id="hand-off_screen"></span><span id="HAND-OFF_SCREEN"></span>Экран передачи

Как только голосовая команда распознана, **Кортана** обязана вызвать метод ReportSuccessAsync и предоставить обратную связь в течение около 500 мс. Если служба приложения не может выполнить действие, заданное голосовой командой, в течение 500 миллисекунд, **Кортана** на отображает экран передачи до тех пор, пока приложение не вызовет метод ReportSuccessAsync (до 5 секунд).

Если служба приложения не вызывает команду ReportSuccessAsync или какой-либо другой метод VoiceCommandServiceConnection, пользователь получает сообщение об ошибке, а вызов службы приложения отменяется.

Вот пример экрана передачи для приложения **Adventure Works**. В этом примере пользователь запросил у **Кортаны** предстоящие поездки. Экран передачи включает настроенное сообщение с именем службы приложения, значком и строкой **Обратная связь**. 

[!NOTE] Вы можете объявить строку **Отзыв** в VCD-файле. Эта строка не влияет на текст пользовательского интерфейса, отображаемый на холсте Кортаны, это влияет только на текст, произносимый **Кортаной**.

![экран передачи фонового приложения в Кортане](images/cortana-backgroundapp-progress-result.png)


## <span id="Progress_screen"></span><span id="progress_screen"></span><span id="PROGRESS_SCREEN"></span>Экран хода выполнения


Если службе приложения требуется более 500 мс для вызова метода ReportSuccessAsync, **Кортана** отображает экран хода выполнения. Отображается значок приложения, и вы должны предоставить строки хода выполнения как для графического интерфейса пользователя, так и для преобразования текста в речь, чтобы сообщить об активной обработке задачи.

**Кортана** отображает экран хода выполнения в течение не более 5 секунд. Через 5 секунд **Кортана** представляет пользователю сообщение об ошибке, а служба приложения закрывается. Если службе приложения нужно больше 5 секунд на выполнение действия, она может продолжить предоставлять **Кортане** экраны хода выполнения.

Вот пример экрана хода выполнения для приложения **Adventure Works**. В этом примере пользователь отменил поездку в Лас-Вегас. Экран хода выполнения включает настроенное для действия сообщение, значок и плитку содержимого с информацией о поездке, которая отменяется.

![экран хода выполнения фонового приложения в Кортане ](images/cortana-progress-screen.png)

AdventureWorksVoiceCommandService.cs содержит следующий метод вывода сообщения о ходе выполнения, который вызывает метод [**ReportProgressAsync**](https://msdn.microsoft.com/library/windows/apps/dn706579), чтобы отобразить экран хода выполнения в **Кортане**.


```    CSharp
/// <summary>
/// Show a progress screen. These should be posted at least every 5 seconds for a 
/// long-running operation.
/// </summary>
/// <param name="message">The message to display, relating to the task being performed.</param>
/// <returns></returns>
private async Task ShowProgressScreen(string message)
{
    var userProgressMessage = new VoiceCommandUserMessage();
    userProgressMessage.DisplayMessage = userProgressMessage.SpokenMessage = message;

    VoiceCommandResponse response = VoiceCommandResponse.CreateResponse(userProgressMessage);
    await voiceServiceConnection.ReportProgressAsync(response);
}
```

## <span id="Confirmation_screen"></span><span id="confirmation_screen"></span><span id="CONFIRMATION_SCREEN"></span>Экран подтверждения


Если действие, заданное голосовой командой, является необратимым, имеет существенное воздействие, или уверенность в распознавании речи недостаточно высока, служба приложения может запросить подтверждение.

Вот пример экрана подтверждения для приложения **Adventure Works**. В этом примере пользователь указал службе приложения отменить поездку в Лас-Вегас через **Кортану**. Служба приложения предоставила **Кортане** экран подтверждения, который запрашивает у пользователя ответ "Да" или "Нет" перед отменой поездки.

Если пользователь говорит что-то кроме "Да" или "Нет", **Кортана** не может определить ответ на вопрос. В этом случае **Кортана** выводит пользователю похожий вопрос, предоставленный службой приложения.

При втором запросе, если пользователь все еще не сказал "Да" или "Нет", **Кортана** выводит пользователю этот же вопрос третий раз, принося перед этим извинения. Если пользователь по-прежнему не говорит "Да" или "Нет", **Кортана** перестает прослушивать голосовой ввод и просит пользователя коснуться одной из кнопок.

Экран подтверждения включает настроенное для действия сообщение, значок и плитку содержимого с информацией о поездке, которая отменяется.

![Экран подтверждения фонового приложения в Кортане](images/cortana-confirmation-screen.png)

AdventureWorksVoiceCommandService.cs содержит следующий метод отмены поездки, который вызывает метод [**RequestConfirmationAsync**](https://msdn.microsoft.com/library/windows/apps/dn706582), чтобы показать экран подтверждения в **Кортане**.

```    CSharp
/// <summary>
/// Handle the Trip Cancellation task. This task demonstrates how to prompt a user
/// for confirmation of an operation, show users a progress screen while performing
/// a long-running task, and show a completion screen.
/// </summary>
/// <param name="destination">The name of a destination.</param>
/// <returns></returns>
private async Task SendCompletionMessageForCancellation(string destination)
{
    // Begin loading data to search for the target store. 
    // Consider inserting a progress screen here, in order to prevent Cortana from timing out. 
    string progressScreenString = string.Format(
        cortanaResourceMap.GetValue("ProgressLookingForTripToDest", cortanaContext).ValueAsString,
        destination);
    await ShowProgressScreen(progressScreenString);

    Model.TripStore store = new Model.TripStore();
    await store.LoadTrips();

    IEnumerable<Model.Trip> trips = store.Trips.Where(p => p.Destination == destination);
    Model.Trip trip = null;
    if (trips.Count() > 1)
    {
        // If there is more than one trip, provide a disambiguation screen.
        // However, if a significant number of items are returned, you might want to 
        // just display a link to your app and provide a deeper search experience.
        string disambiguationDestinationString = string.Format(
            cortanaResourceMap.GetValue("DisambiguationWhichTripToDest", cortanaContext).ValueAsString,
            destination);
        string disambiguationRepeatString = cortanaResourceMap.GetValue("DisambiguationRepeat", cortanaContext).ValueAsString;
        trip = await DisambiguateTrips(trips, disambiguationDestinationString, disambiguationRepeatString);
    }
    else
    {
        trip = trips.FirstOrDefault();
    }

    var userPrompt = new VoiceCommandUserMessage();
    
    VoiceCommandResponse response;
    if (trip == null)
    {
        var userMessage = new VoiceCommandUserMessage();
        string noSuchTripToDestination = string.Format(
            cortanaResourceMap.GetValue("NoSuchTripToDestination", cortanaContext).ValueAsString,
            destination);
        userMessage.DisplayMessage = userMessage.SpokenMessage = noSuchTripToDestination;

        response = VoiceCommandResponse.CreateResponse(userMessage);
        await voiceServiceConnection.ReportSuccessAsync(response);
    }
    else
    {
        // Prompt the user for confirmation that this is the correct trip to cancel.
        string cancelTripToDestination = string.Format(
            cortanaResourceMap.GetValue("CancelTripToDestination", cortanaContext).ValueAsString,
            destination);
        userPrompt.DisplayMessage = userPrompt.SpokenMessage = cancelTripToDestination;
        var userReprompt = new VoiceCommandUserMessage();
        string confirmCancelTripToDestination = string.Format(
            cortanaResourceMap.GetValue("ConfirmCancelTripToDestination", cortanaContext).ValueAsString,
            destination);
        userReprompt.DisplayMessage = userReprompt.SpokenMessage = confirmCancelTripToDestination;
        
        response = VoiceCommandResponse.CreateResponseForPrompt(userPrompt, userReprompt);

        var voiceCommandConfirmation = await voiceServiceConnection.RequestConfirmationAsync(response);

        // If RequestConfirmationAsync returns null, Cortana has likely been dismissed.
        if (voiceCommandConfirmation != null)
        {
            if (voiceCommandConfirmation.Confirmed == true)
            {
                string cancellingTripToDestination = string.Format(
               cortanaResourceMap.GetValue("CancellingTripToDestination", cortanaContext).ValueAsString,
               destination);
                await ShowProgressScreen(cancellingTripToDestination);

                // Perform the operation to remove the trip from app data. 
                // As the background task runs within the app package of the installed app,
                // we can access local files belonging to the app without issue.
                await store.DeleteTrip(trip);

                // Provide a completion message to the user.
                var userMessage = new VoiceCommandUserMessage();
                string cancelledTripToDestination = string.Format(
                    cortanaResourceMap.GetValue("CancelledTripToDestination", cortanaContext).ValueAsString,
                    destination);
                userMessage.DisplayMessage = userMessage.SpokenMessage = cancelledTripToDestination;
                response = VoiceCommandResponse.CreateResponse(userMessage);
                await voiceServiceConnection.ReportSuccessAsync(response);
            }
            else
            {
                // Confirm no action for the user.
                var userMessage = new VoiceCommandUserMessage();
                string keepingTripToDestination = string.Format(
                    cortanaResourceMap.GetValue("KeepingTripToDestination", cortanaContext).ValueAsString,
                    destination);
                userMessage.DisplayMessage = userMessage.SpokenMessage = keepingTripToDestination;

                response = VoiceCommandResponse.CreateResponse(userMessage);
                await voiceServiceConnection.ReportSuccessAsync(response);
            }
        }
    }
}
```

## <span id="Disambiguation_screen"></span><span id="disambiguation_screen"></span><span id="DISAMBIGUATION_SCREEN"></span>Экран уточнения


Если действие, заданное голосовой командой, имеет больше одного возможного результата, служба приложения может запросить у пользователя дополнительные сведения.

Вот пример экрана уточнения для приложения **Adventure Works**. В этом примере пользователь указал службе приложения отменить поездку в Лас-Вегас через **Кортану**. Однако у пользователя запланировано две поездки в Лас-Вегас на разные даты, и служба приложения сможет выполнить действие, только если пользователь выберет нужную поездку.

Прежде чем отменить поездку, служба приложения предоставляет **Кортане** экран уточнения, который предлагает пользователю выбрать поездку из списка соответствующих поездок.

В этом случае **Кортана** выводит пользователю похожий вопрос, предоставленный службой приложения.

При втором запросе, если пользователь все еще не сказал ничего, что можно использовать для определения выбора, **Кортана** выводит пользователю этот же вопрос третий раз, принося перед этим извинения. Если пользователь по-прежнему не говорит ничего, что можно использовать для определения выбора, **Кортана** перестает прослушивать голосовой ввод и просит пользователя коснуться одной из кнопок.

Экран уточнения включает настроенное для действия сообщение, значок и плитку содержимого с информацией о поездке, которая отменяется.

![экран уточнения фонового приложения в Кортане ](images/cortana-disambiguation-screen.png)

AdventureWorksVoiceCommandService.cs содержит следующий метод отмены поездки, который вызывает метод [**RequestDisambiguationAsync**](https://msdn.microsoft.com/library/windows/apps/dn706583), чтобы показать экран уточнения в **Кортане**.

```csharp
/// <summary>
/// Provide the user with a way to identify which trip to cancel. 
/// </summary>
/// <param name="trips">The set of trips</param>
/// <param name="disambiguationMessage">The initial disambiguation message</param>
/// <param name="secondDisambiguationMessage">Repeat prompt retry message</param>
private async Task<Model.Trip> DisambiguateTrips(IEnumerable<Model.Trip> trips, string disambiguationMessage, string secondDisambiguationMessage)
{
    // Create the first prompt message.
    var userPrompt = new VoiceCommandUserMessage();
    userPrompt.DisplayMessage =
        userPrompt.SpokenMessage = disambiguationMessage;

    // Create a re-prompt message if the user responds with an out-of-grammar response.
    var userReprompt = new VoiceCommandUserMessage();
    userReprompt.DisplayMessage =
        userReprompt.SpokenMessage = secondDisambiguationMessage;

    // Create card for each item. 
    var destinationContentTiles = new List<VoiceCommandContentTile>();
    int i = 1;
    foreach (Model.Trip trip in trips)
    {
        var destinationTile = new VoiceCommandContentTile();

        destinationTile.ContentTileType = VoiceCommandContentTileType.TitleWith68x68IconAndText;
        destinationTile.Image = await StorageFile.GetFileFromApplicationUriAsync(new Uri("ms-appx:///AdventureWorks.VoiceCommands/Images/GreyTile.png"));
        
        // The AppContext can be any arbitrary object.
        destinationTile.AppContext = trip;
        string dateFormat = "";
        if (trip.StartDate != null)
        {
            dateFormat = trip.StartDate.Value.ToString(dateFormatInfo.LongDatePattern);
        }
        else
        {
            // The app allows a trip to have no date.
            // However, the choices must be unique so they can be distinguished.
            // Here, we add a number to identify them.
            dateFormat = string.Format("{0}", i);
        } 

        destinationTile.Title = trip.Destination + " " + dateFormat;
        destinationTile.TextLine1 = trip.Description;

        destinationContentTiles.Add(destinationTile);
        i++;
    }

    // Cortana handles re-prompting if no valid response.
    var response = VoiceCommandResponse.CreateResponseForPrompt(userPrompt, userReprompt, destinationContentTiles);

    // If cortana is dismissed in this operation, null is returned.
    var voiceCommandDisambiguationResult = await
        voiceServiceConnection.RequestDisambiguationAsync(response);
    if (voiceCommandDisambiguationResult != null)
    {
        return (Model.Trip)voiceCommandDisambiguationResult.SelectedItem.AppContext;
    }

    return null;
}
```

## <span id="Error_screen"></span><span id="error_screen"></span><span id="ERROR_SCREEN"></span>Экран ошибки


Если действие, заданное голосовой командой, невозможно выполнить, служба приложения может предоставить экран ошибки.

Вот пример экрана ошибки для приложения **Adventure Works**. В этом примере пользователь указал службе приложения отменить поездку в Лас-Вегас через **Кортану**. Однако у пользователя не запланировано никаких поездок в Лас-Вегас.

Служба приложения предоставляет **Кортане** экран ошибки, который включает настроенное для действия сообщение, значок и конкретное сообщение об ошибке.

Вызовите команду [**ReportFailureAsync**](https://msdn.microsoft.com/library/windows/apps/dn706578), чтобы отобразить экран ошибки в **Кортане**.

```csharp
var userMessage = new VoiceCommandUserMessage();
    userMessage.DisplayMessage = userMessage.SpokenMessage = 
      "Sorry, you don't have any trips to Las Vegas";
                
    var response = VoiceCommandResponse.CreateResponse(userMessage);

    response.AppLaunchArgument = "showUpcomingTrips";
    await voiceServiceConnection.ReportFailureAsync(response);
```

## <span id="related_topics"></span>Связанные разделы


**Разработчикам**
* [Взаимодействие с Кортаной](cortana-interactions.md)
* [**Элементы и атрибуты в VCD-файле, версия 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593)

**Проектировщикам**
* [Рекомендации по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233)
* [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121)

**Примеры**
* [Пример голосовой команды Кортаны](http://go.microsoft.com/fwlink/p/?LinkID=619899)
 

 







<!--HONumber=Jun16_HO3-->


