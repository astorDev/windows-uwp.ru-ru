---
author: DelfCo
description: "Используйте фоновую передачу данных API, чтобы надежно копировать файлы по сети."
title: "Фоновая передача данных"
ms.assetid: 1207B089-BC16-4BF0-BBD4-FD99950C764B
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: 02e01be9cf726731697eb5934cb86b398431b532

---

# Фоновая передача данных

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**Windows.Networking.backgroundTransfer**](https://msdn.microsoft.com/library/windows/apps/br207242)
-   [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998)
-   [**Windows.Networking.Sockets**](https://msdn.microsoft.com/library/windows/apps/br226960)

Используйте фоновую передачу данных API, чтобы надежно копировать файлы по сети. API фоновой передачи данных обеспечивает дополнительные функции отправки и скачивания, которые работают в фоновом режиме в случае приостановки приложения и сохраняются в случае завершения работы приложения. API контролирует состояние сети и автоматически приостанавливает, а затем возобновляет передачу данных в случае потери соединения. Кроме того, эти процессы передачи данных регистрируются приложениями Data Sense и Battery Sense, а это означает, что процесс скачивания корректируется в зависимости от текущего состояния подключения и батареи устройства. API идеально подходит для отправки и скачивания больших файлов с помощью HTTP(S). Протокол FTP также поддерживается, но только для скачиваний.

Фоновая передача данных, выполняющаяся независимо от вызывающего приложения, в основном используется при длительных операциях передачи таких данных, как видео, музыка и крупные изображения. Для этих сценариев использование фоновой передачи данных необходимо, так как скачивание продолжается даже после приостановки приложения.

Для небольших ресурсов, которые, скорее всего, скачаются быстро, вместо фоновой передачи данных следует использовать API [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639).

## Использование Windows.Networking.BackgroundTransfer


### Как работает передача в фоновом режиме

Когда приложение использует функцию фоновой передачи данных для запуска передачи данных, настройка и инициализация запроса производятся с помощью объектов класса [**BackgroundDownloader**](https://msdn.microsoft.com/library/windows/apps/br207126) или [**BackgroundUploader**](https://msdn.microsoft.com/library/windows/apps/br207140). Каждая операция передачи данных обрабатывается системой отдельно и независимо от вызывающего приложения. Если вы хотите отобразить состояние передачи данных в пользовательском интерфейсе приложения, можно использовать информацию о ходе выполнения, при этом приложение может приостанавливать, возобновлять, отменять передачу данных или даже считывать данные в процессе их передачи. Способ, которым система обрабатывает передачу данных, позволяет разумно использовать электроэнергию и предотвращать проблемы, которые могут возникнуть, когда работа подключенного приложения нарушается такими событиями, как приостановка или завершение работы либо внезапные изменения состояния сети.

### Выполнение запросов файлов, которые прошли проверку подлинности, с помощью фоновой передачи данных

Функция передачи данных в фоновом режиме предоставляет методы, поддерживающие базовые учетные данные сервера и прокси-сервера, файлы cookie, а также настраиваемые заголовки HTTP (с помощью метода [**SetRequestHeader**](https://msdn.microsoft.com/library/windows/apps/br207146)) для каждой операции передачи.

### Как эта функция адаптируется к изменениям состояния сети или неожиданным выключениям компьютера?

При изменениях состояния сети функция передачи данных в фоновом режиме сохраняет согласованную работу каждой передачи путем интеллектуального использования сведений о подключении и состоянии тарифного плана поставщика услуг, которые предоставляются функцией [Соединение](https://msdn.microsoft.com/library/windows/apps/hh452990). Поведение приложения в различных условиях подключения к сети определяется тарифной политикой, которая устанавливается для каждой операции передачи данных с использованием значений из [**BackgroundTransferCostPolicy**](https://msdn.microsoft.com/library/windows/apps/br207138).

Например, в тарифной политике, установленной для операции, может быть указано, что операция должна автоматически приостанавливаться, если устройство используется в сети с лимитным тарифным планом. Передача данных автоматически возобновляется (или перезапускается) при подключении устройства к «неограниченной» сети. Подробнее о задании тарифов для сетей см. в разделе [**NetworkCostType**](https://msdn.microsoft.com/library/windows/apps/br207292).

Хотя функция фоновой передачи данных имеет собственные механизмы обработки изменений состояния сети, существуют другие общие условия сетевого подключения, применимые к приложениям с подключением к сети. Подробности см. в разделе об [использовании информации о доступных сетевых подключениях](https://msdn.microsoft.com/library/windows/apps/hh452983).

> **Примечание**  В приложения, работающих на мобильных устройствах, могут быть добавлены возможности отслеживания и ограничения объема передаваемых данных в зависимости от типа подключения к сети, нахождения в роуминге и тарифного плана, используемого пользователем. Поэтому на телефоне фоновая передача данных может быть приостановлена несмотря на то, что [**BackgroundTransferCostPolicy**](https://msdn.microsoft.com/library/windows/apps/br207138) сообщает о необходимости ее продолжения.

В следующей таблице показано, когда разрешена фоновая передача данных на телефоне для каждого значения [**BackgroundTransferCostPolicy**](https://msdn.microsoft.com/library/windows/apps/br207138) с учетом текущего состояния телефона. Чтобы определить текущее состояние телефона, можно использовать класс [**ConnectionCost**](https://msdn.microsoft.com/library/windows/apps/br207244).

| Состояние устройства                                                                                                                      | UnrestrictedOnly | По умолчанию | Всегда |
|-----------------------------------------------------------------------------------------------------------------------------------|------------------|---------|--------|
| Подключение к WiFi                                                                                                                 | Разрешена            | Разрешена   | Разрешена  |
| Лимитное подключение, не в роуминге, лимит данных не достигнут, настроен на отслеживание расходов по тарифному плану                                                   | Запрещена             | Разрешена   | Разрешена  |
| Лимитное подключение, не в роуминге, лимит данных не достигнут, настроен на отслеживание превышения лимита                                                       | Запрещена             | Запрещена    | Разрешена  |
| Лимитное подключение, в роуминге, лимит данных не достигнут                                                                                     | Запрещена             | Запрещена    | Разрешена  |
| Лимитное подключение, лимит данных превышен Это состояние возможно только в случае, если пользователь выбирает "Ограничить фоновую передачу данных в Data Sense". | Запрещена             | Запрещена    | Запрещена   |

 

## Отправка файлов


При использовании функции передачи данных в фоновом режиме передача происходит в [**UploadOperation**](https://msdn.microsoft.com/library/windows/apps/br207224), который обеспечивает доступ к методам управления, позволяющим начинать операцию заново или отменять ее. События приложения (например, приостановка или завершение работы) и изменения сетевого подключения обрабатываются системой автоматически в соответствии с **UploadOperation**. Отправки будут продолжаться в периоды приостановки приложения, либо будут приостанавливаться и сохраняться в случаях завершения работы приложения. Можно также установить свойство [**CostPolicy**](https://msdn.microsoft.com/library/windows/apps/hh701018), которое определяет, будет ли ваше приложение начинать отправки, если подключение к Интернету осуществляется через сеть с лимитным тарифным планом.

В следующих примерах будет показано, как создать и инициализировать простую отправку, а также как перечислить и воспроизвести операции, сохраненные в предыдущем сеансе приложения.

### Отправка одного файла

Создание отправки начинается с [**BackgroundUploader**](https://msdn.microsoft.com/library/windows/apps/br207140). Этот класс используется для предоставления методов, которые позволяют приложению настраивать параметры отправки перед созданием итоговой операции [**UploadOperation**](https://msdn.microsoft.com/library/windows/apps/br207224). В следующем примере показано, как это сделать с необходимыми объектами [**Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) и [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171).

**Определение файла и места назначения отправки**

Прежде чем приступить к созданию [**UploadOperation**](https://msdn.microsoft.com/library/windows/apps/br207224), нужно определить URI местоположения отправки и файл, который будет отправлен. В следующем примере значение *uriString* заполняется строкой, введенной в пользовательском интерфейсе, а значение *file* определяется объектом [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), возвращенным в результате операции [**PickSingleFileAsync**](https://msdn.microsoft.com/library/windows/apps/jj635275).

[!code-js[uploadFile]
            (./code/backgroundtransfer/upload_quickstart/js/main.js#Snippetupload_quickstart_B "Определение файла и места назначения отправки")]

**Создание и инициализация операции отправки**

В предыдущем шаге значения *uriString* и *file* передаются экземпляру следующего примера, UploadOp, где они применяются для настройки и запуска новой операции отправки. Сначала проводится анализ *uriString* для создания необходимого объекта [**Uri**](https://msdn.microsoft.com/library/windows/apps/br225998).

Затем на основе свойств предоставленного класса [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171) (*file*) [**BackgroundUploader**](https://msdn.microsoft.com/library/windows/apps/br207140) заполняет заголовок запроса и задает свойство *SourceFile* с использованием объекта **StorageFile**. Далее вызывается метод [**SetRequestHeader**](https://msdn.microsoft.com/library/windows/apps/br207146) для вставки имени файла, предоставленного в виде строки, и свойства [**StorageFile.Name**](https://msdn.microsoft.com/library/windows/apps/br227220).

Наконец, [**BackgroundUploader**](https://msdn.microsoft.com/library/windows/apps/br207140) создает [**UploadOperation**](https://msdn.microsoft.com/library/windows/apps/br207224) (*upload*).

[!code-js[uploadFile]
            (./code/backgroundtransfer/upload_quickstart/js/main.js#Snippetupload_quickstart_A "Создание и инициализация операции отправки")]

Обратите внимание на асинхронные вызовы метода, определенные с помощью объектов Promise на JavaScript. Рассмотрим строку из последнего примера:

```javascript
promise = upload.startAsync().then(complete, error, progress);
```

    The async method call is followed by a then statement which indicates methods, defined by the app, that are called when a result from the async method call is returned. For more information on this programming pattern, see [Asynchronous programming in JavaScript using promises](http://msdn.microsoft.com/library/windows/apps/hh464930.aspx).

### Отправка нескольких файлов

**Определение файлов и места назначения для отправки**

    In a scenario involving multiple files transferred with a single [**UploadOperation**](https://msdn.microsoft.com/library/windows/apps/br207224), the process begins as it usually does by first providing the required destination URI and local file information. Similar to the example in the previous section, the URI is provided as a string by the end-user and [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) can be used to provide the ability to indicate files through the user interface as well. However, in this scenario the app should instead call the [**PickMultipleFilesAsync**](https://msdn.microsoft.com/library/windows/apps/br207851) method to enable the selection of multiple files through the UI.

```javascript
function uploadFiles() {
       var filePicker = new Windows.Storage.Pickers.FileOpenPicker();
       filePicker.fileTypeFilter.replaceAll(["*"]);

       filePicker.pickMultipleFilesAsync().then(function (files) {
          if (files === 0) {
             printLog("No file selected");
                return;
          }

          var upload = new UploadOperation();
          var uriString = document.getElementById("serverAddressField").value;
          upload.startMultipart(uriString, files);

          // Persist the upload operation in the global array.
          uploadOperations.push(upload);
       });
    }
```

**Создание объектов на основе предоставленных параметров**

    The next two examples use code contained in a single example method, **startMultipart**, which was called at the end of the last step. For the purpose of instruction the code in the method that creates an array of [**BackgroundTransferContentPart**](https://msdn.microsoft.com/library/windows/apps/hh923029) objects has been split from the code that creates the resultant [**UploadOperation**](https://msdn.microsoft.com/library/windows/apps/br207224).

    First, the URI string provided by the user is initialized as a [**Uri**](https://msdn.microsoft.com/library/windows/apps/br225998). Next, the array of [**IStorageFile**](https://msdn.microsoft.com/library/windows/apps/br227102) objects (**files**) passed to this method is iterated through, each object is used to create a new [**BackgroundTransferContentPart**](https://msdn.microsoft.com/library/windows/apps/hh923029) object which is then placed in the **contentParts** array.

```javascript
upload.startMultipart = function (uriString, files) {
        try {
            var uri = new Windows.Foundation.Uri(uriString);
            var uploader = new Windows.Networking.BackgroundTransfer.BackgroundUploader();

            var contentParts = [];
            files.forEach(function (file, index) {
                var part = new Windows.Networking.BackgroundTransfer.BackgroundTransferContentPart("File" + index, file.name);
                part.setFile(file);
                contentParts.push(part);
            });
```

**Создание и инициализация операции отправки, состоящей из нескольких этапов**

    With our contentParts array populated with all of the [**BackgroundTransferContentPart**](https://msdn.microsoft.com/library/windows/apps/hh923029) objects representing each [**IStorageFile**](https://msdn.microsoft.com/library/windows/apps/br227102) for upload, we are ready to call [**CreateUploadAsync**](https://msdn.microsoft.com/library/windows/apps/hh923973) using the [**Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) to indicate where the request will be sent.

```javascript
        // Create a new upload operation.
            uploader.createUploadAsync(uri, contentParts).then(function (uploadOperation) {

               // Start the upload and persist the promise to be able to cancel the upload.
               upload = uploadOperation;
               promise = uploadOperation.startAsync().then(complete, error, progress);
            });

         } catch (err) {
             displayError(err);
         }
     };
```

### Перезапуск прерванных операций отправки

После завершения или отмены [**UploadOperation**](https://msdn.microsoft.com/library/windows/apps/br207224) все связанные системные ресурсы освобождаются. Однако если приложение прекратило работу до того, как это произошло, все активные операции приостанавливаются, а связанные с ними ресурсы остаются занятыми. Если эти операции не перечисляются и не воспроизводятся в следующем сеансе приложения, они не завершаются и продолжают использовать ресурсы устройства.

1.  Перед тем как определить функцию, перечисляющую сохраненные операции, следует создать массив, который будет содержать объекты [**UploadOperation**](https://msdn.microsoft.com/library/windows/apps/br207224), возвращенные этой функцией:

[!code-js[uploadFile]
            (./code/backgroundtransfer/upload_quickstart/js/main.js#Snippetupload_quickstart_C "Перезапуск прерванных операций отправки")]

2.  Затем определяем функцию, которая перечисляет сохраненные операции и сохраняет их в нашем массиве. Обратите внимание, что метод **load**, вызываемый для переназначения обратных вызовов операции [**UploadOperation**](https://msdn.microsoft.com/library/windows/apps/br207224), если она сохраняется при завершении работы приложения, содержится в классе UploadOp, который будет определен ниже в этом разделе.

[!code-js[uploadFile]
            (./code/backgroundtransfer/upload_quickstart/js/main.js#Snippetupload_quickstart_D "Перечисление сохраненных операций")]

## Скачивание файлов

При использовании функции передачи данных в фоновом режиме каждая загрузка происходит в [**DownloadOperation**](https://msdn.microsoft.com/library/windows/apps/br207154), который обеспечивает доступ к методам управления, позволяющим приостанавливать, возобновлять, перезапускать и отменять операцию. События приложения (например, приостановка или завершение работы) и изменения сетевого подключения обрабатываются системой автоматически в соответствии с **DownloadOperation**. Скачивание будет продолжаться в периоды приостановки приложения либо будет приостанавливаться и сохраняться в случаях завершения работы приложения. Для мобильных сетевых сценариев можно установить свойство [**CostPolicy**](https://msdn.microsoft.com/library/windows/apps/hh701018), которое определяет, будет ли ваше приложение начинать или продолжать скачивание, если подключение к Интернету осуществляется через сеть с лимитным тарифным планом.

Для небольших ресурсов, которые, скорее всего, скачаются быстро, вместо фоновой передачи данных следует использовать API [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639).

В следующих примерах будет показано, как создать и инициализировать простое скачивание, а также как перечислить и воспроизвести операции, сохраненные в предыдущем сеансе приложения.

### Настройка и запуск фонового скачивания файла

В следующем примере демонстрируется способ использования строк, представляющих универсальный код ресурса (URI), и имени файла для создания объекта [**Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) и класса [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), в который будет помещен запрошенный файл. В этом примере новый файл автоматически помещается в заранее определенное расположение. В качестве альтернативы можно использовать [**FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871), это даст пользователям возможность указать расположение для сохранения файла на устройстве. Обратите внимание, что метод **load**, вызываемый для переназначения обратных вызовов для операции [**DownloadOperation**](https://msdn.microsoft.com/library/windows/apps/br207154), если она сохраняется при завершении работы приложения, содержится в классе DownloadOp, который будет определен ниже в этом разделе.

[!code-js[uploadFile](./code/backgroundtransfer/download_quickstart/js/main.js#Snippetdownload_quickstart_A)]

Обратите внимание на асинхронные вызовы метода, определенные при помощи объектов Promise на JavaScript. См. строку 17 предыдущего примера кода:

```javascript
promise = download.startAsync().then(complete, error, progress);
```

За вызовом асинхронного метода следует оператор "then", указывающий определенные приложением методы, которые вызываются при возврате результата из асинхронного метода. Подробнее об этой технике программирования: [Асинхронное программирование на JavaScript с использованием объектов Promise](http://msdn.microsoft.com/library/windows/apps/hh464930.aspx).

### Добавление дополнительных методов управления работой

Уровень управления может быть повышен с помощью реализации дополнительных методов [**DownloadOperation**](https://msdn.microsoft.com/library/windows/apps/br207154). Так, чтобы обеспечить возможность отменять операцию, можно добавить в пример выше следующий код.

[!code-js[uploadFile](./code/backgroundtransfer/download_quickstart/js/main.js#Snippetdownload_quickstart_B)]

### Перечисление сохраненных операций при запуске

После завершения или отмены [**DownloadOperation**](https://msdn.microsoft.com/library/windows/apps/br207154) все связанные системные ресурсы освобождаются. Однако если приложение завершает работу до того, как происходит одно из этих событий, скачивание будет приостановлено и сохранено в фоновом режиме. В следующих примерах показано, как воспроизвести приостановленное скачивание в новом сеансе приложения.

1.  Перед тем как определить функцию, перечисляющую сохраненные операции, следует создать массив, который будет содержать объекты [**DownloadOperation**](https://msdn.microsoft.com/library/windows/apps/br207154), возвращенные этой функцией:

[!code-js[uploadFile](./code/backgroundtransfer/download_quickstart/js/main.js#Snippetdownload_quickstart_D)]

2.  Затем определяем функцию, которая перечисляет сохраненные операции и сохраняет их в нашем массиве. Обратите внимание, что метод **load**, вызываемый для переназначения обратных вызовов для сохраненной операции [**DownloadOperation**](https://msdn.microsoft.com/library/windows/apps/br207154), содержится в примере DownloadOp, который будет определен ниже в этом разделе.

[!code-js[uploadFile](./code/backgroundtransfer/download_quickstart/js/main.js#Snippetdownload_quickstart_E)]

3.  Теперь вы можете использовать заполненный список для перезапуска невыполненных операций.

## Постобработка

Новая функция в Windows 10 — возможность выполнения кода приложения после завершения фоновой передачи данных, даже когда приложение не запущено. Например, приложению может потребоваться обновление списка доступных фильмов после завершения скачивания фильма, вместо того, чтобы ваше приложение искало новые фильмы при каждом запуске. Или приложению может потребоваться обработка неудачной передачи файла путем повторения попытки использовать другой сервер или порт. Постобработка вызывается как для успешных, так и для неудачных передач, чтобы ее можно было использовать для реализации логики повтора и пользовательской обработки ошибок.

Постобработка использует существующую инфраструктуру фоновой задачи. Вы создадите фоновую задачу и свяжете ее с передачами перед началом передачи. Передачи затем выполняются в фоновом режиме и после выполнения ваша фоновая задача вызывается для постобработки.

Завершающая обработка использует новый класс, [**BackgroundTransferCompletionGroup**](https://msdn.microsoft.com/library/windows/apps/dn804209). Этот класс похож на существующий [**BackgroundTransferGroup**](https://msdn.microsoft.com/library/windows/apps/dn279030) тем, что он позволяет вам группировать фоновые передачи данных вместе, но **BackgroundTransferCompletionGroup** позволяет указывать фоновую задачу для запуска после завершения передачи.

Начните фоновую передачу данных с постобработкой следующим образом.

1.  Создайте объект [**BackgroundTransferCompletionGroup**](https://msdn.microsoft.com/library/windows/apps/dn804209). Затем создайте объект [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768). Настройте свойство **Trigger** объекта построителя на объект группы завершения, и свойство **TaskEngtyPoint** построителя на входную точку фоновой задачи, которая должна выполняться при завершении передачи данных. И, наконец, вызовите метод [**BackgroundTaskBuilder.Register**](https://msdn.microsoft.com/library/windows/apps/br224772), чтобы зарегистрировать фоновую задачу. Обратите внимание, что многие группы завершения могут совместно использовать одну точку входа фоновой задачи, но вы можете использовать только одну группу завершения в регистрации фоновой задачи.

   ```csharp
    var completionGroup = new BackgroundTransferCompletionGroup();
    BackgroundTaskBuilder builder = new BackgroundTaskBuilder();

    builder.Name = "MyDownloadProcessingTask";
    builder.SetTrigger(completionGroup.Trigger);
    builder.TaskEntryPoint = "Tasks.BackgroundDownloadProcessingTask";

    BackgroundTaskRegistration downloadProcessingTask = builder.Register();
    ```

2.  Далее вы свяжете фоновые передачи с группой завершения. После создания всех передач включите группу завершения.

   ```csharp
    BackgroundDownloader downloader = new BackgroundDownloader(completionGroup);
    DownloadOperation download = downloader.CreateDownload(uri, file);
    Task<DownloadOperation> startTask = download.StartAsync().AsTask();

    // App still sees the normal completion path
    startTask.ContinueWith(ForegroundCompletionHandler);

    // Do not enable the CompletionGroup until after all downloads are created.
    downloader.CompletinGroup.Enable();
    ```

3.  Код в фоновом режиме извлекает список операций из состава триггера, и ваш код может затем проверить сведения для каждой операции и выполнить соответствующую постобработку для каждой операции.

   ```csharp
    public class BackgroundDownloadProcessingTask : IBackgroundTask
    {
      public async void Run(IBackgroundTaskInstance taskInstance)
      {
        var details = (BackgroundTransferCompletionGroupTriggerDetails)taskInstance.TriggerDetails;
        IReadOnlyList<DownloadOperation> downloads = details.Downloads;

        // Do post-processing on each finished operation in the list of downloads
      }
    }
    ```

Постобработка является регулярной фоновой задачей. Она является частью пула всех фоновых задач, и она регулируется той же политикой управления ресурсами, что и все фоновые задачи.

Также обратите внимание, что постобработка не заменит обработчики завершения переднего плана. Если ваше приложение определяет обработчик завершения переднего плана, и ваше приложение работает на момент завершения передачи файла, в таком случае будут вызваны обработчик завершения на переднем плане и обработчик завершения в фоновом режиме. Порядок, в котором задачи переднего плана и фоновые задачи вызываются, не гарантируется. Если указать оба, вы должны убедиться, что две задачи будут работать надлежащим образом и не будут мешать друг другу, если будут выполняться одновременно.

## Тайм-ауты запросов

Таким образом, следует учитывать два основных сценария тайм-аута подключения.

-   При отсутствии ответа на запрос нового подключения для передачи в течение пяти минут такой запрос отменяется.

-   Если подключение установлено, любой HTTP-запрос отменяется при отсутствии ответа в течение двух минут.

> **Примечание.** В данных сценариях при условии наличия подключения к Интернету функция передачи данных в фоновом режиме автоматически повторит отправку запроса до трех раз. Если подключение к Интернету не было обнаружено, дополнительные запросы будут ожидать его обнаружения.

## Руководство по отладке

Остановка сеанса отладки в Microsoft Visual Studio сопоставима с закрытием приложения: отправки PUT при этом приостанавливаются, а отправки POST завершаются. Даже в процессе отладки ваше приложение должно перечислить, а затем перезапустить или отменить любые оставшиеся отправки. Например, можно сделать так, чтобы приложение отменяло перечисленные сохранившиеся операции отправки при запуске приложения, если предыдущие операции не имеют значения для данного сеанса отладки.

Можно сделать, чтобы при перечислении скачиваний и отправок во время запуска приложения в процессе сеанса отладки приложение отменяло их, если для данного сеанса отладки предыдущие операции не важны. Обратите внимание, что при наличии обновлений проекта Visual Studio, таких как изменения в манифесте приложения, когда приложение удаляется и развертывается заново, [**GetCurrentUploadsAsync**](https://msdn.microsoft.com/library/windows/apps/hh701149) не может перечислить операции, созданные при предыдущем развертывании.

Если при развертывании вы используете фоновую передачу данных, синхронизация внутреннего кэша активных и завершенных операций передачи может быть нарушена. В результате этого запуск новой операции передачи или взаимодействие с существующими операциями и объектами [**BackgroundTransferGroup**](https://msdn.microsoft.com/library/windows/apps/dn279030) может стать невозможным. В ряде случаев попытка взаимодействия с существующими операциями способна вызвать сбой. Это может произойти, если свойству [**TransferBehavior**](https://msdn.microsoft.com/library/windows/apps/dn279033) задано значение **Parallel**. Такая проблема присуща определенным сценариям во время разработки и не затрагивает конечных пользователей приложения.

Проблема может возникнуть в четырех сценариях с использованием Visual Studio:

-   если вы создаете новый проект с таким же именем приложения, как в существующем проекте, но на другом языке (например, не C++, а C#);
-   если вы изменяете архитектуру конечного объекта (например, с x86 на x64) в существующем проекте;
-   если вы изменяете культуру (например, с нейтральной на en-US) в существующем проекте;
-   если вы добавляете или удаляете возможность в манифесте пакета (например, добавляете **корпоративную аутентификацию**) в существующем проекте.

Обычное обслуживание приложения, включая обновления манифеста, добавляющие или удаляющие возможности, не вызывает данную проблему при развертывании приложения у конечных пользователей.
Чтобы обойти проблему, полностью удалите все версии приложения и повторите развертывание с новым языком, архитектурой, культурой или возможностью. Это можно сделать, используя **начальный** экран или PowerShell и командлет **Remove-AppxPackage**.

## Исключения в Windows.Networking.BackgroundTransfer

Исключение создается, если конструктору объекта [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) передается неправильная строка для универсального кода ресурса (URI).

**.NET: **Тип [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) отображается как [**System.Uri**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) в C# и VB.

В C# и Visual Basic можно избежать этой ошибки, используя класс [**System.Uri**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) из платформы .NET 4.5 и один из методов [**System.Uri.TryCreate**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.trycreate.aspx), чтобы перед составлением URI проверить строку, полученную от пользователя приложения.

В C++ нет метода для преобразования строки в код URI. Если пользователь вводит в приложение данные [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998), конструктор должен находиться в блоке try/catch. Если вызывается исключение, приложение может уведомить об этом пользователя и запросить новое имя узла.

Пространство имен [**Windows.Networking.backgroundTransfer**](https://msdn.microsoft.com/library/windows/apps/br207242) располагает удобными вспомогательными методами и использует перечисления в пространстве имен [**Windows.Networking.Sockets**](https://msdn.microsoft.com/library/windows/apps/br226960) для обработки ошибок. Это удобно, чтобы по-разному обрабатывать в приложении различные сетевые исключения.

Ошибка, обнаруженная в асинхронном методе в пространстве имен [**Windows.Networking.backgroundTransfer**](https://msdn.microsoft.com/library/windows/apps/br207242), возвращается в виде значения **HRESULT**. Метод [**BackgroundTransferError.GetStatus**](https://msdn.microsoft.com/library/windows/apps/hh701093) преобразует сетевую ошибку, произошедшую в операции фоновой передачи, в значение перечисления [**WebErrorStatus**](https://msdn.microsoft.com/library/windows/apps/hh747818). Большинство значений перечисления **WebErrorStatus** соответствуют ошибке, возвращаемой стандартной операцией клиента HTTP или FTP. Приложение может фильтровать полученные данные по определенному значению перечисления **WebErrorStatus**, чтобы по-разному действовать в зависимости от причины исключения.

Для ошибок при проверке параметров приложение может использовать также **HRESULT** из исключения, чтобы получить более подробные сведения об ошибке, которая вызвала исключение. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Для многих ошибок при проверке параметров возвращаемым **HRESULT** является **E\_INVALIDARG**.




<!--HONumber=Jun16_HO4-->


