---
description: За вашим пользовательским интерфейсом расположены уровни бизнеса и данных.
title: Перенос уровней данных и бизнес-Windows Phone Silverlight для универсальной платформы Windows
ms.assetid: 27c66759-2b35-41f5-9f7a-ceb97f4a0e3f
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 25e7fdcb4195dcc0dffed7657d41bd02bea8a5c2
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67322302"
---
#  <a name="porting-windowsphone-silverlight-business-and-data-layers-to-uwp"></a>Перенос уровней данных и бизнес-Windows Phone Silverlight для универсальной платформы Windows


Предыдущий раздел назывался [Перенос для ввода-вывода, устройств и моделей приложений](wpsl-to-uwp-input-and-sensors.md).

За вашим пользовательским интерфейсом расположены уровни бизнеса и данных. Код в этих уровнях вызывает операционную систему и API .NET Framework (например, фоновую обработку, местонахождение, камеру, файловую систему, сети и другой доступ к данным). Подавляющее их большинство [доступно для приложения UWP](https://docs.microsoft.com/previous-versions/windows/br211369(v=win.10)), поэтому вы можете рассчитывать на то, что большая часть этого кода может быть перенесена без изменений.

## <a name="asynchronous-methods"></a>Асинхронные методы

Один из приоритетов UWP — обеспечение возможности выполнять построение приложений, которые действительно и последовательно реагируют на действия пользователя. Анимации всегда выполняются плавно, а сенсорное взаимодействие (например, панорамирование и прокрутка) является мгновенным и выполняется без задержек, создавая впечатление, что пользовательский интерфейс соединен с вашим пальцем. Чтобы достичь этого, любой API-интерфейс UWP, который не может гарантировать завершение в пределах 50 мс, был реализован асинхронным, а к его имени добавлен суффикс **Async**. Ваш поток пользовательского интерфейса немедленно возвращается из вызова метода **Async** и работа происходит в другом потоке. Использование метода **Async** синтаксически очень простое: используются оператор C# **await**, объекты Promise JavaScript и продолжения C++. Дополнительные сведения см. в разделе [Асинхронное программирование](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-universal-windows-platform-apps).

## <a name="background-processing"></a>Фоновая обработка

Приложения Windows Phone Silverlight можно использовать управляемый **ScheduledTaskAgent** объект для выполнения задачи, пока приложение не находится на переднем плане. Приложение UWP использует класс [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) для создания и регистрации фоновой задачи подобным образом. Вы определяете класс, реализующий работу фоновой задачи. Система выполняет фоновую задачу, периодически вызывая метод [**Run**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) вашего класса для выполнения работы. Не забудьте в приложении UWP установить объявление **Фоновые задачи** в манифесте пакета приложения. Дополнительные сведения см. в разделе [Поддержка приложения с помощью фоновых задач](https://docs.microsoft.com/windows/uwp/launch-resume/support-your-app-with-background-tasks).

Для передачи больших объемов данных файлов в фоновом режиме, используется в приложении Windows Phone Silverlight **BackgroundTransferService** класса. В приложении UWP для этого используются API-интерфейсы в пространстве имен [**Windows.Networking.BackgroundTransfer**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer). Данные функции используют аналогичный шаблон начала передачи данных, но новый API обладает улучшенными возможностями и производительностью. Дополнительные сведения см. в разделе [Передача данных в фоновом режиме](https://docs.microsoft.com/previous-versions/windows/apps/hh452975(v=win.10)).

Приложения Windows Phone Silverlight использует управляемые классы в **Microsoft.Phone.BackgroundAudio** пространство имен для воспроизведения звука при приложение не находится на переднем плане. UWP использует модель приложения Магазина Windows Phone. См. раздел [Фоновый звук](https://docs.microsoft.com/windows/uwp/audio-video-camera/background-audio) и пример [Фоновый звук](https://go.microsoft.com/fwlink/p/?linkid=619997).

## <a name="cloud-services-networking-and-databases"></a>Облачные службы, сеть и и базы данных

Размещение данных и служб приложений в облаке возможны с помощью Azure. См. раздел [Начало работы с мобильными службами](https://go.microsoft.com/fwlink/p/?LinkID=403138). Для решений, которые требуют автономно и данных см. в разделе: [Использование синхронизации автономных данных в мобильных службах](https://azure.microsoft.com/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/).

UWP частично поддерживает класс **System.Net.HttpWebRequest**, но класс **System.Net.WebClient** не поддерживается. Рекомендуемая дальновидная альтернатива — класс [**Windows.Web.Http.HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118)), если необходимо, чтобы код можно было переносить на другие платформы, поддерживающие .NET). Эти API-интерфейсы используют [System.Net.Http.HttpRequestMessage](https://docs.microsoft.com/previous-versions/visualstudio/hh159020(v=vs.118)) для представления запроса HTTP.

Приложения UWP на данный момент не имеют встроенной поддержки обработки больших объемов данных, которая, в частности, требуется в бизнес-приложениях. Однако вы можете настроить использование SQLite для локальных операционных служб баз данных. Подробнее см. в разделе [SQLite](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform).

Передайте абсолютные URI, а не относительные URI, типам среды выполнения Windows. См. раздел [Передача URI в среду выполнения Windows](https://docs.microsoft.com/dotnet/standard/cross-platform/passing-a-uri-to-the-windows-runtime).

## <a name="launchers-and-choosers"></a>Средства запуска и выбора

С помощью средства запуска, а также навигацией (в **Microsoft.Phone.Tasks** пространство имен), приложение Windows Phone Silverlight можно взаимодействовать с операционной системой для выполнения общих операций, такие как составление сообщения электронной почты, выбрав фотографию, или совместное использование некоторых типов данных с другим приложением. Поиск **Microsoft.Phone.Tasks** в разделе [Windows Phone Silverlight для Windows 10 сопоставления пространства имен и класса](wpsl-to-uwp-namespace-and-class-mappings.md) для поиска эквивалентных типа универсальной платформы Windows. Они варьируются от подобных механизмов, называемых средствами запуска и выбора, до реализации контракта для совместного использования данных между приложениями.

Приложения Windows Phone Silverlight можно поместить в неактивное состояние или даже полностью отключенного при использовании, например, задача выбора фотографий. Приложение UWP остается активным и продолжает работу во время использования класса [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker).

## <a name="monetization-trial-mode-and-in-app-purchases"></a>Получение дохода (пробный режим и покупки из приложения)

Приложения Windows Phone Silverlight можно использовать UWP [**CurrentApp** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp) класс большая часть его режим пробной версии и в приложении приобрести функциональные возможности, таким образом, чтобы код не должен быть перенесена. Но приложение Windows Phone Silverlight вызывает **MarketplaceDetailTask.Show** предложить приложение для приобретения:

```csharp
    private void Buy()
    {
        MarketplaceDetailTask marketplaceDetailTask = new MarketplaceDetailTask();
        marketplaceDetailTask.ContentType = MarketplaceContentType.Applications;
        marketplaceDetailTask.Show();
    }
```

Перенесите этот код для вызова метода UWP [**RequestAppPurchaseAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestapppurchaseasync):

```csharp
    private async void Buy()
    {
        await Windows.ApplicationModel.Store.CurrentApp.RequestAppPurchaseAsync(false);
    }
```

Если у вас есть код, который имитирует покупку вашего приложения и возможности покупки из приложения для тестирования, то вы можете перенести его, чтобы он вместо этого использовал класс [**CurrentAppSimulator**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentAppSimulator).

## <a name="notifications-for-tile-or-toast-updates"></a>Уведомления для обновления плиток или всплывающих уведомлений

Уведомления являются расширением модели уведомлений Push-уведомлений для приложений Windows Phone Silverlight. При получении уведомления из службы push-уведомлений Windows (WNS) можно отобразить эти сведения на поверхности пользовательского интерфейса с помощью обновления плитки или всплывающего уведомления. Для получения дополнительных сведений о переносе стороны пользовательского интерфейса ваших функций уведомлений см. разделы [Плитки и всплывающие уведомления](w8x-to-uwp-porting-xaml-and-ui.md).

Дополнительные сведения об использовании уведомлений в приложениях UWP см. в статье [Отправка всплывающих уведомлений](https://docs.microsoft.com/previous-versions/windows/apps/hh868266(v=win.10)).

Дополнительные сведения и учебники по использованию плиток, всплывающих уведомлений, индикаторов событий, баннеров и уведомлений в приложениях среды выполнения Windows на C++, C# или Visual Basic см. в статье [Работа с плитками, индикаторами событий и всплывающими уведомлениями](https://docs.microsoft.com/previous-versions/windows/apps/hh868259(v=win.10)).

## <a name="storage-file-access"></a>Хранилище (доступ к файловой системе)

Код Windows Phone Silverlight, который хранит параметры приложения в виде пар "ключ значение", в изолированном хранилище легко перенести. Ниже приведен пример до и после, сначала версии Windows Phone Silverlight:

```csharp
    var propertySet = IsolatedStorageSettings.ApplicationSettings;
    const string key = "favoriteAuthor";
    propertySet[key] = "Charles Dickens";
    propertySet.Save();
    string myFavoriteAuthor = propertySet.Contains(key) ? (string)propertySet[key] : "<none>";
```

И эквивалент UWP:

```csharp
    var propertySet = Windows.Storage.ApplicationData.Current.LocalSettings.Values;
    const string key = "favoriteAuthor";
    propertySet[key] = "Charles Dickens";
    string myFavoriteAuthor = propertySet.ContainsKey(key) ? (string)propertySet[key] : "<none>";
```

Несмотря на то что подмножество **Windows.Storage** пространство имен доступно на них, многие Silverlight Windows Phone, выполнять приложения файловый ввод-вывод с **IsolatedStorageFile** класса, так как он поддерживается для больше времени. Предположим, что **IsolatedStorageFile** — используется, ниже приведен пример до и после записи и чтения файла, сначала версии Windows Phone Silverlight:

```csharp
    const string filename = "FavoriteAuthor.txt";
    using (var store = IsolatedStorageFile.GetUserStoreForApplication())
    {
        using (var streamWriter = new StreamWriter(store.CreateFile(filename)))
        {
            streamWriter.Write("Charles Dickens");
        }
        using (var StreamReader = new StreamReader(store.OpenFile(filename, FileMode.Open, FileAccess.Read)))
        {
            string myFavoriteAuthor = StreamReader.ReadToEnd();
        }
    }
```

И та же функция с помощью UWP.

```csharp
    const string filename = "FavoriteAuthor.txt";
    var store = Windows.Storage.ApplicationData.Current.LocalFolder;
    Windows.Storage.StorageFile file = await store.CreateFileAsync(filename, Windows.Storage.CreationCollisionOption.ReplaceExisting);
    await Windows.Storage.FileIO.WriteTextAsync(file, "Charles Dickens");
    file = await store.GetFileAsync(filename);
    string myFavoriteAuthor = await Windows.Storage.FileIO.ReadTextAsync(file);
```

Приложения Windows Phone Silverlight имеет доступ только для чтения, необязательно SD-карту. Приложение UWP имеет доступ к SD-карте для чтения и записи. Дополнительные сведения см. в разделе [Доступ к SD-карте](https://docs.microsoft.com/windows/uwp/files/access-the-sd-card).

Сведения о доступе к файлам фотографий, музыки и видео в приложении UWP см. в разделе [Файлы и папки в библиотеках музыки, изображений и видео](https://docs.microsoft.com/windows/uwp/files/quickstart-managing-folders-in-the-music-pictures-and-videos-libraries).

Дополнительную информацию см. в разделе [Файлы, папки и библиотеки](https://docs.microsoft.com/windows/uwp/files/index).

Следующий раздел называется [Перенос для форм-фактора и взаимодействия с пользователем](wpsl-to-uwp-form-factors-and-ux.md).

## <a name="related-topics"></a>См. также

* [Пространство имен и класс сопоставления](wpsl-to-uwp-namespace-and-class-mappings.md)
 

