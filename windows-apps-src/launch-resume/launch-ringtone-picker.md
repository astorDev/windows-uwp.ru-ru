---
title: Схема ms-tonepicker
description: В этом разделе описывается схема URI ms-tonepicker и порядок ее использования для отображения средства выбора звуковых сигналов с целью выбора звукового сигнала, сохранения звукового сигнала и получения понятного имени звукового сигнала.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 0c17e4fb-7241-4da9-b457-d6d3a7aefccb
ms.localizationpriority: medium
ms.openlocfilehash: 78ed118ba15f38f8914cf2046344d782cd0df71b
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370797"
---
# <a name="choose-and-save-tones-using-the-ms-tonepicker-uri-scheme"></a>Выбор и сохранение звуковых сигналов с помощью схемы URI ms-tonepicker

В этом разделе описывается использование **ms tonepicker:** Схема URI. Эту схему URI можно использовать для указанных ниже целей.
- Определение доступности средства выбора звуковых сигналов на устройстве.
- Отображение средства выбора звуковых сигналов для вывода доступных мелодий звонка, системных звуков, мелодий SMS и звуковых сигналов, а также для получения маркера звукового сигнала, который представляет выбранный пользователем звук.
- Отображение средства сохранения звукового сигнала, которое в качестве входных данных получает маркер звукового файла и сохраняет его на устройстве. Сохраненные звуковые сигналы становятся доступными через средство выбора звуковых сигналов. Пользователи могут также присвоить звуковому сигналу понятное имя.
- Преобразование маркера звукового сигнала в его понятное имя.

## <a name="ms-tonepicker-uri-scheme-reference"></a>ms-tonepicker: Схема URI-ссылка

