---
description: Используйте фоновую передачу данных API, чтобы надежно копировать файлы по сети.
title: Фоновая передача данных
ms.assetid: 1207B089-BC16-4BF0-BBD4-FD99950C764B
ms.date: 03/23/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 7d06d91d2195c483f5453aeadbc5523a8935003c
ms.sourcegitcommit: a20457776064c95a74804f519993f36b87df911e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71340574"
---
# <a name="background-transfers"></a>Фоновая передача данных
Используйте фоновую передачу данных API, чтобы надежно копировать файлы по сети. API фоновой передачи данных обеспечивает дополнительные функции отправки и скачивания, которые работают в фоновом режиме в случае приостановки приложения и сохраняются в случае завершения работы приложения. API контролирует состояние сети и автоматически приостанавливает, а затем возобновляет передачу данных в случае потери соединения. Кроме того, эти процессы передачи данных регистрируются приложениями Data Sense и Battery Sense, а это означает, что процесс скачивания корректируется в зависимости от текущего состояния подключения и батареи устройства. API идеально подходит для отправки и скачивания больших файлов с помощью HTTP(S). Протокол FTP также поддерживается, но только для скачиваний.

Фоновая передача данных, выполняющаяся независимо от вызывающего приложения, в основном используется при длительных операциях передачи таких данных, как видео, музыка и крупные изображения. Для этих сценариев использование фоновой передачи данных необходимо, так как скачивание продолжается даже после приостановки приложения.

Для небольших ресурсов, которые, скорее всего, скачаются быстро, вместо фоновой передачи данных следует использовать API [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient).

## <a name="using-windowsnetworkingbackgroundtransfer"></a>Использование Windows.Networking.BackgroundTransfer

### <a name="how-does-the-background-transfer-feature-work"></a>Как работает передача в фоновом режиме
Когда приложение использует функцию фоновой передачи данных для запуска передачи данных, настройка и инициализация запроса производятся с помощью объектов класса [**BackgroundDownloader**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundDownloader) или [**BackgroundUploader**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundUploader). Каждая операция передачи данных обрабатывается системой отдельно и независимо от вызывающего приложения. Если вы хотите отобразить состояние передачи данных в пользовательском интерфейсе приложения, можно использовать информацию о ходе выполнения, при этом приложение может приостанавливать, возобновлять, отменять передачу данных или даже считывать данные в процессе их передачи. Способ, которым система обрабатывает передачу данных, позволяет разумно использовать электроэнергию и предотвращать проблемы, которые могут возникнуть, когда работа подключенного приложения нарушается такими событиями, как приостановка или завершение работы либо внезапные изменения состояния сети.

> [!NOTE]
> В связи с ограниченностью ресурсов для каждого приложения приложение не должно создавать более 200 операций передачи (DownloadOperations + UploadOperations) в любой момент времени. Превышение этого количества может привести очередь передач этого приложения в состояние неустранимой ошибки.

При запуске приложения оно должно вызвать [**AttachAsync**](/uwp/api/windows.networking.backgroundtransfer.downloadoperation.AttachAsync) для всех существующих объектов [**DownloadOperation**](/uwp/api/windows.networking.backgroundtransfer.downloadoperation) и [**UploadOperation**](/uwp/api/windows.networking.backgroundtransfer.uploadoperation). Иначе произойдет утечка уже переданных данных и в конечном итоге результативность функции фоновой передачи данных будет сведена к нулю.

