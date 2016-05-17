---
author: mijacobs
Description: Информация о хранении и извлечении локальных, перемещаемых и временных данных приложений.
title: Хранение и извлечение параметров и прочих данных приложения
ms.assetid: 41676A02-325A-455E-8565-C9EC0BC3A8FE
label: App settings and data
template: detail.hbs
---

# Хранение и извлечение параметров и прочих данных приложения





*Данные приложения* — это изменяемые данные, присущие определенному приложению. К ним относится состояние среды выполнения, предпочтения пользователей и другие параметры. Данные приложения – это не то же самое, что *данные пользователя*, данные, которые пользователь создает и которыми управляет при использовании приложения. К данным пользователя относятся файлы документов или мультимедиа, текст сообщений электронной почты или записи разговоров, а также записи баз данных, содержимое которых создано пользователем. Данные пользователя могут быть полезными или имеющими смысл для нескольких приложений. Часто это данные, которые пользователь меняет или передает как объект, не зависящий от самого приложения, например документ.

**Важное замечание о данных приложения.  **Время существования данных приложения привязано ко времени существования приложения. Если приложение удалить, все его данные будут потеряны. Не используйте данные приложения для хранения пользовательских данных или чего-либо ценного и незаменимого с точки зрения пользователей. Рекомендуется сохранять информацию такого рода в библиотеках пользователя и Microsoft OneDrive. Данные приложения идеально подходят для хранения пользовательских настроек, параметров и избранного для приложения.

## <span id="Types_of_app_data"></span><span id="types_of_app_data"></span><span id="TYPES_OF_APP_DATA"></span>Типы данных приложений


Существует два типа данных приложений: параметры и файлы.

