---
author: mcleblanc
description: "За вашим пользовательским интерфейсом расположены уровни бизнеса и данных."
title: "Перенос уровней бизнеса и данных для Windows Phone Silverlight в UWP"
ms.assetid: 27c66759-2b35-41f5-9f7a-ceb97f4a0e3f
translationtype: Human Translation
ms.sourcegitcommit: 9dc441422637fe6984f0ab0f036b2dfba7d61ec7
ms.openlocfilehash: 6e6cea49064f49a15b557aad037c5baaa1e9b57b

---

#  <a name="porting-windows-phone-silverlight-business-and-data-layers-to-uwp"></a>Перенос уровней бизнеса и данных для Windows Phone Silverlight в UWP

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Предыдущий раздел назывался [Перенос для ввода-вывода, устройств и моделей приложений](wpsl-to-uwp-input-and-sensors.md).

За вашим пользовательским интерфейсом расположены уровни бизнеса и данных. Код в этих уровнях вызывает операционную систему и API .NET Framework (например, фоновую обработку, местонахождение, камеру, файловую систему, сети и другой доступ к данным). Подавляющее их большинство [доступно для приложения UWP](https://msdn.microsoft.com/library/windows/apps/br211369), поэтому вы можете рассчитывать на то, что большая часть этого кода может быть перенесена без изменений.

## <a name="asynchronous-methods"></a>Асинхронные методы

Один из приоритетов UWP — обеспечение возможности выполнять построение приложений, которые действительно и последовательно реагируют на действия пользователя. Анимации всегда выполняются плавно, а сенсорное взаимодействие (например, панорамирование и прокрутка) является мгновенным и выполняется без задержек, создавая впечатление, что пользовательский интерфейс соединен с вашим пальцем. Чтобы достичь этого, любой API-интерфейс UWP, который не может гарантировать завершение в пределах 50 мс, был реализован асинхронным, а к его имени добавлен суффикс **Async**. Ваш поток пользовательского интерфейса немедленно возвращается из вызова метода **Async** и работа происходит в другом потоке. Использование метода **Async** синтаксически очень простое: используются оператор C# **await**, объекты Promise JavaScript и продолжения C++. Дополнительные сведения см. в разделе [Асинхронное программирование](https://msdn.microsoft.com/library/windows/apps/mt187335).

## <a name="background-processing"></a>Фоновая обработка

Приложение Windows Phone Silverlight может использовать управляемый объект **ScheduledTaskAgent** для выполнения задачи, в то время как приложение не находится на переднем плане. Приложение UWP использует класс [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) для создания и регистрации фоновой задачи подобным образом. Вы определяете класс, реализующий работу фоновой задачи. Система выполняет фоновую задачу, периодически вызывая метод [**Run**](https://msdn.microsoft.com/library/windows/apps/br224811) вашего класса для выполнения работы. Не забудьте в приложении UWP установить объявление **Фоновые задачи** в манифесте пакета приложения. Дополнительные сведения см. в разделе [Поддержка приложения с помощью фоновых задач](https://msdn.microsoft.com/library/windows/apps/mt299103).

Для передачи больших файлов данных в фоновом режиме приложение Windows Phone Silverlight использует класс **BackgroundTransferService**. В приложении UWP для этого используются API-интерфейсы в пространстве имен [**Windows.Networking.BackgroundTransfer**](https://msdn.microsoft.com/library/windows/apps/br207242). Данные функции используют аналогичный шаблон начала передачи данных, но новый API обладает улучшенными возможностями и производительностью. Дополнительные сведения см. в разделе [Передача данных в фоновом режиме](https://msdn.microsoft.com/library/windows/apps/xaml/hh452975).

Приложение Windows Phone Silverlight использует управляемые классы в пространстве имен **Microsoft.Phone.BackgroundAudio** для воспроизведения звука, когда приложение не находится на переднем плане. UWP использует модель приложения Магазина Windows Phone. См. раздел [Фоновый звук](https://msdn.microsoft.com/library/windows/apps/mt282140) и пример [Фоновый звук](http://go.microsoft.com/fwlink/p/?linkid=619997).

## <a name="cloud-services-networking-and-databases"></a>Облачные службы, сеть и и базы данных

Размещение данных и служб приложений в облаке возможны с помощью Azure. См. раздел [Начало работы с мобильными службами](http://go.microsoft.com/fwlink/p/?LinkID=403138). Решения, которым требуются сетевые и автономные данные, описаны в разделе [Использование синхронизации автономных данных в мобильных службах](http://azure.microsoft.com/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/).

UWP частично поддерживает класс **System.Net.HttpWebRequest**, но класс **System.Net.WebClient** не поддерживается. Рекомендуемая дальновидная альтернатива — класс [**Windows.Web.Http.HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx), если необходимо, чтобы код можно было переносить на другие платформы, поддерживающие .NET). Эти API-интерфейсы используют [System.Net.Http.HttpRequestMessage](https://msdn.microsoft.com/library/system.net.http.httprequestmessage.aspx) для представления запроса HTTP.

Приложения UWP на данный момент не имеют встроенной поддержки обработки больших объемов данных, которая, в частности, требуется в бизнес-приложениях. Однако вы можете настроить использование SQLite для локальных операционных служб баз данных. Подробнее см. в разделе [SQLite](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).

Передайте абсолютные URI, а не относительные URI, типам среды выполнения Windows. См. раздел [Передача URI в среду выполнения Windows](https://msdn.microsoft.com/library/hh763341.aspx).

## <a name="launchers-and-choosers"></a>Средства запуска и выбора

С помощью средств запуска и выбора (расположенных в пространстве имен **Microsoft.Phone.Tasks**) приложение Windows Phone Silverlight может взаимодействовать с операционной системой для выполнения общих операций, таких как составление почты, выбор фотографии или предоставление некоторых типов данных другому приложению. Ищите информацию о **Microsoft.Phone.Tasks** в разделе [Сопоставление Windows Phone Silverlight с пространствами имен и классами Windows 10](wpsl-to-uwp-namespace-and-class-mappings.md), чтобы найти эквивалент данного типа UWP. Они варьируются от подобных механизмов, называемых средствами запуска и выбора, до реализации контракта для совместного использования данных между приложениями.

Приложение Windows Phone Silverlight можно поместить в неактивное состояние или даже полностью удалить при использовании, например, задание средства выбора фотографий. Приложение UWP остается активным и продолжает работу во время использования класса [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847).

## <a name="monetization-trial-mode-and-in-app-purchases"></a>Получение дохода (пробный режим и покупки из приложения)

Приложение Windows Phone Silverlight может использовать класс UWP [**CurrentApp**](https://msdn.microsoft.com/library/windows/apps/hh779765) для большей части своих функций пробного режима и возможностей покупки из приложения, поэтому нет необходимости переносить этот код. Но приложение Windows Phone Silverlight вызывает **MarketplaceDetailTask.Show**, чтобы предложить купить это приложение.

```csharp
    private void Buy()
    {
        MarketplaceDetailTask marketplaceDetailTask = new MarketplaceDetailTask();
        marketplaceDetailTask.ContentType = MarketplaceContentType.Applications;
        marketplaceDetailTask.Show();
    }
```

Перенесите этот код для вызова метода UWP [**RequestAppPurchaseAsync**](https://msdn.microsoft.com/library/windows/apps/hh967813):

```csharp
    private async void Buy()
    {
        await Windows.ApplicationModel.Store.CurrentApp.RequestAppPurchaseAsync(false);
    }
```

Если у вас есть код, который имитирует покупку вашего приложения и возможности покупки из приложения для тестирования, то вы можете перенести его, чтобы он вместо этого использовал класс [**CurrentAppSimulator**](https://msdn.microsoft.com/library/windows/apps/hh779766).

## <a name="notifications-for-tile-or-toast-updates"></a>Уведомления для обновления плиток или всплывающих уведомлений

Уведомления — это расширение модели push-уведомлений для приложений Windows Phone Silverlight. При получении уведомления из службы push-уведомлений Windows (WNS) можно отобразить эти сведения на поверхности пользовательского интерфейса с помощью обновления плитки или всплывающего уведомления. Для получения дополнительных сведений о переносе стороны пользовательского интерфейса ваших функций уведомлений см. разделы [Плитки и всплывающие уведомления](w8x-to-uwp-porting-xaml-and-ui.md).

Дополнительные сведения об использовании уведомлений в приложениях UWP см. в статье [Отправка всплывающих уведомлений](https://msdn.microsoft.com/library/windows/apps/xaml/hh868266).

Дополнительные сведения и учебники по использованию плиток, всплывающих уведомлений, индикаторов событий, баннеров и уведомлений в приложениях среды выполнения Windows на C++, C# или Visual Basic см. в статье [Работа с плитками, индикаторами событий и всплывающими уведомлениями](https://msdn.microsoft.com/library/windows/apps/xaml/hh868259).

## <a name="storage-file-access"></a>Хранилище (доступ к файловой системе)

Код Windows Phone Silverlight, который хранит параметры приложения в виде пар "ключ-значение" в изолированном хранилище легко переносится. Ниже приведен пример до и после, сначала показана версия Windows Phone Silverlight.

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

Хотя часть пространства имен **Windows.Storage** им доступна, многие приложения Windows Phone Silverlight выполняют вводы-выводы файла с помощью класса **IsolatedStorageFile**, потому что он поддерживается на протяжении большего количества времени. Ниже приведен пример написания и чтения файла до и после с условием, что используется **IsolatedStorageFile**. Сначала показана версия Windows Phone Silverlight.

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

Приложение Windows Phone Silverlight имеет доступ к дополнительной SD-карте только для чтения. Приложение UWP имеет доступ к SD-карте для чтения и записи. Дополнительные сведения см. в разделе [Доступ к SD-карте](https://msdn.microsoft.com/library/windows/apps/mt188699).

Сведения о доступе к файлам фотографий, музыки и видео в приложении UWP см. в разделе [Файлы и папки в библиотеках музыки, изображений и видео](https://msdn.microsoft.com/library/windows/apps/mt188703).

Дополнительную информацию см. в разделе [Файлы, папки и библиотеки](https://msdn.microsoft.com/library/windows/apps/mt185399).

Следующий раздел называется [Перенос для форм-фактора и взаимодействия с пользователем](wpsl-to-uwp-form-factors-and-ux.md).

## <a name="related-topics"></a>Связанные разделы

* [Сопоставление пространства имен и класса](wpsl-to-uwp-namespace-and-class-mappings.md)
 




<!--HONumber=Dec16_HO1-->