### <a name="performing-authenticated-file-requests-with-background-transfer"></a>Выполнение запросов файлов, которые прошли проверку подлинности, с помощью фоновой передачи данных
Функция передачи данных в фоновом режиме предоставляет методы, поддерживающие базовые учетные данные сервера и прокси-сервера, файлы cookie, а также настраиваемые заголовки HTTP (с помощью метода [**SetRequestHeader**](https://docs.microsoft.com/uwp/api/windows.networking.backgroundtransfer.backgrounduploader.setrequestheader)) для каждой операции передачи.

### <a name="how-does-this-feature-adapt-to-network-status-changes-or-unexpected-shutdowns"></a>Как эта функция адаптируется к изменениям состояния сети или неожиданным выключениям компьютера?
При изменениях состояния сети функция передачи данных в фоновом режиме сохраняет согласованную работу каждой передачи путем интеллектуального использования сведений о подключении и состоянии тарифного плана поставщика услуг, которые предоставляются функцией [Соединение](https://docs.microsoft.com/previous-versions/windows/apps/hh452990(v=win.10)). Поведение приложения в различных условиях подключения к сети определяется тарифной политикой, которая устанавливается для каждой операции передачи данных с использованием значений из [**BackgroundTransferCostPolicy**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundTransferCostPolicy).

Например, в тарифной политике, установленной для операции, может быть указано, что операция должна автоматически приостанавливаться, если устройство используется в сети с лимитным тарифным планом. Передача данных автоматически возобновляется (или перезапускается) при подключении устройства к «неограниченной» сети. Подробнее о задании тарифов для сетей см. в разделе [**NetworkCostType**](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.NetworkCostType).

Хотя функция фоновой передачи данных имеет собственные механизмы обработки изменений состояния сети, существуют другие общие условия сетевого подключения, применимые к приложениям с подключением к сети. Подробности см. в разделе об [использовании информации о доступных сетевых подключениях](https://docs.microsoft.com/previous-versions/windows/apps/hh452983(v=win.10)).

> **Примечание.**   В приложения, работающие на мобильных устройствах, можно добавить возможности отслеживания и ограничения объема передаваемых данных в зависимости от типа подключения к сети, нахождения в роуминге и тарифного плана пользователя. Поэтому на телефоне фоновая передача данных может быть приостановлена несмотря на то, что [**BackgroundTransferCostPolicy**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundTransferCostPolicy) сообщает о необходимости ее продолжения.

В следующей таблице показано, когда разрешена фоновая передача данных на телефоне для каждого значения [**BackgroundTransferCostPolicy**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundTransferCostPolicy) с учетом текущего состояния телефона. Чтобы определить текущее состояние телефона, можно использовать класс [**ConnectionCost**](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.ConnectionCost).

| Состояние устройства                                                                                                                      | UnrestrictedOnly | По умолчанию | Всегда |
|-----------------------------------------------------------------------------------------------------------------------------------|------------------|---------|--------|
| Подключение к WiFi                                                                                                                 | Разрешить            | Разрешить   | Разрешить  |
| Лимитное подключение, не в роуминге, лимит данных не достигнут, настроен на отслеживание расходов по тарифному плану                                                   | Запретить             | Разрешить   | Разрешить  |
| Лимитное подключение, не в роуминге, лимит данных не достигнут, настроен на отслеживание превышения лимита                                                       | Запретить             | Запретить    | Разрешить  |
| Лимитное подключение, в роуминге, лимит данных не достигнут                                                                                     | Запретить             | Запретить    | Разрешить  |
| Лимитное подключение, лимит данных превышен Это состояние возможно только в случае, если пользователь выбирает "Ограничить фоновую передачу данных в Data Sense". | Запретить             | Запретить    | Запретить   |

## <a name="uploading-files"></a>Отправка файлов
При использовании функции передачи данных в фоновом режиме передача происходит в [**UploadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.UploadOperation), который обеспечивает доступ к методам управления, позволяющим начинать операцию заново или отменять ее. События приложения (например, приостановка или завершение работы) и изменения сетевого подключения обрабатываются системой автоматически в соответствии с **UploadOperation**. Отправки будут продолжаться в периоды приостановки приложения, либо будут приостанавливаться и сохраняться в случаях завершения работы приложения. Можно также установить свойство [**CostPolicy**](https://docs.microsoft.com/uwp/api/windows.networking.backgroundtransfer.backgrounddownloader.costpolicy), которое определяет, будет ли ваше приложение начинать отправки, если подключение к Интернету осуществляется через сеть с лимитным тарифным планом.

В следующих примерах будет показано, как создать и инициализировать простую отправку, а также как перечислить и воспроизвести операции, сохраненные в предыдущем сеансе приложения.

### <a name="uploading-a-single-file"></a>Отправка одного файла
Создание отправки начинается с [**BackgroundUploader**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundUploader). Этот класс используется для предоставления методов, которые позволяют приложению настраивать параметры отправки перед созданием итоговой операции [**UploadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.UploadOperation). В следующем примере показано, как это сделать с необходимыми объектами [**Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri) и [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile).

**Определение файла и места назначения отправки**

Прежде чем приступить к созданию [**UploadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.UploadOperation), нужно определить URI местоположения отправки и файл, который будет отправлен. В следующем примере значение *uriString* заполняется строкой, введенной в пользовательском интерфейсе, а значение *file* определяется объектом [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), возвращенным в результате операции [**PickSingleFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.picksinglefileasync).

[!code-js[uploadFile](./code/backgroundtransfer/upload_quickstart/js/main.js#Snippetupload_quickstart_B "Identify the file and destination for the upload")]

**Создание и инициализация операции отправки**

В предыдущем шаге значения *uriString* и *file* передаются экземпляру следующего примера, UploadOp, где они применяются для настройки и запуска новой операции отправки. Сначала проводится анализ *uriString* для создания необходимого объекта [**Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri).

Затем на основе свойств предоставленного класса [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) (*file*) [**BackgroundUploader**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundUploader) заполняет заголовок запроса и задает свойство *SourceFile* с использованием объекта **StorageFile**. Далее вызывается метод [**SetRequestHeader**](https://docs.microsoft.com/uwp/api/windows.networking.backgroundtransfer.backgrounduploader.setrequestheader) для вставки имени файла, предоставленного в виде строки, и свойства [**StorageFile.Name**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.name).

Наконец, [**BackgroundUploader**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundUploader) создает [**UploadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.UploadOperation) (*upload*).

[!code-js[uploadFile](./code/backgroundtransfer/upload_quickstart/js/main.js#Snippetupload_quickstart_A "Create and initialize the upload operation")]

Обратите внимание на асинхронные вызовы метода, определенные с помощью обещаний JavaScript. Рассмотрим строку из последнего примера:

```javascript
promise = upload.startAsync().then(complete, error, progress);
```

За вызовом асинхронного метода следует оператор "then", указывающий определенные приложением методы, которые вызываются при возврате результата из асинхронного метода. Подробнее об этой технике программирования см. в разделе об [асинхронном программировании на JavaScript с использованием объектов Promise](https://docs.microsoft.com/previous-versions/windows).

### <a name="uploading-multiple-files"></a>Отправка нескольких файлов
**Определение файлов и места назначения для отправки**

В сценарии, включающем несколько файлов, передаваемых в одной операции [**UploadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.UploadOperation), процесс начинается обычным способом, указывая необходимый универсальный код ресурса (URI) назначения и сведения о локальном файле. Аналогично примеру из предыдущего раздела, универсальный код ресурса (URI) предоставляется в виде строки пользователем. [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) можно использовать для указания файлов также через пользовательский интерфейс. Однако в этом сценарии приложение должно вызвать метод [**PickMultipleFilesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.pickmultiplefilesasync), чтобы разрешить выбор нескольких файлов через пользовательский интерфейс.

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

В следующих двух примерах используется один метод из примера, **startMultipart**, который вызывался в конце последнего этапа. В целях обучения код в методе, создающем массив объектов [**BackgroundTransferContentPart**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundTransferContentPart), отделен от кода, создающего итоговую операцию [**UploadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.UploadOperation).

Сначала строка URI, указанная пользователем, инициализируется как [**Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri). Затем массив объектов [**IStorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.IStorageFile) (**файлы**), переданный этому методу, проходит итерацию, каждый объект используется для создания нового объекта [**BackgroundTransferContentPart**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundTransferContentPart), который затем помещается в массив **contentParts**.

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

Когда наш массив contentParts заполнен всеми объектами [**BackgroundTransferContentPart**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundTransferContentPart), каждый из которых представляет [**IStorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.IStorageFile) для отправки, мы готовы вызвать [**CreateUploadAsync**](https://docs.microsoft.com/uwp/api/windows.networking.backgroundtransfer.backgrounduploader.createuploadasync), используя [**Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri) для указания, куда будет направлен запрос.

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

### <a name="restarting-interrupted-upload-operations"></a>Перезапуск прерванных операций отправки
После завершения или отмены [**UploadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.UploadOperation) все связанные системные ресурсы освобождаются. Однако если приложение прекратило работу до того, как это произошло, все активные операции приостанавливаются, а связанные с ними ресурсы остаются занятыми. Если эти операции не перечисляются и не воспроизводятся в следующем сеансе приложения, они не завершаются и продолжают использовать ресурсы устройства.

1.  Перед тем как определить функцию, перечисляющую сохраненные операции, следует создать массив, который будет содержать объекты [**UploadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.UploadOperation), возвращенные этой функцией:

    [!code-js[uploadFile](./code/backgroundtransfer/upload_quickstart/js/main.js#Snippetupload_quickstart_C "Restart interrupted upload operation")]

1.  Затем определяем функцию, которая перечисляет сохраненные операции и сохраняет их в нашем массиве. Обратите внимание, что метод **load**, вызываемый для переназначения обратных вызовов операции [**UploadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.UploadOperation), если она сохраняется при завершении работы приложения, содержится в классе UploadOp, который будет определен ниже в этом разделе.

    [!code-js[uploadFile](./code/backgroundtransfer/upload_quickstart/js/main.js#Snippetupload_quickstart_D "Enumerate persisted operations")]

## <a name="downloading-files"></a>Скачивание файлов
При использовании функции передачи данных в фоновом режиме каждая загрузка происходит в [**DownloadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.DownloadOperation), который обеспечивает доступ к методам управления, позволяющим приостанавливать, возобновлять, перезапускать и отменять операцию. События приложения (например, приостановка или завершение работы) и изменения сетевого подключения обрабатываются системой автоматически в соответствии с **DownloadOperation**. Скачивание будет продолжаться в периоды приостановки приложения либо будет приостанавливаться и сохраняться в случаях завершения работы приложения. Для мобильных сетевых сценариев можно установить свойство [**CostPolicy**](https://docs.microsoft.com/uwp/api/windows.networking.backgroundtransfer.backgrounddownloader.costpolicy), которое определяет, будет ли ваше приложение начинать или продолжать скачивание, если подключение к Интернету осуществляется через сеть с лимитным тарифным планом.

Для небольших ресурсов, которые, скорее всего, скачаются быстро, вместо фоновой передачи данных следует использовать API [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient).

В следующих примерах будет показано, как создать и инициализировать простое скачивание, а также как перечислить и воспроизвести операции, сохраненные в предыдущем сеансе приложения.

### <a name="configure-and-start-a-background-transfer-file-download"></a>Настройка и запуск фонового скачивания файла
В следующем примере демонстрируется способ использования строк, представляющих универсальный код ресурса (URI), и имени файла для создания объекта [**Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri) и класса [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), в который будет помещен запрошенный файл. В этом примере новый файл автоматически помещается в заранее определенное расположение. В качестве альтернативы можно использовать [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker), это даст пользователям возможность указать расположение для сохранения файла на устройстве. Обратите внимание, что метод **load**, вызываемый для переназначения обратных вызовов для операции [**DownloadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.DownloadOperation), если она сохраняется при завершении работы приложения, содержится в классе DownloadOp, который будет определен ниже в этом разделе.

[!code-js[uploadFile](./code/backgroundtransfer/download_quickstart/js/main.js#Snippetdownload_quickstart_A)]

Обратите внимание на асинхронные вызовы метода, определенные с помощью обещаний JavaScript. См. строку 17 предыдущего примера кода:

```javascript
promise = download.startAsync().then(complete, error, progress);
```

За вызовом асинхронного метода следует оператор "then", указывающий определенные приложением методы, которые вызываются при возврате результата из асинхронного метода. Подробнее об этой технике программирования см. в разделе об [асинхронном программировании на JavaScript с использованием объектов Promise](https://docs.microsoft.com/previous-versions/windows).

### <a name="adding-additional-operation-control-methods"></a>Добавление дополнительных методов управления работой
Уровень управления может быть повышен с помощью реализации дополнительных методов [**DownloadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.DownloadOperation). Так, чтобы обеспечить возможность отменять операцию, можно добавить в пример выше следующий код.

[!code-js[uploadFile](./code/backgroundtransfer/download_quickstart/js/main.js#Snippetdownload_quickstart_B)]

### <a name="enumerating-persisted-operations-at-start-up"></a>Перечисление сохраненных операций при запуске
После завершения или отмены [**DownloadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.DownloadOperation) все связанные системные ресурсы освобождаются. Однако если приложение завершает работу до того, как происходит одно из этих событий, скачивание будет приостановлено и сохранено в фоновом режиме. В следующих примерах показано, как воспроизвести приостановленное скачивание в новом сеансе приложения.

1.  Перед тем как определить функцию, перечисляющую сохраненные операции, следует создать массив, который будет содержать объекты [**DownloadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.DownloadOperation), возвращенные этой функцией:

    [!code-js[uploadFile](./code/backgroundtransfer/download_quickstart/js/main.js#Snippetdownload_quickstart_D)]

1.  Затем определяем функцию, которая перечисляет сохраненные операции и сохраняет их в нашем массиве. Обратите внимание, что метод **load**, вызываемый для переназначения обратных вызовов для сохраненной операции [**DownloadOperation**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.DownloadOperation), содержится в примере DownloadOp, который будет определен ниже в этом разделе.

    [!code-js[uploadFile](./code/backgroundtransfer/download_quickstart/js/main.js#Snippetdownload_quickstart_E)]

1.  Теперь вы можете использовать заполненный список для перезапуска невыполненных операций.

## <a name="post-processing"></a>Постобработка
Новая функция в Windows 10 — возможность выполнения кода приложения по завершении фоновой передачи данных, даже когда приложение не запущено. Например, приложению может потребоваться обновление списка доступных фильмов после завершения скачивания фильма, вместо того, чтобы ваше приложение искало новые фильмы при каждом запуске. Или приложению может потребоваться обработка неудачной передачи файла путем повторения попытки использовать другой сервер или порт. Постобработка вызывается как для успешных, так и для неудачных передач, чтобы ее можно было использовать для реализации логики повтора и пользовательской обработки ошибок.

Постобработка использует существующую инфраструктуру фоновой задачи. Вы создадите фоновую задачу и свяжете ее с передачами перед началом передачи. Передачи затем выполняются в фоновом режиме и после выполнения ваша фоновая задача вызывается для постобработки.

Завершающая обработка использует новый класс, [**BackgroundTransferCompletionGroup**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundTransferCompletionGroup). Этот класс похож на существующий [**BackgroundTransferGroup**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundTransferGroup) тем, что он позволяет вам группировать фоновые передачи данных вместе, но **BackgroundTransferCompletionGroup** позволяет указывать фоновую задачу для запуска после завершения передачи.

Начните фоновую передачу данных с постобработкой следующим образом.

1.  Создайте объект [**BackgroundTransferCompletionGroup**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundTransferCompletionGroup). Затем создайте объект [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder). Настройте свойство **Trigger** объекта построителя на объект группы завершения, и свойство **TaskEntryPoint** построителя на входную точку фоновой задачи, которая должна выполняться при завершении передачи данных. И, наконец, вызовите метод [**BackgroundTaskBuilder.Register**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskbuilder.register), чтобы зарегистрировать фоновую задачу. Обратите внимание, что многие группы завершения могут совместно использовать одну точку входа фоновой задачи, но вы можете использовать только одну группу завершения в регистрации фоновой задачи.

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

## <a name="request-timeouts"></a>Тайм-ауты запросов
Таким образом, следует учитывать два основных сценария тайм-аута подключения.

-   При отсутствии ответа на запрос нового подключения для передачи в течение пяти минут такой запрос отменяется.

-   Если подключение установлено, любой HTTP-запрос отменяется при отсутствии ответа в течение двух минут.

> **Примечание.**   В этих сценариях при подключении к Интернету функция передачи данных в фоновом режиме автоматически повторит отправку запроса до трех раз. Если подключение к Интернету не было обнаружено, дополнительные запросы будут ожидать его обнаружения.

## <a name="debugging-guidance"></a>Руководство по отладке
Остановка сеанса отладки в Microsoft Visual Studio сопоставима с закрытием приложения: отправки PUT при этом приостанавливаются, а отправки POST завершаются. Даже в процессе отладки ваше приложение должно перечислить, а затем перезапустить или отменить любые оставшиеся отправки. Например, можно сделать так, чтобы приложение отменяло перечисленные сохранившиеся операции отправки при запуске приложения, если предыдущие операции не имеют значения для данного сеанса отладки.

Можно сделать, чтобы при перечислении скачиваний и отправок во время запуска приложения в процессе сеанса отладки приложение отменяло их, если для данного сеанса отладки предыдущие операции не важны. Обратите внимание, что при наличии обновлений проекта Visual Studio, таких как изменения в манифесте приложения, когда приложение удаляется и развертывается заново, [**GetCurrentUploadsAsync**](https://docs.microsoft.com/uwp/api/windows.networking.backgroundtransfer.backgrounduploader.getcurrentuploadsasync) не может перечислить операции, созданные при предыдущем развертывании.

Если при развертывании вы используете фоновую передачу данных, синхронизация внутреннего кэша активных и завершенных операций передачи может быть нарушена. В результате этого запуск новой операции передачи или взаимодействие с существующими операциями и объектами [**BackgroundTransferGroup**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer.BackgroundTransferGroup) может стать невозможным. В ряде случаев попытка взаимодействия с существующими операциями способна вызвать сбой. Это может произойти, если свойству [**TransferBehavior**](https://docs.microsoft.com/uwp/api/windows.networking.backgroundtransfer.backgroundtransfergroup.transferbehavior) задано значение **Parallel**. Такая проблема присуща определенным сценариям во время разработки и не затрагивает конечных пользователей приложения.

Проблема может возникнуть в четырех сценариях с использованием Visual Studio:

-   если вы создаете новый проект с таким же именем приложения, как в существующем проекте, но на другом языке (например, не C++, а C#);
-   если вы изменяете архитектуру конечного объекта (например, с x86 на x64) в существующем проекте;
-   если вы изменяете культуру (например, с нейтральной на en-US) в существующем проекте;
-   если вы добавляете или удаляете возможность в манифесте пакета (например, добавляете **корпоративную аутентификацию**) в существующем проекте.

Обычное обслуживание приложения, включая обновления манифеста, добавляющие или удаляющие возможности, не вызывает данную проблему при развертывании приложения у конечных пользователей.
Чтобы обойти проблему, полностью удалите все версии приложения и повторите развертывание с новым языком, архитектурой, культурой или возможностью. Это можно сделать, используя **начальный** экран или PowerShell и командлет **Remove-AppxPackage**.

## <a name="exceptions-in-windowsnetworkingbackgroundtransfer"></a>Исключения в Windows.Networking.BackgroundTransfer
Исключение создается, если конструктору для объекта [**Windows.Foundation.Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri) передается неправильная строка универсального кода ресурса (URI).

**.NET:** тип [**Windows.Foundation.Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri) отображается в C# и VB как [**System.Uri**](https://docs.microsoft.com/dotnet/api/system.uri).

В C# и Visual Basic можно избежать этой ошибки, используя класс [**System.Uri**](https://docs.microsoft.com/dotnet/api/system.uri) из платформы .NET 4.5 и один из методов [**System.Uri.TryCreate**](https://docs.microsoft.com/dotnet/api/system.uri.trycreate#overloads), чтобы перед составлением URI проверить строку, полученную от пользователя приложения.

В C++ нет метода для преобразования строки в код URI. Если пользователь вводит в приложение данные [**Windows.Foundation.Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri), конструктор должен находиться в блоке try/catch. Если вызывается исключение, приложение может уведомить об этом пользователя и запросить новое имя узла.

Пространство имен [**Windows.Networking.backgroundTransfer**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer) располагает удобными вспомогательными методами и использует перечисления в пространстве имен [**Windows.Networking.Sockets**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets) для обработки ошибок. Это удобно, чтобы по-разному обрабатывать в приложении различные сетевые исключения.

Ошибка, обнаруженная в асинхронном методе в пространстве имен [**Windows.Networking.backgroundTransfer**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer), возвращается в виде значения **HRESULT**. Метод [**BackgroundTransferError.GetStatus**](https://docs.microsoft.com/uwp/api/windows.networking.backgroundtransfer.backgroundtransfererror.getstatus) преобразует сетевую ошибку, произошедшую в операции фоновой передачи, в значение перечисления [**WebErrorStatus**](https://docs.microsoft.com/uwp/api/Windows.Web.WebErrorStatus). Большинство значений перечисления **WebErrorStatus** соответствуют ошибке, возвращаемой стандартной операцией клиента HTTP или FTP. Приложение может фильтровать полученные данные по определенному значению перечисления **WebErrorStatus**, чтобы по-разному действовать в зависимости от причины исключения.

Для ошибок при проверке параметров приложение может использовать также **HRESULT** из исключения, чтобы получить более подробные сведения об ошибке, которая вызвала исключение. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Для многих ошибок при проверке параметров **HRESULT** возвращает значение **E\_INVALIDARG**.

## <a name="important-apis"></a>Важные API
* [**Windows.Networking.BackgroundTransfer**](/uwp/api/windows.networking.backgroundtransfer)
* [**Windows.Foundation.Uri**](/uwp/api/Windows.Foundation.Uri)
* [**Windows.Networking.Sockets**](/uwp/api/Windows.Networking.Sockets)