-   **Параметры**

    Параметры используются для сохранения настроек пользователя и данных о состоянии приложения. API данных приложения позволяет с легкостью создавать и восстанавливать параметры (мы покажем вам несколько примеров позже в этой статье).

    В качестве параметров приложения можно использовать следующие типы данных.

    -   **UInt8**, **Int16**, **UInt16**, **Int32**, **UInt32**, **Int64**, **UInt64**, **Single**, **Double**
    -   **Boolean**
    -   **Char16**, **String**
    -   [
              **DateTime**
            ](https://msdn.microsoft.com/library/windows/apps/br206576), [**TimeSpan**](https://msdn.microsoft.com/library/windows/apps/br225996)
    -   **GUID**, [**Point**](https://msdn.microsoft.com/library/windows/apps/br225870), [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995), [**Rect**](https://msdn.microsoft.com/library/windows/apps/br225994)
    -   [
              **ApplicationDataCompositeValue**
            ](https://msdn.microsoft.com/library/windows/apps/br241588): набор связанных параметров приложения, которые необходимо сериализовать и десериализовать атомарным образом. Чтобы с легкостью управлять обновлениями взаимозависимых параметров, выполняемыми как одна неделимая операция, используйте композитные параметры. Система обеспечивает целостность композитных параметров при параллельном доступе и перемещении. Композитные параметры оптимизированы для малых объемов данных и могут привести к значительному снижению производительности, если использовать их для крупных наборов данных.
-   **Файлы**

    Файлы используют для хранения двоичных данных либо для включения собственных настраиваемых сериализованных типов.

## <span id="Storing_app_data_in_the_app_data_stores"></span><span id="storing_app_data_in_the_app_data_stores"></span><span id="STORING_APP_DATA_IN_THE_APP_DATA_STORES"></span>Хранение данных приложения в хранилищах данных приложения


При установке приложения система выделяет для хранения параметров и файлов отдельные хранилища по количеству пользователей приложения. Вам не нужно знать, где или как хранятся эти данные, поскольку что система отвечает за управление физическим хранилищем, обеспечивая хранение этих данных изолированно от других приложений и других пользователей. Система также сохраняет содержимое этих хранилищ данных в случае установки и обновления приложения пользователем, а также полностью удаляет это содержимое в случае удаления приложения.

В хранилище данных приложения каждое приложение имеет определяемые системой корневые каталоги — по одному для локальных, перемещаемых и временных файлов. Ваше приложение может добавлять новые файлы и новые контейнеры в каждый из корневых каталогов.

## <span id="Local_app_data"></span><span id="local_app_data"></span><span id="LOCAL_APP_DATA"></span>Локальные данные приложения


В локальные данные приложения следует помещать информацию, которую нужно сохранить между сеансами приложения, но которая не подходит для перемещаемых данных приложения. Здесь также следует хранить данные, которые не применимы на других устройствах. Общего ограничения по размеру сохраненных локальных данных не существует. Используйте локальное хранилище данных приложения для данных, которые не имеет смысла перемещать, и для крупных наборов данных.

### <span id="Retrieve_the_local_app_data_store"></span><span id="retrieve_the_local_app_data_store"></span><span id="RETRIEVE_THE_LOCAL_APP_DATA_STORE"></span>Извлечение локального хранилища данных приложения

Прежде чем получить возможность чтения или записи локальных данных приложения, необходимо извлечь локальное хранилище данных приложения. Для извлечения локального хранилища данных приложения используйте свойство [**ApplicationData.LocalSettings**](https://msdn.microsoft.com/library/windows/apps/br241622), чтобы получить локальные параметры приложения в виде объекта [**ApplicationDataContainer**](https://msdn.microsoft.com/library/windows/apps/br241599). Используйте свойство [**ApplicationData.LocalFolder**](https://msdn.microsoft.com/library/windows/apps/br241621) для загрузки файлов в объект [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230). Используйте свойство [**ApplicationData.LocalCacheFolder**](https://msdn.microsoft.com/library/windows/apps/dn633825), чтобы получить папку в локальном хранилище данных приложения, в которой можно сохранить файлы, которые не включены в резервную копию и копию для восстановления.

```CSharp
Windows.Storage.ApplicationDataContainer localSettings = 
    Windows.Storage.ApplicationData.Current.LocalSettings;
Windows.Storage.StorageFolder localFolder = 
    Windows.Storage.ApplicationData.Current.LocalFolder;
```

### <span id="Create_and_retrieve_a_simple_local_setting"></span><span id="create_and_retrieve_a_simple_local_setting"></span><span id="CREATE_AND_RETRIEVE_A_SIMPLE_LOCAL_SETTING"></span>Создание и извлечение простого локального параметра

Для создания или записи параметра используйте свойство [**ApplicationDataContainer.Values**](https://msdn.microsoft.com/library/windows/apps/br241615) для получения доступа к параметрам в контейнере `localSettings`, полученном на предыдущем этапе. В следующем примере создается параметр с именем

```CSharp
// Simple setting

localSettings.Values["exampleSetting"] = "Hello Windows";
```

Для извлечения параметра используйте то же свойство [**ApplicationDataContainer.Values**](https://msdn.microsoft.com/library/windows/apps/br241615), которое использовалось для создания этого параметра. В этом примере показано, как извлечь только что созданный параметр.

```CSharp
// Simple setting
Object value = localSettings.Values["exampleSetting"];
```

### <span id="Create_and_retrieve_a_local_composite_value"></span><span id="create_and_retrieve_a_local_composite_value"></span><span id="CREATE_AND_RETRIEVE_A_LOCAL_COMPOSITE_VALUE"></span>Создание и извлечение локального составного значения

Для создания или записи составного значения создайте объект [**ApplicationDataCompositeValue**](https://msdn.microsoft.com/library/windows/apps/br241588). В следующем примере составной параметр с именем `exampleCompositeSetting` создается и добавляется в контейнер `localSettings`.

```CSharp
// Composite setting

Windows.Storage.ApplicationDataCompositeValue composite = 
    new Windows.Storage.ApplicationDataCompositeValue();
composite["intVal"] = 1;
composite["strVal"] = "string";

localSettings.Values["exampleCompositeSetting"] = composite;
```

В этом примере показано, как извлечь только что созданное составное значение.

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

### <span id="Create_and_read_a_local_file"></span><span id="create_and_read_a_local_file"></span><span id="CREATE_AND_READ_A_LOCAL_FILE"></span>Создание и чтение локального файла

Для создания и обновления файла в локальном хранилище данных приложения используйте файловые API, например, [**Windows.Storage.StorageFolder.CreateFileAsync**](https://msdn.microsoft.com/library/windows/apps/br227249) и [**Windows.Storage.FileIO.WriteTextAsync**](https://msdn.microsoft.com/library/windows/apps/hh701505). В следующем примере создается файл с именем `dataFile.txt` в контейнере `localFolder` и в этот файл записываются текущие дата и время. Значение **ReplaceExisting** из перечисления [**CreationCollisionOption**](https://msdn.microsoft.com/library/windows/apps/br241631) показывает, что, если файл уже существует, его следует заменить.

```CSharp
async void WriteTimestamp()
{
   Windows.Globalization.DateTimeFormatting.DateTimeFormatter formatter = 
       new Windows.Globalization.DatetimeFormatting.DateTimeFormatter("longtime");

   StorageFile sampleFile = await localFolder.CreateFileAsync("dataFile.txt", 
       CreateCollisionOption.ReplaceExisting);
   await FileIO.WriteTextAsync(sampleFile, formatter.Format(DateTime.Now));
}
```

Для открытия и чтения файла в локальном хранилище данных приложения используйте файловые API, например, [**Windows.Storage.StorageFolder.GetFileAsync**](https://msdn.microsoft.com/library/windows/apps/br227272), [**Windows.Storage.StorageFile.GetFileFromApplicationUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701741) и [**Windows.Storage.FileIO.ReadTextAsync**](https://msdn.microsoft.com/library/windows/apps/hh701482). В следующем примере открывается файл `dataFile.txt`, созданный на предыдущем шаге, и из него считывается дата. Подробные сведения о загрузке файловых ресурсов из различных расположений см. в разделе [Загрузка файловых ресурсов](https://msdn.microsoft.com/library/windows/apps/xaml/hh965322).

```CSharp
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

## <span id="Roaming_data"></span><span id="roaming_data"></span><span id="ROAMING_DATA"></span>Перемещение данных


Если в приложении используются перемещаемые данные, то пользователи с легкостью смогут синхронизировать данные приложения на нескольких устройствах. Если пользователь устанавливает приложение на несколько устройств, ОС следит за своевременной синхронизацией данных приложения, тем самым уменьшая объем работ по настройке приложения, которые пользователь должен выполнить на втором устройстве. Кроме того, использование перемещаемых данных позволяет пользователю продолжить выполнение задачи, например составление списка, с того самого места, на котором он остановился, даже если работа выполнялась на другом устройстве. Когда перемещаемые данные обновляются, ОС реплицирует их в облаке и синхронизирует данные с другими устройствами, на которых установлено приложение.

ОС ограничивает объем данных приложения, которые каждое приложение может отнести к перемещаемым. См. раздел [**ApplicationData.RoamingStorageQuota**](https://msdn.microsoft.com/library/windows/apps/br241625). Если приложение достигает указанного предельного значения, то никакие данные приложения не будут реплицироваться в облаке до тех пор, пока общий объем перемещаемых данных приложения снова не окажется меньше предельного значения. По этой причине рекомендуется использовать перемещаемые данные только для предпочтений пользователей, ссылок и небольших файлов данных.

Перемещаемые данные приложения доступны в облаке до тех пор, пока пользователь обращается к ним с каких-либо устройств в течение заданного интервала времени. Если пользователь не запускает приложение дольше этого временного интервала, то его перемещаемые данные удаляются из облака. Если пользователь удаляет приложение, его перемещаемые данные не удаляются из облака автоматически, они сохраняются. Если пользователь повторно устанавливает приложение в течение указанного временного интервала, то перемещаемые данные синхронизируются с данными, сохраненными в облаке.

### Рекомендации по перемещению данных

-   Используйте перемещение данных для пользовательских предпочтений и настроек, ссылок и небольших файлов данных. Например, используйте перемещаемые данные, чтобы сохранить настройку цвета фона на всех своих устройствах.
-   Используйте перемещение данных, чтобы предоставить пользователям возможность продолжать работу над задачей на разных устройствах. Например, перемещайте такие данные приложения, как содержимое черновика сообщения электронной почты или последнюю просматриваемую страницу в средстве просмотра.
-   Обрабатывайте событие [**DataChanged**](https://msdn.microsoft.com/library/windows/apps/br241620), обновляя данные приложения. Это событие возникает сразу после завершения синхронизации данных приложения из облака.
-   Перемещайте ссылки на содержимое вместо необработанных данных. Например, перемещайте URL-адрес вместо содержимого статьи в сети.
-   Для важных зависящих от времени настроек используйте параметр *HighPriority*, связанный с [**RoamingSettings**](https://msdn.microsoft.com/library/windows/apps/br241624).
-   Не перемещайте данные приложения, относящиеся к устройству. Некоторая информация, например путь к локальному файловому ресурсу, имеет смысл только на локальном уровне. Если вы перемещаете локальные данные, убедитесь, что приложение может проверять, является ли эта информация действительной на втором устройстве.
-   Не перемещайте большие наборы данных приложения. Существует предел объема данных, который может перемещать приложение; значение этого максимума можно получить с помощью свойства [**RoamingStorageQuota**](https://msdn.microsoft.com/library/windows/apps/br241625). Если в приложении достигается этот предел, перемещение данных становится невозможным до тех пор, пока объем данных в хранилище приложения не станет меньше предельного. При проектировании приложения рекомендуется рассмотреть способ установки ограничения для данных большего размера, чтобы не превышать заданный предел. Например, если для каждого сохранения состояния игры требуется 10 КБ, приложение может позволить пользователю выполнять сохранение не более 10 раз.
-   Не используйте перемещение для данных, которые нуждаются в мгновенной синхронизации. Windows не гарантирует мгновенную синхронизацию. Перемещение может существенно задерживаться, если пользователь вышел из сети или используется сеть с высокой задержкой. Убедитесь, что ваш пользовательский интерфейс не зависит от мгновенной синхронизации.
-   Не используйте перемещение для часто изменяющихся данных. Например, если ваше приложение отслеживает часто меняющуюся информацию, такую как позиция при воспроизведении композиции, не сохраняйте эти данные как перемещаемые. Вместо этого выберите реже меняющееся представление, которое при этом обеспечивает хорошее взаимодействие, например представление текущей композиции.

### <span id="Roaming_pre-requisites"></span><span id="roaming_pre-requisites"></span><span id="ROAMING_PRE-REQUISITES"></span>Необходимые условия для перемещения

Любой пользователь может воспользоваться преимуществами перемещения данных приложения, если он использует учетную запись Майкрософт для входа на свое устройство. Однако пользователи и администраторы групповой политики могут в любое время отключить перемещение данных приложения на устройстве. Если пользователь принимает решение не использовать учетную запись Майкрософт или отключает возможности перемещения данных, он по-прежнему сможет использовать данные вашего приложения, но данные приложения будут локальными на каждом устройстве.

Данные, сохраненные в [**PasswordVault**](https://msdn.microsoft.com/library/windows/apps/br227081), перемещаются только в том случае, если пользователь присвоил своему устройству статус доверенного. Если устройство не является доверенным, данные, сохраненные в этом хранилище, не будут перемещаться.

### <span id="Conflict_resolution"></span><span id="conflict_resolution"></span><span id="CONFLICT_RESOLUTION"></span>Устранение конфликтов

Перемещаемые данные приложения не предназначены для использования на нескольких устройствах одновременно. Если во время синхронизации возникает конфликт из-за изменения конкретного блока данных на двух устройствах, система всегда будет выбирать значение, которое было записано последним. Это гарантирует использование самой новой информации. Если блок данных представляет собой составной параметр, то разрешение конфликтов все равно будет выполняться на уровне единиц параметра, то есть будет синхронизироваться составной параметр с последним изменением.

### <span id="When_to_write_data"></span><span id="when_to_write_data"></span><span id="WHEN_TO_WRITE_DATA"></span>Когда следует записывать данные

В зависимости от ожидаемой частоты изменения параметра данные следует записывать в разное время. Редко меняющиеся данные приложения следует записывать немедленно. Часто меняющиеся данные следует записывать периодически через регулярные интервалы (например, раз в 5 минут), а также при приостановке приложения. Например, музыкальное приложение может записывать параметр "текущая композиция" в начале воспроизведения новой композиции, а фактическое положение в песне следует записывать только при приостановке.

### <span id="Excessive_usage_protection"></span><span id="excessive_usage_protection"></span><span id="EXCESSIVE_USAGE_PROTECTION"></span>Защита от чрезмерного использования

У системы есть несколько механизмов для защиты от ненадлежащего использования ресурсов. Если ожидаемого перемещения данных не происходит, возможно, устройство временно ограничено. Через некоторое время ситуация разрешится сама собой, и никакие действия предпринимать не потребуется.

### <span id="Versioning"></span><span id="versioning"></span><span id="VERSIONING"></span>Управление версиями

Данные приложения могут использовать управление версиями для обновления с одной структуры данных до другой. Номер версии отличается от версии приложения и может быть установлен по желанию. Хотя это и не обязательно, мы настоятельно рекомендуем использовать только возрастающие номера версий, так как при переходе на версию с меньшим номером и новыми данными возможны нежелательные ситуации, включая потерю данных.

Данные приложения перемещаются только между установленными приложениями с одинаковым номером версии. Например, данные будут перемещаться между устройствами с приложениями версии 2 или между устройствами с приложениями версии 3, но перемещение данных между устройством с приложением версии 2 и устройством с приложением версии 3 выполняться не будет. Если вы устанавливаете новое приложение, которое использует разные номера версий на других устройствах, то новое установленное приложение будет выполнять синхронизацию данных приложения, связанных с самым высоким номером версии.

### <span id="Testing_and_tools"></span><span id="testing_and_tools"></span><span id="TESTING_AND_TOOLS"></span>Тестирование и инструменты

Разработчики могут заблокировать свое устройство, чтобы запустить синхронизацию перемещаемых данных приложения. Если создается впечатление, что данные приложения не перемещаются в течение определенного времени, убедитесь в следующем:

-   размер перемещаемых данных не превышает установленный предел (дополнительную информацию см. в разделе [**RoamingStorageQuota**](https://msdn.microsoft.com/library/windows/apps/br241625));
-   ваши файлы закрыты и выпущены надлежащим образом;
-   есть минимум два устройства с приложениями одной и той же версии.


### <span id="Register_to_receive_notification_when_roaming_data_changes"></span><span id="register_to_receive_notification_when_roaming_data_changes"></span><span id="REGISTER_TO_RECEIVE_NOTIFICATION_WHEN_ROAMING_DATA_CHANGES"></span>Регистрация для получения уведомлений об изменении перемещаемых данных

Для использования перемещаемых данных приложения необходимо включить функцию уведомления об изменениях перемещаемых данных и извлечь контейнеры перемещаемых данных, чтобы иметь возможность читать и записывать параметры.

1.  Регистрация для получения уведомлений об изменении перемещаемых данных.

    Событие [**DataChanged**](https://msdn.microsoft.com/library/windows/apps/br241620) уведомляет об изменении перемещаемых данных. В следующем примере `DataChangeHandler` определяется как обработчик изменений перемещаемых данных.

```    CSharp
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

2.  Получение контейнеров для параметров и файлов приложения.

    Используйте свойство [**ApplicationData.RoamingSettings**](https://msdn.microsoft.com/library/windows/apps/br241624) для получения параметров, а свойство [**ApplicationData.RoamingFolder**](https://msdn.microsoft.com/library/windows/apps/br241623) для получения файлов.

```    CSharp
Windows.Storage.ApplicationDataContainer roamingSettings = 
        Windows.Storage.ApplicationData.Current.RoamingSettings;
    Windows.Storage.StorageFolder roamingFolder = 
        Windows.Storage.ApplicationData.Current.RoamingFolder;
```

### <span id="Create_and_retrieve_roaming_settings"></span><span id="create_and_retrieve_roaming_settings"></span><span id="CREATE_AND_RETRIEVE_ROAMING_SETTINGS"></span>Создание и извлечение параметров перемещения

Используйте свойство [**ApplicationDataContainer.Values**](https://msdn.microsoft.com/library/windows/apps/br241615) для доступа к параметрам в контейнере `roamingSettings`, полученном в предыдущем разделе. В следующем примере создается простой параметр с именем `exampleSetting` и составное значение

```CSharp
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

В следующем примере извлекаются только что созданные параметры.

```CSharp
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

### <span id="Create_and_retrieve_roaming_files"></span><span id="create_and_retrieve_roaming_files"></span><span id="CREATE_AND_RETRIEVE_ROAMING_FILES"></span>Создание и восстановление перемещаемых файлов

Для создания и обновления файла в хранилище данных перемещаемого приложения используйте файловые API, например, [**Windows.Storage.StorageFolder.CreateFileAsync**](https://msdn.microsoft.com/library/windows/apps/br227249) и [**Windows.Storage.FileIO.WriteTextAsync**](https://msdn.microsoft.com/library/windows/apps/hh701505). В следующем примере создается файл с именем `dataFile.txt` в контейнере `roamingFolder` и в этот файл записываются текущие дата и время. Значение **ReplaceExisting** из перечисления [**CreationCollisionOption**](https://msdn.microsoft.com/library/windows/apps/br241631) показывает, что, если файл уже существует, его следует заменить.

```CSharp
async void WriteTimestamp()
{
   Windows.Globalization.DateTimeFormatting.DateTimeFormatter formatter = 
       new Windows.Globalization.DatetimeFormatting.DateTimeFormatter("longtime");

   StorageFile sampleFile = await roamingFolder.CreateFileAsync("dataFile.txt", 
       CreationCollisionOption.ReplaceExisting);
   await FileIO.WriteTextAsync(sampleFile, formatter.Format(DateTime.Now));
}
```

Для открытия и чтения файла в хранилище данных перемещаемого приложения используйте файловые API, например, [**Windows.Storage.StorageFolder.GetFileAsync**](https://msdn.microsoft.com/library/windows/apps/br227272), [**Windows.Storage.StorageFile.GetFileFromApplicationUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701741) и [**Windows.Storage.FileIO.ReadTextAsync**](https://msdn.microsoft.com/library/windows/apps/hh701482). В следующем примере открывается файл `dataFile.txt`, созданный в предыдущем разделе, и из него считывается дата. Подробные сведения о загрузке файловых ресурсов из различных расположений см. в разделе [Загрузка файловых ресурсов](https://msdn.microsoft.com/library/windows/apps/xaml/hh965322).

```CSharp
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


## <span id="Temporary_app_data"></span><span id="temporary_app_data"></span><span id="TEMPORARY_APP_DATA"></span>Временные данные приложения


Хранилище временных данных приложения действует подобно кэшу. Его файлы не перемещаются и могут быть удалены в любое время. Задача обслуживания системы может автоматически удалить данные, сохраненные в этом месте хранения в любое время. Кроме того, пользователь может удалить файлы из хранилища временных файлов с помощью средства очистки диска. Временные данные приложения могут использоваться для хранения временной информации в течение сеанса приложения. Нет никакой гарантии, что эти данные сохранятся после того, как сеанс приложения будет завершен, потому что система может при необходимости освободить занимаемое ими место. Местоположение определяется свойством [**temporaryFolder**](https://msdn.microsoft.com/library/windows/apps/br241629).

### <span id="Retrieve_the_temporary_data_container"></span><span id="retrieve_the_temporary_data_container"></span><span id="RETRIEVE_THE_TEMPORARY_DATA_CONTAINER"></span>Восстановление контейнера временных данных

Используйте свойство [**ApplicationData.TemporaryFolder**](https://msdn.microsoft.com/library/windows/apps/br241629) для получения файлов. Переменная `temporaryFolder` из этого шага используется на следующих шагах.

```CSharp
Windows.Storage.StorageFolder temporaryFolder = ApplicationData.Current.TemporaryFolder;</code></pre></td>
</tr>
</tbody>
</table>
```

### <span id="Create_and_read_temporary_files"></span><span id="create_and_read_temporary_files"></span><span id="CREATE_AND_READ_TEMPORARY_FILES"></span>Создание и чтение временных файлов

Для создания и обновления файла в хранилище временных данных приложения используйте файловые API, например, [**Windows.Storage.StorageFolder.CreateFileAsync**](https://msdn.microsoft.com/library/windows/apps/br227249) и [**Windows.Storage.FileIO.WriteTextAsync**](https://msdn.microsoft.com/library/windows/apps/hh701505). В следующем примере создается файл с именем `dataFile.txt` в контейнере `temporaryFolder` и в этот файл записываются текущие дата и время. Значение **ReplaceExisting** из перечисления [**CreationCollisionOption**](https://msdn.microsoft.com/library/windows/apps/br241631) показывает, что, если файл уже существует, его следует заменить.

<span codelanguage="CSharp"></span>
```CSharp
<colgroup>
<col width="100%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">C#</th>
</tr>
</thead>
<tbody>
<tr class="odd">
async void WriteTimestamp()
{
   Windows.Globalization.DateTimeFormatting.DateTimeFormatter formatter = 
       new Windows.Globalization.DatetimeFormatting.DateTimeFormatter("longtime");

   StorageFile sampleFile = await temporaryFolder.CreateFileAsync("dataFile.txt", 
       CreateCollisionOption.ReplaceExisting);
   await FileIO.WriteTextAsync(sampleFile, formatter.Format(DateTime.Now));
}
```

Для открытия и чтения файла в хранилище временных данных приложения используйте файловые API, например, [**Windows.Storage.StorageFolder.GetFileAsync**](https://msdn.microsoft.com/library/windows/apps/br227272), [**Windows.Storage.StorageFile.GetFileFromApplicationUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701741) и [**Windows.Storage.FileIO.ReadTextAsync**](https://msdn.microsoft.com/library/windows/apps/hh701482). В следующем примере открывается файл `dataFile.txt`, созданный на предыдущем шаге, и из него считывается дата. Подробные сведения о загрузке файловых ресурсов из различных расположений см. в разделе [Загрузка файловых ресурсов](https://msdn.microsoft.com/library/windows/apps/xaml/hh965322).

```CSharp
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

## <span id="Organize_app_data_with_containers"></span><span id="organize_app_data_with_containers"></span><span id="ORGANIZE_APP_DATA_WITH_CONTAINERS"></span>Упорядочение данных приложения с помощью контейнеров


Для упорядочения параметров и файлов данных приложения необходимо создать контейнеры (представленные объектами [**ApplicationDataContainer**](https://msdn.microsoft.com/library/windows/apps/br241599)) вместо того, чтобы работать непосредственно с каталогами. Контейнеры можно добавлять в хранилища локальных, перемещаемых и временных данных приложения. Контейнеры могут иметь до 32 уровней вложенности.

Для создания контейнера параметров вызовите метод [**ApplicationDataContainer.CreateContainer**](https://msdn.microsoft.com/library/windows/apps/br241611). В следующем примере создается контейнер локальных параметров с именем `exampleContainer` и добавляется параметр с именем `exampleSetting`. Значение **Always** из перечисления [**ApplicationDataCreateDisposition**](https://msdn.microsoft.com/library/windows/apps/br241616) показывает, что, если контейнер не существует, он создается.

```CSharp
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

## <span id="Delete_app_settings_and_containers"></span><span id="delete_app_settings_and_containers"></span><span id="DELETE_APP_SETTINGS_AND_CONTAINERS"></span>Удаление параметров и контейнеров приложения


Для удаления простого параметра, который больше не требуется для вашего приложения, используйте метод [**ApplicationDataContainerSettings.Remove**](https://msdn.microsoft.com/library/windows/apps/br241608). В этом примере выполняется удаление ранее созданного локального параметра `exampleSetting`.

```CSharp
Windows.Storage.ApplicationDataContainer localSettings = 
    Windows.Storage.ApplicationData.Current.LocalSettings;
Windows.Storage.StorageFolder localFolder = 
    Windows.Storage.ApplicationData.Current.LocalFolder;

// Delete simple setting

localSettings.Values.Remove("exampleSetting");
```

Для удаления составного параметра используйте метод [**ApplicationDataCompositeValue.Remove**](https://msdn.microsoft.com/library/windows/apps/br241597). В этом примере удаляется локальный составной параметр `exampleCompositeSetting`, созданный в примере выше.

```CSharp
Windows.Storage.ApplicationDataContainer localSettings = 
    Windows.Storage.ApplicationData.Current.LocalSettings;
Windows.Storage.StorageFolder localFolder = 
    Windows.Storage.ApplicationData.Current.LocalFolder;

// Delete composite setting

localSettings.Values.Remove("exampleCompositeSetting");
```

Для удаления контейнера вызовите метод [**ApplicationDataContainer.DeleteContainer**](https://msdn.microsoft.com/library/windows/apps/br241612). В этом примере удаляется ранее созданный контейнер локальных параметров `exampleContainer`.

```CSharp
Windows.Storage.ApplicationDataContainer localSettings = 
    Windows.Storage.ApplicationData.Current.LocalSettings;
Windows.Storage.StorageFolder localFolder = 
    Windows.Storage.ApplicationData.Current.LocalFolder;

// Delete container

localSettings.DeleteContainer("exampleContainer");
```

## <span id="Versioning_your_app_data"></span><span id="versioning_your_app_data"></span><span id="VERSIONING_YOUR_APP_DATA"></span>Управление версиями данных приложения


При желании вы можете назначить версию для данных приложения. Это позволит вам создать следующую версию приложения, которая при изменении формата данных не вызовет проблем совместимости с предыдущей версией приложения. Приложение проверит версию данных приложения в хранилище данных и в случае, если версия окажется более ранней, чем ожидалось, обновит данные приложения до нового формата и изменит их версию. Подробнее см. в описании свойства[** Application.Version**](https://msdn.microsoft.com/library/windows/apps/br241630) и метода [**ApplicationData.SetVersionAsync**](https://msdn.microsoft.com/library/windows/apps/hh701429).

## Связанные разделы

* [**Windows.Storage.ApplicationData**](https://msdn.microsoft.com/library/windows/apps/br241587)
* [**Windows.Storage.ApplicationData.RoamingSettings**](https://msdn.microsoft.com/library/windows/apps/br241624)
* [**Windows.Storage.ApplicationData.RoamingFolder**](https://msdn.microsoft.com/library/windows/apps/br241623)
* [**Windows.Storage.ApplicationData.RoamingStorageQuota**](https://msdn.microsoft.com/library/windows/apps/br241625)
* [**Windows.Storage.ApplicationDataCompositeValue**](https://msdn.microsoft.com/library/windows/apps/br241588)




<!--HONumber=May16_HO2-->


