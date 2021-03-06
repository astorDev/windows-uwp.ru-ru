---
title: Запуск приложения по умолчанию для URI
description: Узнайте, как запускать приложение по умолчанию для универсального кода ресурса (URI). Универсальные коды ресурсов (URI) позволяют запустить приложение, чтобы выполнить определенную задачу. В этом разделе также приводится обзор множества схем URI, встроенных в Windows.
ms.assetid: 7B0D0AF5-D89E-4DB0-9B79-90201D79974F
ms.date: 06/26/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 0d636b5689d604c0eaa3b66763709251a2445deb
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75685214"
---
# <a name="launch-the-default-app-for-a-uri"></a>Запуск приложения по умолчанию для URI


**Важные API**

- [**лаунчуриасинк**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync)
- [**преферредаппликатионпаккажефамилинаме**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.preferredapplicationpackagefamilyname)
- [**десиредремаинингвиев**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.desiredremainingview)

Узнайте, как запускать приложение по умолчанию для универсального кода ресурса (URI). Универсальные коды ресурсов (URI) позволяют запустить приложение, чтобы выполнить определенную задачу. В этом разделе также приводится обзор множества схем URI, встроенных в Windows. Вы также можете запустить собственные схемы URI. Дополнительные сведения о регистрации собственной схемы URI и обработке активации URI см. в разделе [Обработка активации URI](handle-uri-activation.md).

Схемы URI позволяют открывать приложения, щелкая гиперссылки. Таким же образом можно создать электронное сообщение с помощью **mailto:** или открыть веб-браузер по умолчанию с помощью **http:** .

В этом разделе описываются указанные ниже схемы URI, встроенные в Windows:

