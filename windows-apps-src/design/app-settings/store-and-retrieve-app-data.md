---
Description: Сведения о хранении и извлечении локальных, перемещаемых и временных данных приложения.
title: Хранение и извлечение параметров и других данных приложений
ms.assetid: 41676A02-325A-455E-8565-C9EC0BC3A8FE
label: App settings and data
template: detail.hbs
ms.date: 11/14/2017
ms.topic: article
keywords: Windows 10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: 0eb7ef49d0ce1876635dc36e84f43432c13e1791
ms.sourcegitcommit: f561efbda5c1d47b85601d91d70d86c5332bbf8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690369"
---
# <a name="store-and-retrieve-settings-and-other-app-data"></a>Хранение и извлечение параметров и других данных приложений

*Данные приложения* — это изменяемые данные, создаваемые и управляемые конкретным приложением. Он включает состояние среды выполнения, параметры приложения, пользовательские настройки, справочное содержимое (например, определения словарей в словарном приложении) и другие параметры. Данные приложений отличаются от *пользовательских данных*, данных, создаваемых и управляемых пользователем при использовании приложения. К данным пользователей относятся файлы документов или мультимедиа, записи электронной почты или сообщения, а также записей базы данных, содержащих содержимое, созданное пользователем. Пользовательские данные могут быть полезными или осмысленными для более чем одного приложения. Часто это данные, которые пользователь хочет обрабатывать или передавать как сущность независимо от самого приложения, например документа.

**Важное замечание о данных приложения:** Время существования данных приложения связано со временем существования приложения. Если приложение удалено, все данные приложения будут потеряны. Не используйте данные приложений для хранения пользовательских данных или чего-либо, которые пользователи могут воспринимать как ценные и невосстановимых. Для хранения этих сведений рекомендуется использовать библиотеки пользователя и Microsoft OneDrive. Данные приложения идеально подходят для хранения пользовательских настроек, параметров и избранного приложения.

## <a name="types-of-app-data"></a>Типы данных приложений

Существует два типа данных приложения: параметры и файлы.

### <a name="settings"></a>Параметры

Используйте параметры для хранения пользовательских настроек и сведений о состоянии приложения. API данных приложений позволяет легко создавать и извлекать параметры (мы покажем некоторые примеры ниже в этой статье).

Ниже приведены типы данных, которые можно использовать для параметров приложения.

- **Uint8**, **Int16**, **UInt16**, **Int32**, **UInt32**, **Int64**, **UInt64**, **Single**, **Double**
- **Логический**
- **Char16**, **строка**
- [**DateTime**](/uwp/api/Windows.Foundation.DateTime), [ **TimeSpan**](/uwp/api/Windows.Foundation.TimeSpan)
    - Для C#/.NET используйте: [**System. DateTimeOffset**](/dotnet/api/system.datetimeoffset?view=dotnet-uwp-10.0), [**System. TimeSpan**](/dotnet/api/system.timespan?view=dotnet-uwp-10.0) .
- **GUID**, [**точка**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point), [**Размер**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Size), [**Rect**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Rect)
- [**Аппликатиондатакомпоситевалуе**](/uwp/api/Windows.Storage.ApplicationDataCompositeValue): набор связанных параметров приложения, которые должны быть сериализованы и десериализованы атомарным образом. Используйте составные параметры, чтобы легко управлять атомарными обновлениями взаимозависимых параметров. Система обеспечивает целостность составных параметров во время параллельного доступа и роуминга. Составные параметры оптимизированы для небольших объемов данных, и производительность может быть низкой, если они используются для больших наборов данных.

### <a name="files"></a>Файлы

Используйте файлы для хранения двоичных данных или для включения собственных настраиваемых сериализованных типов.

## <a name="storing-app-data-in-the-app-data-stores"></a>Хранение данных приложений в хранилищах данных приложений


При установке приложения система предоставляет ему собственные пользовательские хранилища данных для параметров и файлов. Вам не нужно знать, где и как эти данные существуют, поскольку система несет ответственность за управление физическим хранилищем, обеспечивая изолированность данных от других приложений и других пользователей. Система также сохраняет содержимое этих хранилищ данных, когда пользователь устанавливает обновление в приложение и удаляет содержимое этих хранилищ данных полностью и аккуратно при удалении приложения.