Эта схема URI не передает аргументы через строку схемы URI; вместо этого аргументы передаются через [ValueSet](https://docs.microsoft.com/uwp/api/windows.foundation.collections.valueset). Во всех строках учитывается регистр.

В приведенных ниже разделах указано, какие аргументы должны передаваться для выполнения указанной задачи.

## <a name="task-determine-if-the-tone-picker-is-available-on-the-device"></a>Задача. Определить, доступно ли средство выбора сигнала на устройстве
```cs
var status = await Launcher.QueryUriSupportAsync(new Uri("ms-tonepicker:"),     
                                     LaunchQuerySupportType.UriForResults,
                                     "Microsoft.Tonepicker_8wekyb3d8bbwe");

if (status != LaunchQuerySupportStatus.Available)
{
    // the tone picker is not available
}
```

## <a name="task-display-the-tone-picker"></a>Задача. Отобразить средство выбора звуковой сигнал

Ниже перечислены аргументы, которые можно передавать для отображения средства выбора звуковых сигналов.

| Параметр | Тип | Обязательно | Возможные значения | Описание |
|-----------|------|----------|-------|-------------|
| Действие | строка | Да | "PickRingtone" | Открытие средства выбора звуковых сигналов. |
| CurrentToneFilePath | строка | Нет | Маркер существующего звукового сигнала. | Звуковой сигнал, который должен отображаться как текущий в средстве выбора звуковых сигналов. Если это значение не задано, по умолчанию выбирается первый звуковой сигнал в списке.<br>Строго говоря, это не является путем к файлу. Подходящее значение для параметра `CurrenttoneFilePath` можно получить из значения `ToneToken`, возвращаемого средством выбора звуковых сигналов.  |
| TypeFilter | строка | Нет | "Ringtones", "Notifications", "Alarms", "None" | Выбор звуковых сигналов, добавляемых в средство выбора. Если никакой фильтр не задан, отображаются все звуковые сигналы. |

Значения, возвращаемые в [LaunchUriResults.Result](https://docs.microsoft.com/uwp/api/windows.system.launchuriresult.result):

| Возвращаемые значения | Тип | Возможные значения | Описание |
|--------------|------|-------|-------------|
| Результат | Int32 | 0 – успех. <br>1 – отменено. <br>7 – недопустимые параметры. <br>8 – нет звуковых сигналов, удовлетворяющих условиям фильтра. <br>255 – указанное действие не реализовано. | Результат операции в средстве выбора. |
| ToneToken | строка | Маркер выбранного звукового сигнала. <br>Эта строка будет пустой, если пользователь выбирает в средстве выбора значение **по умолчанию**. | Этот маркер может использоваться в полезных данных всплывающего уведомления, или его можно назначить мелодии звонка или мелодию SMS контакта. Этот параметр возвращается в ValueSet только в случае, если **Result** равен 0. |
| DisplayName | строка | Понятное имя указанного звукового сигнала. | Строка, которая может отображаться пользователю для представления выбранного звукового сигнала. Этот параметр возвращается в ValueSet только в случае, если **Result** равен 0. |


**Пример: Откройте средство выбора тон, чтобы пользователь мог выбрать звуковой сигнал**

``` cs
LauncherOptions options = new LauncherOptions();
options.TargetApplicationPackageFamilyName = "Microsoft.Tonepicker_8wekyb3d8bbwe";

ValueSet inputData = new ValueSet() {
    { "Action", "PickRingtone" },
    { "TypeFilter", "Ringtones" } // Show only ringtones
};

LaunchUriResult result = await Launcher.LaunchUriForResultsAsync(new Uri("ms-tonepicker:"), options, inputData);

if (result.Status == LaunchUriStatus.Success)
{
     Int32 resultCode =  (Int32)result.Result["Result"];
     if (resultCode == 0)
     {
         string token = result.Result["ToneToken"] as string;
         string name = result.Result["DisplayName"] as string;
     }
     else
     {
           // handle failure
     }
}
```

## <a name="task-display-the-tone-saver"></a>Задача. Отображение заставки тон

Ниже перечислены аргументы, которые можно передавать для отображения средства сохранения звуковых сигналов.

| Параметр | Тип | Обязательно | Возможные значения | Описание |
|-----------|------|----------|-------|-------------|
| Действие | строка | Да | "SaveRingtone" | Открытие средства выбора для сохранения мелодии звонка. |
| ToneFileSharingToken | строка | Да | Метка [SharedStorageAccessManager](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.sharedstorageaccessmanager) для общего доступа к сохраняемому файлу мелодии звонка. | Сохранение определенного файла звукового сигнала в качестве мелодии звонка. Поддерживаемые типы содержимого для файла: звук mpeg и x-ms-wma. |
| DisplayName | строка | Нет | Понятное имя указанного звукового сигнала. | Задает отображаемое имя для использования при сохранении указанной мелодии звонка. |

Значения, возвращаемые в [LaunchUriResults.Result](https://docs.microsoft.com/uwp/api/windows.system.launchuriresult.result):

| Возвращаемые значения | Тип | Возможные значения | Описание |
|--------------|------|-------|-------------|
| Результат | Int32 | 0 – успех.<br>1 – отменено пользователем.<br>2 – недопустимый файл.<br>3 – недопустимый тип содержимого файла.<br>4 – размер файла превышает максимальный размер мелодии звонка (1 МБ в Windows 10).<br>5 – длительность воспроизведения файла превышает макс. 40 с.<br>6 – файл защищен системой управления цифровыми правами.<br>7 – недопустимые параметры. | Результат операции в средстве выбора. |

**Пример: Сохранить локальный музыкальных файлов как мелодию звонка**

``` cs
LauncherOptions options = new LauncherOptions();
options.TargetApplicationPackageFamilyName = "Microsoft.Tonepicker_8wekyb3d8bbwe";

ValueSet inputData = new ValueSet() {
    { "Action", "SaveRingtone" },
    { "ToneFileSharingToken", SharedStorageAccessManager.AddFile(myLocalFile) }
};

LaunchUriResult result = await Launcher.LaunchUriForResultsAsync(new Uri("ms-tonepicker:"), options, inputData);

if (result.Status == LaunchUriStatus.Success)
{
     Int32 resultCode = (Int32)result.Result["Result"];

     if (resultCode == 0)
     {
         // no issues
     }
     else
     {
          switch (resultCode)
          {
             case 2:
              // The specified file was invalid
              break;
              case 3:
              // The specified file's content type is invalid
              break;
              case 4:
              // The specified file was too big
              break;
              case 5:
              // The specified file was too long
              break;
              case 6:
              // The file was protected by DRM
              break;
              case 7:
              // The specified parameter was incorrect
              break;
          }
      }
 }
```

## <a name="task-convert-a-tone-token-to-its-friendly-name"></a>Задача. Преобразование тон маркер в его понятное имя

Ниже перечислены аргументы, которые можно передавать для получения понятного имени звукового сигнала:

| Параметр | Тип | Обязательно | Возможные значения | Описание |
|-----------|------|----------|-------|-------------|
| Действие | строка | Да | "GetToneName" | Указывает, что требуется получить понятное имя звукового сигнала. |
| ToneToken | строка | Да | Маркер звукового сигнала | Маркер звукового сигнала, для которого требуется получить отображаемое имя. |

Значения, возвращаемые в [LaunchUriResults.Result](https://docs.microsoft.com/uwp/api/windows.system.launchuriresult.result):

| Возвращаемое значение | Тип | Возможные значения | Описание |
|--------------|------|-------|-------------|
| Результат | Int32 | 0 – операция средства выбора выполнена успешно.<br>7 – неправильный параметр (например, не указан ToneToken).<br>9 – ошибка чтения имени для указанного маркера.<br>10 – не удалось найти указанный маркер звукового сигнала. | Результат операции в средстве выбора.
| DisplayName | строка | Понятное имя звукового сигнала. | Возвращает отображаемое имя выбранного звукового сигнала. Этот параметр возвращается в ValueSet только в случае, если **Result** равен 0. |

**Пример: Получить маркер тон из Contact.RingToneToken и отобразить его понятное имя в карточку контакта.**

```cs
using (var connection = new AppServiceConnection())
{
    connection.AppServiceName = "ms-tonepicker-nameprovider";
    connection.PackageFamilyName = "Microsoft.Tonepicker_8wekyb3d8bbwe";
    AppServiceConnectionStatus connectionStatus = await connection.OpenAsync();
    if (connectionStatus == AppServiceConnectionStatus.Success)
    {
        var message = new ValueSet() {
            { "Action", "GetToneName" },
            { "ToneToken", token)
        };
        AppServiceResponse response = await connection.SendMessageAsync(message);
        if (response.Status == AppServiceResponseStatus.Success)
        {
            Int32 resultCode = (Int32)response.Message["Result"];
            if (resultCode == 0)
            {
                string name = response.Message["DisplayName"] as string;
            }
            else
            {
                // handle failure
            }
        }
        else
        {
            // handle failure
        }
    }
}
```