| Схема URI | Запускает |
| ----------:|----------|
|[bingmaps:, MS-Drive-to: и MS-пошаговое руководство:](#maps-app-uri-schemes) | Приложение «Карты» |
|[НТТР](#http-uri-scheme) | Веб-браузер по умолчанию |
|[:)](#email-uri-scheme) | Приложение электронной почты по умолчанию |
|[MS-Call:](#call-app-uri-scheme) |  Приложение «Звонок» |
|[MS-чат:](#messaging-app-uri-scheme) | Приложение «Сообщения» |
|[MS-люди:](#people-app-uri-scheme) | Приложение «Люди» |
|[MS-Photos:](#photos-app-uri-scheme) | Приложение "Фотографии" |
|[Параметры MS:](#settings-app-uri-scheme) | Приложение «Параметры» |
|[MS-Store:](#store-app-uri-scheme)  | Приложение «Магазин» |
|[MS-тонепиккер:](#tone-picker-uri-scheme) | Средство выбора звуковых сигналов |
|[MS-елловпаже:](#nearby-numbers-app-uri-scheme) | Приложения «Телефоны мест по соседству» |
|[мснвеасер:](#weather-app-uri-scheme) | Приложение Weather |

<br>
Например, следующая схема URI открывает браузер, заданный по умолчанию, и отображает веб-сайт Bing.

`https://bing.com`

Кроме того, вы можете запустить собственные схемы URI. Если ни одно из установленных приложений не предназначено для обработки URI, можно порекомендовать пользователю приложение для установки. Дополнительные сведения см. в разделе [Рекомендовать приложение, если для обработки URI нет доступных приложений](#recommend-an-app-if-one-is-not-available-to-handle-the-uri).

В большинстве случаев ваше приложение не может выбирать запускаемое приложение. Пользователь сам определяет, какое приложение запустить. Можно зарегистрировать несколько приложений для обработки одной схемы URI. Исключение составляют зарезервированные схемы URI. Регистрация зарезервированных схем URI игнорируется. Полный список зарезервированных схем URI см. в разделе [Обработка активации URI](handle-uri-activation.md). В тех случаях, когда несколько приложений могут зарегистрировать одну схему URI, ваше приложение может порекомендовать определенное приложение для запуска. Дополнительные сведения см. в разделе [Рекомендовать приложение, если для обработки URI нет доступных приложений](#recommend-an-app-if-one-is-not-available-to-handle-the-uri).

### <a name="call-launchuriasync-to-launch-a-uri"></a>Вызов метода LaunchUriAsync для запуска URI

Используйте метод [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync) для запуска URI. При вызове этого метода ваше приложение должно быть приложением переднего плана, то есть быть видимым пользователю. Это требование обеспечивает контроль пользователя над ситуацией. Для соблюдения этого требования убедитесь, что все запуски URI напрямую связаны с пользовательским интерфейсом вашего приложения. Чтобы запустить URI, пользователь всегда должен выполнить какое-либо действие. Если вы попытаетесь запустить URI, когда приложение не находится на переднем плане, запуск завершится ошибкой, и будет выполнен обратный вызов для ошибки.

Сначала создайте объект [**System.Uri**](https://docs.microsoft.com/dotnet/api/system.uri) для представления URI, а затем передайте его методу [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync). С помощью возвращаемого результата узнайте, был ли вызов выполнен успешно, как показано в следующем примере.

```cs
private async void launchURI_Click(object sender, RoutedEventArgs e)
{
   // The URI to launch
   var uriBing = new Uri(@"http://www.bing.com");

   // Launch the URI
   var success = await Windows.System.Launcher.LaunchUriAsync(uriBing);

   if (success)
   {
      // URI launched
   }
   else
   {
      // URI launch failed
   }
}
```

В некоторых случаях операционная система отправляет пользователю запрос, чтобы определить, действительно ли нужно переключиться между приложениями.

![Диалоговое окно предупреждения с перекрытием на фоне неактивного приложения. Диалоговое окно, запрашивающее у пользователя подтверждение переключения между приложениями, с кнопками «Да» и «Нет» в правой нижней части. Выбрана кнопка «Нет».](images/warningdialog.png)

Если вы хотите, чтобы этот запрос выполнялся постоянно, используйте свойство [**Windows.System.LauncherOptions.TreatAsUntrusted**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.treatasuntrusted), чтобы указать, что операционная система должна вывести на экран предупреждение.

```cs
// The URI to launch
var uriBing = new Uri(@"http://www.bing.com");

// Set the option to show a warning
var promptOptions = new Windows.System.LauncherOptions();
promptOptions.TreatAsUntrusted = true;

// Launch the URI
var success = await Windows.System.Launcher.LaunchUriAsync(uriBing, promptOptions);
```

### <a name="recommend-an-app-if-one-is-not-available-to-handle-the-uri"></a>Если приложение для обработки данного URI отсутствует, порекомендуйте приложение.

В некоторых случаях у пользователя может быть не установлено приложение для обработки запускаемого URI. Тогда по умолчанию операционная система предоставит пользователю ссылку для поиска подходящего приложения в Магазине. Если вы при этом хотите порекомендовать пользователю конкретное приложение, вы можете передать рекомендацию вместе с запускаемым URI.

Кроме того, рекомендации полезны при регистрации нескольких приложений для обработки схемы URI. Порекомендовав конкретное приложение, Windows откроет это приложение, при условии что оно уже установлено.

Чтобы порекомендовать приложение, вызовите метод [**Windows.System.Launcher.LaunchUriAsync(Uri, LauncherOptions)** ](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync#Windows_System_Launcher_LaunchUriAsync_Windows_Foundation_Uri_Windows_System_LauncherOptions_), указав в качестве значения параметра [**LauncherOptions.preferredApplicationPackageFamilyName**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.preferredapplicationpackagefamilyname) имя семейства пакета приложения Магазина, которое вы рекомендуете. Операционная система будет использовать эту информацию, чтобы заменить общий параметр (поиск приложения в Магазине) конкретным параметром (приобретение рекомендованного приложения в Магазине).

```cs
// Set the recommended app
var options = new Windows.System.LauncherOptions();
options.PreferredApplicationPackageFamilyName = "Contoso.URIApp_8wknc82po1e";
options.PreferredApplicationDisplayName = "Contoso URI Ap";

// Launch the URI and pass in the recommended app
// in case the user has no apps installed to handle the URI
var success = await Windows.System.Launcher.LaunchUriAsync(uriContoso, options);
```

### <a name="set-remaining-view-preference"></a>Настройка представления оставшегося пространства

Исходные приложения, вызывающие [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync), могут запрашивать разрешение остаться на экране после запуска URI. По умолчанию Windows пытается поровну поделить все доступное пространство между исходным приложением и конечным приложением, обрабатывающим URI. Исходные приложения могут использовать свойство [**DesiredRemainingView**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.desiredremainingview), чтобы сообщить операционной системе, что для окна приложения требуется больше или меньше доступного пространства. При помощи **DesiredRemainingView** также можно указать, что исходному приложению не нужно оставаться на экране после запуска URI и что его пространство можно полностью занять конечным приложением. Это свойство указывает только предпочтительный размер окна для вызывающего приложения. Оно не задает условия для других приложений, которые могут находиться на экране в это же время.

**Обратите внимание** ,  Windows учитывает несколько различных факторов при определении конечного размера окна исходного приложения, например предпочтений исходного приложения, количества приложений на экране, ориентации экрана и т. д. Задав [**DesiredRemainingView**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.desiredremainingview), вы не гарантируете конкретного поведения окон для исходного приложения.

```cs
// Set the desired remaining view.
var options = new Windows.System.LauncherOptions();
options.DesiredRemainingView = Windows.UI.ViewManagement.ViewSizePreference.UseLess;

// Launch the URI
var success = await Windows.System.Launcher.LaunchUriAsync(uriContoso, options);
```

## <a name="uri-schemes"></a>Схемы URI ##

Ниже описаны различные схемы URI.

### <a name="call-app-uri-scheme"></a>Схема URI приложения «Звонок»

Используйте схему URI **ms-call:** для запуска приложения «Звонок».

| Схема URI       | Результат                   |
|------------------|--------------------------|
| ms-call:settings | Вызов страницы параметров приложения. |

### <a name="email-uri-scheme"></a>Схема URI электронной почты

Используйте схему URI **mailto:** для запуска почтового приложения, заданного по умолчанию.

| Схема URI |Результаты                          |
|------------|---------------------------------|
| mailto:    | Запускает почтовое приложение, заданное по умолчанию. |
| mailto:\[адрес электронной почты\] | Запускает почтовое приложение и создает новое сообщение с указанным электронным адресом в строке «Кому». Обратите внимание, что электронная почта не отправляется, пока пользователь не нажмет кнопку «Отправить». |

### <a name="http-uri-scheme"></a>Схема URI HTTP

Используйте схему URI **http:** для запуска веб-браузера, заданного по умолчанию.

| Схема URI | Результаты                           |
|------------|-----------------------------------|
| http:      | Запуск веб-браузера, заданного по умолчанию. |

### <a name="maps-app-uri-schemes"></a>Схемы URI приложения «Карты»

Используйте схемы URI **bingmaps:** , **ms-drive-to:** и **ms-walk-to:** для [запуска приложения «Карты Windows»](launch-maps-app.md) с определенными картами, маршрутами и результатами поиска. Например, следующий URI открывает приложение «Карты Windows» и отображает карту, в центре которой расположен Нью-Йорк.

`bingmaps:?cp=40.726966~-74.006076`

![Пример приложения Карты Windows.](images/mapnyc.png)

Дополнительные сведения см. в разделе [Запуск приложения "Карты Windows"](launch-maps-app.md). Подробнее об использовании элемента управления картой в приложении описано в статье [Отображение карт с двухмерными и трехмерными представлениями, а также с представлениями Streetside](https://docs.microsoft.com/windows/uwp/maps-and-location/display-maps).

### <a name="messaging-app-uri-scheme"></a>Схема URI приложения «Сообщения»

Используйте схему URI **ms-chat:** для запуска приложения «Сообщения Windows».

| Схема URI |Результаты |
|------------|--------|
| ms-chat:   | Запускает приложение для обмена сообщениями. |
| ms-chat:?ContactID={contacted}  |  Позволяет приложению для обмена сообщениями запускаться с определенными контактными сведениями.   |
| ms-chat:?Body={body} | Позволяет приложению для обмена сообщениями запускаться со строкой для использования в качестве содержимого сообщения.|
| ms-chat:?Addresses={address}&Body={body} | Позволяет приложению для обмена сообщениями запускаться с определенными сведениями об адресах и со строкой для использования в качестве содержимого сообщения. Примечание. Адреса могут быть объединены. |
| ms-chat:?TransportId={transportId}  | Позволяет приложению для обмена сообщениями запускаться с определенным кодом транспортировки. |

### <a name="tone-picker-uri-scheme"></a>Схема URI средства выбора звуковых сигналов

Используйте схему URI **ms tonepicker:** для выбора мелодий звонка, сигналов будильника и системных звуковых сигналов. Можно также сохранить новые мелодии звонка и получить отображаемое имя звукового сигнала.

| Схема URI | Результаты |
|------------|---------|
| ms-tonepicker: | Выбор мелодий звонка, сигналов будильника и системных звуковых сигналов. |

Параметры передаются в API LaunchURI через [ValueSet](https://docs.microsoft.com/uwp/api/windows.foundation.collections.valueset). Подробнее см. раздел [Выбор и сохранение звуковых сигналов с помощью схемы URI ms-tonepicker](launch-ringtone-picker.md).

### <a name="nearby-numbers-app-uri-scheme"></a>Схема URI приложения «Телефоны мест по соседству»

Используйте схему URI **ms-yellowpage:** для запуска приложения «Телефоны мест по соседству».

| Схема URI | Результаты |
|------------|---------|
| MS-елловпаже:? input =\[ключевое слово\]& Method =\[строка или T9\] | Запуск приложения «Телефоны мест по соседству».<br>`input` ссылается на ключевое слово, которое требуется найти.<br>`method` относится к типу поиска (String или T9 Search).<br>Если параметр `method` имеет значение `T9` (тип клавиатуры), параметр `keyword` должен быть цифровой строкой, соответствующей буквам на клавиатуре T9, по которым требуется произвести поиск.<br>Если параметр `method` имеет значение `String`, параметр `keyword` представляет собой ключевое слово, которое требуется найти. |

### <a name="people-app-uri-scheme"></a>Схема URI приложения «Люди»

Используйте схему URI **ms-people:** для запуска приложения «Люди».
Дополнительные сведения см. в разделе [Запуск приложения «Люди»](launch-people-apps.md).

### <a name="photos-app-uri-scheme"></a>Схема URI приложения «Фотографии»

Используйте схему URI **ms-photos:** для запуска приложения "Фотографии", чтобы просмотреть изображение или редактировать видео. Пример  
Просмотр изображения: `ms-photos:viewer?fileName=c:\users\userName\Pictures\image.jpg`  
Или, чтобы изменить видео: `ms-photos:videoedit?InputToken=123abc&Action=Trim&StartTime=01:02:03`  

> [!NOTE]
> URI для редактирования видео или отображения изображения доступны только на настольных системах.

| Схема URI |Результаты |
|------------|--------|
| ms-photos:viewer?fileName={filename} | Запускает приложение "Фотографии" для просмотра указанного изображения, где {filename} — имя с полным путем. Пример: `c:\users\userName\Pictures\ImageToView.jpg` |
| ms-photos:videoedit?InputToken={input token} | Запускает приложение "Фотографии" в режиме редактирования видео для файла, представленного маркером файла. **InputToken** является обязательным. Используйте [SharedStorageAccessManager](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.SharedStorageAccessManager) для получения маркера файла. |
| ms-photos:videoedit?Action={action} | Необязательный параметр, который открывает приложение "Фотографии" в заданном режиме редактирования видео, где вместо {action} используется одно из следующих значений: **SlowMotion**, **FrameExtraction**, **Trim**, **View**, **Ink**. Если действие не задано, по умолчанию используется **View** |
| ms-photos:videoedit?StartTime={timespan} | Необязательный параметр, указывающий, откуда начать воспроизведение видео. `{timespan}` должны быть в формате `"hh:mm:ss.ffff"`. Если не указано, по умолчанию используется `00:00:00.0000` |

### <a name="settings-app-uri-scheme"></a>Схема URI приложения «Параметры»

Используйте схему URI **ms-settings:** для [запуска приложения «Параметры» для Windows](launch-settings-app.md). Запуск приложения «Параметры» — важная составляющая создания приложения, учитывающего требования конфиденциальности. Если ваше приложение не может получить доступ к конфиденциальному ресурсу, рекомендуется предоставить пользователю удобную ссылку на параметры конфиденциальности для этого ресурса. Например, следующий URI открывает приложение «Параметры» и отображает параметры конфиденциальности камеры.

`ms-settings:privacy-webcam`

![Параметры конфиденциальности камеры.](images/privacyawarenesssettingsapp.png)

Дополнительные сведения см. в разделе: [Запуск приложения «Параметры» для Windows](launch-settings-app.md) и [Руководство по приложениям, учитывающим требования конфиденциальности](https://docs.microsoft.com/windows/uwp/security/index).

### <a name="store-app-uri-scheme"></a>Схема URI для приложения Store

Используйте схему URI **ms-windows-store:** для [запуска приложения UWP](launch-store-app.md). Открытие страниц с подробным описанием продукта, страниц с обзором продукта, страниц поиска и др. Например, следующий URI открывает приложение UWP и запускает домашнюю страницу Store.

`ms-windows-store://home/`

Дополнительные сведения см. в разделе [Запуск приложения UWP](launch-store-app.md).

### <a name="weather-app-uri-scheme"></a>Схема URI приложения Weather

Используйте схему **мснвеасер:** URI для запуска приложения Weather.

| Схема URI | Результаты |
|------------|---------|
| мснвеасер://форекаст? La =\[Широта\]& с\[долготой\] | Запускает приложение Weather на странице прогноза на основе географических координат расположения.<br>`latitude` ссылается на широту расположения.<br> `longitude` ссылается на долготу расположения.<br> |