В хранилище данных приложений для каждого приложения определены системные корневые каталоги: один для локальных файлов, один для перемещаемых файлов и один для временных файлов. Приложение может добавлять новые файлы и новые контейнеры в каждый из этих корневых каталогов.

## <a name="local-app-data"></a>Данные локального приложения


Данные локального приложения следует использовать для любых сведений, которые необходимо сохранять между сеансами приложения и которые не подходят для перемещаемых данных приложений. Данные, которые не применяются на других устройствах, также должны храниться здесь. Общий размер хранимых локальных данных не ограничен. Используйте локальное хранилище данных приложений для данных, которые не имеют смысла перемещаться и для больших наборов данных.

### <a name="retrieve-the-local-app-data-store"></a>Получение хранилища данных локального приложения

Прежде чем можно будет читать или записывать данные локального приложения, необходимо получить локальное хранилище данных приложения. Чтобы получить локальное хранилище данных приложения, используйте свойство [**ApplicationData. LocalSettings**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localsettings) , чтобы получить локальные параметры приложения в виде объекта [**ApplicationDataContainer**](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationDataContainer) . Используйте свойство [**ApplicationData. локалфолдер**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localfolder) для получения файлов в объекте [**сторажефолдер**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder) . Используйте свойство [**ApplicationData. локалкачефолдер**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localcachefolder) , чтобы получить папку в локальном хранилище данных приложений, где можно сохранять файлы, не входящие в резервные копии и восстановление.

```CSharp
Windows.Storage.ApplicationDataContainer localSettings = 
    Windows.Storage.ApplicationData.Current.LocalSettings;
Windows.Storage.StorageFolder localFolder = 
    Windows.Storage.ApplicationData.Current.LocalFolder;
```

### <a name="create-and-retrieve-a-simple-local-setting"></a>Создание и получение простого локального параметра

Чтобы создать или записать параметр, используйте свойство [**ApplicationDataContainer. Values**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer.values) для доступа к параметрам в контейнере `localSettings`, полученном на предыдущем шаге. В этом примере создается параметр с именем `exampleSetting`.

```CSharp
// Simple setting

localSettings.Values["exampleSetting"] = "Hello Windows";
```

Чтобы получить этот параметр, используйте то же свойство [**ApplicationDataContainer. Values**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer.values) , которое использовалось для создания параметра. В этом примере показано, как получить только что созданный параметр.

```CSharp
// Simple setting
Object value = localSettings.Values["exampleSetting"];
```

### <a name="create-and-retrieve-a-local-composite-value"></a>Создание и получение локального составного значения

Чтобы создать или записать составное значение, создайте объект [**аппликатиондатакомпоситевалуе**](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationDataCompositeValue) . В этом примере создается составной параметр с именем `exampleCompositeSetting` и добавляется в контейнер `localSettings`.

```CSharp
// Composite setting

Windows.Storage.ApplicationDataCompositeValue composite = 
    new Windows.Storage.ApplicationDataCompositeValue();
composite["intVal"] = 1;
composite["strVal"] = "string";

localSettings.Values["exampleCompositeSetting"] = composite;
```

В этом примере показано, как получить только что созданное составное значение.

```CSharp
// Composite setting

Windows.Storage.ApplicationDataCompositeValue composite = 
   (Windows.Storage.ApplicationDataCompositeValue)localSettings.Values["exampleCompositeSetting"];

if (composite == null)
{
   // No data
}
else
{
   // Access data in composite["intVal"] and composite["strVal"]
}
```

### <a name="create-and-read-a-local-file"></a>Создание и чтение локального файла

Чтобы создать и обновить файл в локальном хранилище данных приложения, используйте API файлов, такие как [**Windows. Storage. сторажефолдер. CreateFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.createfileasync) и [**Windows. Storage. FileIO. вритетекстасинк**](https://docs.microsoft.com/uwp/api/windows.storage.fileio.writetextasync). В этом примере создается файл с именем `dataFile.txt` в контейнере `localFolder` и записывается текущая дата и время в файл. Значение **реплацеексистинг** из перечисления [**креатионколлисионоптион**](https://docs.microsoft.com/uwp/api/Windows.Storage.CreationCollisionOption) указывает на замену файла, если он уже существует.

```csharp
async void WriteTimestamp()
{
   Windows.Globalization.DateTimeFormatting.DateTimeFormatter formatter = 
       new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("longtime");

   StorageFile sampleFile = await localFolder.CreateFileAsync("dataFile.txt", 
       CreationCollisionOption.ReplaceExisting);
   await FileIO.WriteTextAsync(sampleFile, formatter.Format(DateTimeOffset.Now));
}
```

Чтобы открыть и прочитать файл в локальном хранилище данных приложения, используйте API-интерфейсы файлов, такие как [**Windows. Storage. сторажефолдер. GetFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfileasync), [**Windows. Storage. StorageFile. жетфилефромаппликатионуриасинк**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.getfilefromapplicationuriasync)и [**Windows. Storage. FileIO. метод** ](https://docs.microsoft.com/uwp/api/windows.storage.fileio.readtextasync). Этот пример открывает файл `dataFile.txt`, созданный на предыдущем шаге, и считывает дату из файла. Дополнительные сведения о загрузке файловых ресурсов из различных расположений см. [в разделе Загрузка файловых ресурсов](https://docs.microsoft.com/previous-versions/windows/apps/hh965322(v=win.10)).

```csharp
async void ReadTimestamp()
{
   try
   {
      StorageFile sampleFile = await localFolder.GetFileAsync("dataFile.txt");
      String timestamp = await FileIO.ReadTextAsync(sampleFile);
      // Data is contained in timestamp
   }
   catch (Exception)
   {
      // Timestamp not found
   }
}
```

## <a name="roaming-data"></a>Перемещаемые данные


При использовании перемещаемых данных в приложении пользователи могут без проблем синхронизировать данные приложения на нескольких устройствах. Если пользователь устанавливает приложение на нескольких устройствах, операционная система сохраняет синхронизацию данных приложения, уменьшая объем работы по установке, которую пользователь должен выполнить для вашего приложения на втором устройстве. Роуминг также позволяет пользователям продолжить задачу, например создание списка, с тем, что они остались даже на другом устройстве. Операционная система реплицирует перемещаемые данные в облако при обновлении и синхронизирует данные с другими устройствами, на которых установлено приложение.

Операционная система ограничивает размер данных приложения, которые может перемещать каждое приложение. См. раздел [**ApplicationData. роамингсторажекуота**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingstoragequota). Если приложение достигает этого предела, ни одно из данных приложений приложения не будет реплицировано в облако, пока все данные приложения не будут меньше этого предела. По этой причине рекомендуется использовать перемещаемые данные только для пользовательских предпочтений, ссылок и небольших файлов данных.

Перемещаемые данные для приложения доступны в облаке при условии, что пользователь обращается к нему с какого-либо устройства в течение требуемого интервала времени. Если пользователь не запускает приложение дольше этого интервала времени, его перемещаемые данные удаляются из облака. Если пользователь удаляет приложение, его перемещаемые данные не удаляются из облака автоматически. Если пользователь переустанавливает приложение в течение интервала времени, перемещаемые данные синхронизируются из облака.

### <a name="roaming-data-dos-and-donts"></a>Данные роуминга и их запреты

- Используйте роуминг для пользовательских настроек, ссылок и небольших файлов данных. Например, можно использовать роуминг, чтобы сохранить предпочтение цвета фона для всех устройств.
- Используйте роуминг, чтобы позволить пользователям продолжить задачу на устройствах. Например, перемещаем данные приложения, например содержимое черновика электронного письма или последней просмотренной страницы в приложении Reader.
- Обработка события с [**измененными**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.datachanged) данными путем обновления данных приложения. Это событие возникает, когда данные приложения только что завершили синхронизацию из облака.
- Перемещать ссылки на содержимое, а не на необработанные данные. Например, переместить URL-адрес, а не содержимое статьи в Интернете.
- Для важных, критических для времени параметров используйте параметр *хигхприорити* , связанный с [**роамингсеттингс**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingsettings).
- Не следует перемещать данные приложений, характерные для устройства. Некоторые сведения относятся только локально, например имя пути к локальному файлу ресурса. Если вы решите переместить локальную информацию, убедитесь, что приложение может восстановиться, если эти сведения не являются допустимыми на вторичном устройстве.
- Не следует перемещать большие наборы данных приложений. Существует ограничение на объем данных приложений, которые может перемещать приложение. чтобы получить это максимальное значение, используйте свойство [**роамингсторажекуота**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingstoragequota) . Если приложение достигает этого предела, данные не могут перемещаться, пока размер хранилища данных приложения не превысит предельное значение. При проектировании приложения подумайте о том, как разместить границу больших данных, чтобы не превысить ограничение. Например, если для сохранения состояния игры требуется 10 КБ, приложение может хранить не более 10 игр.
- Не используйте роуминг для данных, которые используют мгновенную синхронизацию. Windows не гарантирует мгновенную синхронизацию; Роуминг может значительно откладываться, если пользователь находится в автономном режиме или в сети с высокой задержкой. Убедитесь, что пользовательский интерфейс не зависит от мгновенной синхронизации.
- Не используйте роуминг для часто изменяющихся данных. Например, если приложение отслеживает часто изменяющиеся сведения, такие как расположение в песне, в секунду, не храните его в качестве перемещаемых данных приложения. Вместо этого выберите менее частное представление, которое по-прежнему обеспечивает хорошее взаимодействие с пользователем, например воспроизводимая песня.

### <a name="roaming-pre-requisites"></a>Предварительные требования для роуминга

Любой пользователь может воспользоваться преимуществами перемещаемых данных приложений, если они используют учетная запись Майкрософт для входа на устройство. Однако пользователи и администраторы групповой политики могут в любое время отключать перемещаемые данные приложений на устройстве. Если пользователь решил не использовать учетная запись Майкрософт или отключать возможности перемещения данных, он по-прежнему сможет использовать ваше приложение, но данные приложения будут локальными для каждого устройства.

Данные, хранящиеся в [**PasswordVault**](https://docs.microsoft.com/uwp/api/Windows.Security.Credentials.PasswordVault) , будут переходить только в том случае, если пользователь сделал устройство доверенным. Если устройство не является доверенным, данные, защищенные в этом хранилище, не будут перемещаться.

### <a name="conflict-resolution"></a>Разрешение конфликтов

Перемещаемые данные приложения не предназначены для одновременного использования на нескольких устройствах одновременно. Если во время синхронизации возникает конфликт, поскольку определенная единица данных была изменена на двух устройствах, система всегда будет предпочитать значение, которое было записано последним. Это гарантирует, что приложение будет использовать самые последние сведения. Если единица данных является составной, разрешение конфликтов все равно будет происходить на уровне единицы параметра, что означает, что совмещенный с последним изменением будет синхронизирован.

### <a name="when-to-write-data"></a>Время записи данных

В зависимости от ожидаемого времени существования параметра данные должны записываться в разное время. Нечастое или медленно изменяющееся данные приложения следует записывать немедленно. Однако данные приложения, которые часто меняются, должны периодически записываться с регулярными интервалами (например, каждые 5 минут), а также при приостановке приложения. Например, музыкальное приложение может записывать параметры «текущая песня» при начале воспроизведения новой песни, однако фактическое расположение в песне должно быть написано только при приостановке.

### <a name="excessive-usage-protection"></a>Чрезмерная защита использования

Система использует различные механизмы защиты, чтобы избежать неправильного использования ресурсов. Если данные приложения не переходят ожидаемым образом, скорее всего, устройство временно ограничено. Ожидание в течение некоторого времени обычно приводит к автоматическому устранению этой ситуации, и никаких действий не требуется.

### <a name="versioning"></a>Управление версиями

Данные приложения могут использовать управление версиями для обновления одной структуры данных до другой. Номер версии отличается от версии приложения и может быть установлен в. Хотя это и не применяется, настоятельно рекомендуется использовать более высокие номера версий, так как при попытке перехода на более низкий номер версии данных, представляющий новые данные, могут возникать нежелательные сложности (включая потери данных).

Данные приложений перемещаются только между установленными приложениями с одинаковым номером версии. Например, устройства в версии 2 будут переносить данные между собой, а устройства в версии 3 будут выполнять те же действия, но роуминг между устройством, работающим под управлением версии 2, и устройством, работающим под управлением версии 3, будет невозможен. При установке нового приложения, использующего различные номера версий на других устройствах, вновь установленное приложение будет синхронизировать данные приложения, связанные с самым высоким номером версии.

### <a name="testing-and-tools"></a>Тестирование и средства

Разработчики могут заблокировать устройство, чтобы активировать синхронизацию перемещаемых данных приложений. Если кажется, что данные приложения не переходят в течение определенного промежутка времени, проверьте следующие элементы и убедитесь, что:

- Перемещаемые данные не превышают максимальный размер (Дополнительные сведения см. в [**роамингсторажекуота**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingstoragequota) ).
- Ваши файлы закрываются и освобождаются должным образом.
- Существует по меньшей мере два устройства с одинаковой версией приложения.


### <a name="register-to-receive-notification-when-roaming-data-changes"></a>Регистрация для получения уведомлений при изменении перемещаемых данных

Чтобы использовать перемещаемые данные приложения, необходимо зарегистрироваться для изменения перемещаемых данных и получить перемещаемые контейнеры данных, чтобы можно было читать и записывать параметры.

1.  Зарегистрируйтесь, чтобы получить уведомление при изменении перемещаемых данных.

    Событие с [**измененными**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.datachanged) данными уведомляет вас при изменении перемещаемых данных. В этом примере задается `DataChangeHandler` в качестве обработчика для изменения перемещаемых данных.

```csharp
void InitHandlers()
    {
       Windows.Storage.ApplicationData.Current.DataChanged += 
          new TypedEventHandler<ApplicationData, object>(DataChangeHandler);
    }

    void DataChangeHandler(Windows.Storage.ApplicationData appData, object o)
    {
       // TODO: Refresh your data
    }
```

2.  Получите контейнеры для параметров и файлов приложения.

    Используйте свойство [**ApplicationData. роамингсеттингс**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingsettings) , чтобы получить параметры и свойство [**ApplicationData. роамингфолдер**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingfolder) для получения файлов.

```csharp
Windows.Storage.ApplicationDataContainer roamingSettings = 
        Windows.Storage.ApplicationData.Current.RoamingSettings;
    Windows.Storage.StorageFolder roamingFolder = 
        Windows.Storage.ApplicationData.Current.RoamingFolder;
```

### <a name="create-and-retrieve-roaming-settings"></a>Создание и получение параметров роуминга

Используйте свойство [**ApplicationDataContainer. Values**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer.values) для доступа к параметрам в контейнере `roamingSettings`, который мы приступили в предыдущем разделе. В этом примере создается простой параметр с именем `exampleSetting` и составное значение с именем `composite`.

```csharp
// Simple setting

roamingSettings.Values["exampleSetting"] = "Hello World";
// High Priority setting, for example, last page position in book reader app
roamingSettings.values["HighPriority"] = "65";

// Composite setting

Windows.Storage.ApplicationDataCompositeValue composite = 
    new Windows.Storage.ApplicationDataCompositeValue();
composite["intVal"] = 1;
composite["strVal"] = "string";

roamingSettings.Values["exampleCompositeSetting"] = composite;

```

В этом примере извлекаются только что созданные параметры.

```csharp
// Simple setting

Object value = roamingSettings.Values["exampleSetting"];

// Composite setting

Windows.Storage.ApplicationDataCompositeValue composite = 
   (Windows.Storage.ApplicationDataCompositeValue)roamingSettings.Values["exampleCompositeSetting"];

if (composite == null)
{
   // No data
}
else
{
   // Access data in composite["intVal"] and composite["strVal"]
}
```

### <a name="create-and-retrieve-roaming-files"></a>Создание и получение перемещаемых файлов

Чтобы создать и обновить файл в хранилище данных перемещаемых приложений, используйте API файлов, такие как [**Windows. Storage. сторажефолдер. CreateFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.createfileasync) и [**Windows. Storage. FileIO. вритетекстасинк**](https://docs.microsoft.com/uwp/api/windows.storage.fileio.writetextasync). В этом примере создается файл с именем `dataFile.txt` в контейнере `roamingFolder` и записывается текущая дата и время в файл. Значение **реплацеексистинг** из перечисления [**креатионколлисионоптион**](https://docs.microsoft.com/uwp/api/Windows.Storage.CreationCollisionOption) указывает на замену файла, если он уже существует.

```csharp
async void WriteTimestamp()
{
   Windows.Globalization.DateTimeFormatting.DateTimeFormatter formatter = 
       new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("longtime");

   StorageFile sampleFile = await roamingFolder.CreateFileAsync("dataFile.txt", 
       CreationCollisionOption.ReplaceExisting);
   await FileIO.WriteTextAsync(sampleFile, formatter.Format(DateTimeOffset.Now));
}
```

Чтобы открыть и прочитать файл в хранилище данных перемещаемых приложений, используйте API-интерфейсы файлов, такие как [**Windows. Storage. сторажефолдер. GetFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfileasync), [**Windows. Storage. StorageFile. жетфилефромаппликатионуриасинк**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.getfilefromapplicationuriasync)и [ **Windows. Storage. FileIO. метод**](https://docs.microsoft.com/uwp/api/windows.storage.fileio.readtextasync). Этот пример открывает файл `dataFile.txt`, созданный в предыдущем разделе, и считывает дату из файла. Дополнительные сведения о загрузке файловых ресурсов из различных расположений см. [в разделе Загрузка файловых ресурсов](https://docs.microsoft.com/previous-versions/windows/apps/hh965322(v=win.10)).

```csharp
async void ReadTimestamp()
{
   try
   {
      StorageFile sampleFile = await roamingFolder.GetFileAsync("dataFile.txt");
      String timestamp = await FileIO.ReadTextAsync(sampleFile);
      // Data is contained in timestamp
   }
   catch (Exception)
   {
      // Timestamp not found
   }
}
```


## <a name="temporary-app-data"></a>Временные данные приложения


Временное хранилище данных приложений работает как кэш. Его файлы не перемещаются и могут быть удалены в любое время. Задача обслуживания системы может автоматически удалять данные, хранящиеся в этом расположении, в любое время. Пользователь также может очищать файлы из временного хранилища данных с помощью средства очистки диска. Временные данные приложения можно использовать для хранения временных данных во время сеанса приложения. Нет никакой гарантии, что эти данные будут сохраняться за пределами сеанса приложения, так как система может освободить используемое пространство при необходимости. Расположение доступно через свойство [**темпорарифолдер**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.temporaryfolder) .

### <a name="retrieve-the-temporary-data-container"></a>Получение временного контейнера данных

Чтобы получить файлы, используйте свойство [**ApplicationData. темпорарифолдер**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.temporaryfolder) . Следующие шаги используют переменную `temporaryFolder` на этом шаге.

```csharp
Windows.Storage.StorageFolder temporaryFolder = ApplicationData.Current.TemporaryFolder;
```

### <a name="create-and-read-temporary-files"></a>Создание и чтение временных файлов

Чтобы создать и обновить файл во временном хранилище данных приложения, используйте API файлов, такие как [**Windows. Storage. сторажефолдер. CreateFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.createfileasync) и [**Windows. Storage. FileIO. вритетекстасинк**](https://docs.microsoft.com/uwp/api/windows.storage.fileio.writetextasync). В этом примере создается файл с именем `dataFile.txt` в контейнере `temporaryFolder` и записывается текущая дата и время в файл. Значение **реплацеексистинг** из перечисления [**креатионколлисионоптион**](https://docs.microsoft.com/uwp/api/Windows.Storage.CreationCollisionOption) указывает на замену файла, если он уже существует.


```csharp
async void WriteTimestamp()
{
   Windows.Globalization.DateTimeFormatting.DateTimeFormatter formatter = 
       new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("longtime");

   StorageFile sampleFile = await temporaryFolder.CreateFileAsync("dataFile.txt", 
       CreateCollisionOption.ReplaceExisting);
   await FileIO.WriteTextAsync(sampleFile, formatter.Format(DateTimeOffset.Now));
}
```

Чтобы открыть и прочитать файл во временном хранилище данных приложения, используйте API-интерфейсы файлов, такие как [**Windows. Storage. сторажефолдер. GetFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfileasync), [**Windows. Storage. StorageFile. жетфилефромаппликатионуриасинк**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.getfilefromapplicationuriasync)и [ **Windows. Storage. FileIO. метод**](https://docs.microsoft.com/uwp/api/windows.storage.fileio.readtextasync). Этот пример открывает файл `dataFile.txt`, созданный на предыдущем шаге, и считывает дату из файла. Дополнительные сведения о загрузке файловых ресурсов из различных расположений см. [в разделе Загрузка файловых ресурсов](https://docs.microsoft.com/previous-versions/windows/apps/hh965322(v=win.10)).

```csharp
async void ReadTimestamp()
{
   try
   {
      StorageFile sampleFile = await temporaryFolder.GetFileAsync("dataFile.txt");
      String timestamp = await FileIO.ReadTextAsync(sampleFile);
      // Data is contained in timestamp
   }
   catch (Exception)
   {
      // Timestamp not found
   }
}
```

## <a name="organize-app-data-with-containers"></a>Организация данных приложения с помощью контейнеров


Чтобы упростить организацию параметров и файлов данных приложения, создайте контейнеры (представленные объектами [**ApplicationDataContainer**](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationDataContainer) ) вместо работы с каталогами напрямую. Контейнеры можно добавлять в локальные, перемещаемые и временные хранилища данных приложений. Контейнеры могут быть вложены в глубину до 32 уровней.

Чтобы создать контейнер параметров, вызовите метод [**ApplicationDataContainer. CreateContainer**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer.createcontainer) . В этом примере создается контейнер локальных параметров с именем `exampleContainer` и добавляется параметр с именем `exampleSetting`. Значение **Always** из перечисления [**аппликатиондатакреатедиспоситион**](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationDataCreateDisposition) указывает, что контейнер создается, если он еще не существует.

```csharp
Windows.Storage.ApplicationDataContainer localSettings = 
    Windows.Storage.ApplicationData.Current.LocalSettings;
Windows.Storage.StorageFolder localFolder = 
    Windows.Storage.ApplicationData.Current.LocalFolder;

// Setting in a container
Windows.Storage.ApplicationDataContainer container = 
   localSettings.CreateContainer("exampleContainer", Windows.Storage.ApplicationDataCreateDisposition.Always);

if (localSettings.Containers.ContainsKey("exampleContainer"))
{
   localSettings.Containers["exampleContainer"].Values["exampleSetting"] = "Hello Windows";
}
```

## <a name="delete-app-settings-and-containers"></a>Удаление параметров и контейнеров приложения


Чтобы удалить простой параметр, который больше не нужен приложению, используйте метод [**аппликатиондатаконтаинерсеттингс. Remove**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainersettings.remove) . В этом примере делетессе `exampleSetting` локальный параметр, созданный ранее.

```csharp
Windows.Storage.ApplicationDataContainer localSettings = 
    Windows.Storage.ApplicationData.Current.LocalSettings;
Windows.Storage.StorageFolder localFolder = 
    Windows.Storage.ApplicationData.Current.LocalFolder;

// Delete simple setting

localSettings.Values.Remove("exampleSetting");
```

Чтобы удалить составной параметр, используйте метод [**аппликатиондатакомпоситевалуе. Remove**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacompositevalue.remove) . В этом примере удаляется локальный `exampleCompositeSetting` составной параметр, созданный в предыдущем примере.

```csharp
Windows.Storage.ApplicationDataContainer localSettings = 
    Windows.Storage.ApplicationData.Current.LocalSettings;
Windows.Storage.StorageFolder localFolder = 
    Windows.Storage.ApplicationData.Current.LocalFolder;

// Delete composite setting

localSettings.Values.Remove("exampleCompositeSetting");
```

Чтобы удалить контейнер, вызовите метод [**ApplicationDataContainer. DeleteContainer**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer.deletecontainer) . В этом примере удаляется созданный ранее контейнер локальных параметров `exampleContainer`.

```csharp
Windows.Storage.ApplicationDataContainer localSettings = 
    Windows.Storage.ApplicationData.Current.LocalSettings;
Windows.Storage.StorageFolder localFolder = 
    Windows.Storage.ApplicationData.Current.LocalFolder;

// Delete container

localSettings.DeleteContainer("exampleContainer");
```

## <a name="versioning-your-app-data"></a>Управление версиями данных приложения


При необходимости можно выбрать версию данных приложения для приложения. Это позволит создать будущую версию приложения, которая изменяет формат данных приложения, не вызывая проблем совместимости с предыдущей версией приложения. Приложение проверяет версию данных приложения в хранилище данных, и, если версия меньше, чем версия приложения, приложение должно обновить данные приложения до нового формата и обновить версию. Дополнительные сведения см. в описании свойства[**Application. Version**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.version) и метода [**ApplicationData. сетверсионасинк**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.setversionasync) .

## <a name="related-articles"></a>Связанные статьи

* [**Windows. Storage. ApplicationData**](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationData)
* [**Windows. Storage. ApplicationData. Роамингсеттингс**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingsettings)
* [**Windows. Storage. ApplicationData. Роамингфолдер**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingfolder)
* [**Windows. Storage. ApplicationData. Роамингсторажекуота**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingstoragequota)
* [**Windows. Storage. Аппликатиондатакомпоситевалуе**](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationDataCompositeValue)
