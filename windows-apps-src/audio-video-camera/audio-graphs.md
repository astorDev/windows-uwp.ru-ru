---
ms.assetid: CB924E17-C726-48E7-A445-364781F4CCA1
description: В этой статье показано, как с помощью API в пространстве имен Windows.Media.Audio создавать звуковые графы для передачи, микширования и обработки звука.
title: Звуковые графы
---

# Звуковые графы

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этой статье показано, как с помощью API в пространстве имен [**Windows.Media.Audio**](https://msdn.microsoft.com/library/windows/apps/dn914341) создавать звуковые графы для передачи, микширования и обработки звука.

Звуковой граф — это набор связанных друг с другом звуковых узлов, по которым передаются звуковые данные. Узлы аудиовхода поставляют звуковые данные из входных аудиоустройств, звуковых файлов или пользовательского кода в граф. Звуковые данные, обработанные с помощью графа, доставляются на узлы аудиовыхода. Звук можно направить из графа на выходные аудиоустройства, а также в звуковые файлы и пользовательский код. Последний тип узла — это узел субмикширования, принимающий звуковые данные из одного или нескольких узлов и объединяющий их в единый вывод, который можно передать в другие узлы графа. После создания всех узлов и настройки соединений между ними вам просто нужно запустить звуковой граф, после чего звуковые данные перемещаются из узлов ввода через узлы субмикширования на узлы вывода. Эта модель позволяет быстро и легко реализовать ряд сценариев, например записать звуковой файл с помощью микрофона устройства, воспроизвести звук из файла через динамик устройства или смикшировать аудиоданные из нескольких источников.

При добавлении к звуковому графу звуковых эффектов реализуются дополнительные сценарии. Каждый узел звукового графа можно при необходимости заполнить звуковыми эффектами, которые отвечают за обработку звука, проходящего через узел. Написав всего несколько строк кода, вы можете подключить к звуковому узлу несколько встроенных эффектов, таких как эхо, эквалайзер, ограничение и реверберация. Кроме того, вы можете создать собственные пользовательские звуковые эффекты, которые работают точно так же, как и встроенные эффекты.

**Примечание.**  
В [примере UWP AudioGraph](http://go.microsoft.com/fwlink/?LinkId=619481) реализован код, описанный в этом обзоре. Скачайте этот пример, чтобы просмотреть код в контексте или использовать пример как отправную точку для настройки вашего приложения.

## Выбор между звуковым графом среды выполнения Windows и интерфейсом XAudio2

API звуковых графов среды выполнения Windows обеспечивают выполнение функций, которые также можно реализовать с помощью [API XAudio2](https://msdn.microsoft.com/library/windows/desktop/hh405049) на основе модели COM. Ниже перечислены возможности инфраструктуры звукового графа среды выполнения Windows, отличные от интерфейса XAudio2.

-   API звуковых графов среды выполнения Windows значительно проще в использовании, чем интерфейс XAudio2.
-   API звуковых графов среды выполнения Windows можно использовать как в среде C++, так и в среде C#.
-   В API звуковых графов среды выполнения Windows можно непосредственно использовать звуковые файлы, в том числе сжатые. Интерфейс XAudio2 работает только в звуковых буферах и не обеспечивает никаких возможностей ввода-вывода файлов.
-   API звуковых графов среды выполнения Windows могут использовать звуковой конвейер с низкой задержкой в ОС Windows 10.
-   API звуковых графов среды выполнения Windows поддерживают автоматическое переключение между конечными точками, если используются параметры конечных точек по умолчанию. Например, если пользователь переключается с динамика устройства на гарнитуру, звук автоматически перенаправляется на новый ввод.

## Класс AudioGraph

Класс [**AudioGraph**](https://msdn.microsoft.com/library/windows/apps/dn914176) — это родительский объект всех узлов, которые входят в граф. Этот объект используется для создания экземпляров звуковых узлов всех типов. Создайте экземпляр класса **AudioGraph**. Для этого инициализируйте объект [**AudioGraphSettings**](https://msdn.microsoft.com/library/windows/apps/dn914185), содержащий параметры конфигурации графа, а затем вызовите метод [**AudioGraph.CreateAsync**](https://msdn.microsoft.com/library/windows/apps/dn914216). Возвращенный класс [**CreateAudioGraphResult**](https://msdn.microsoft.com/library/windows/apps/dn914273) обеспечивает доступ к созданному звуковому графу или возвращает значение ошибки, если звуковой граф не удалось создать.

[!code-cs[DeclareAudioGraph](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetDeclareAudioGraph)]

[!code-cs[InitAudioGraph](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetInitAudioGraph)]

-   Звуковые узлы всех типов создаются с помощью методов Create\* класса **AudioGraph**.
-   Метод [**AudioGraph.Start**](https://msdn.microsoft.com/library/windows/apps/dn914244) позволяет начать обработку звуковых данных с помощью звукового графа. Метод [**AudioGraph.Stop**](https://msdn.microsoft.com/library/windows/apps/dn914245) позволяет остановить звуковую обработку. Каждый узел графа можно запустить и остановить независимо от того, выполняется ли граф, однако при остановке графа все узлы отключаются. Метод [**ResetAllNodes**](https://msdn.microsoft.com/library/windows/apps/dn914242) позволяет удалить из всех узлов графа любые данные, находящиеся в их звуковых буферах..
-   Событие [**QuantumStarted**](https://msdn.microsoft.com/library/windows/apps/dn914241) возникает, когда граф приступает к обработке нового кванта времени звуковых данных. Событие [**QuantumProcessed**](https://msdn.microsoft.com/library/windows/apps/dn914240) возникает, когда обработка кванта времени завершается.

-   Единственное необходимое свойство [**AudioGraphSettings**](https://msdn.microsoft.com/library/windows/apps/dn914185) — это [**AudioRenderCategory**](https://msdn.microsoft.com/library/windows/apps/dn297724). Если задать это значение, система оптимизирует звуковой конвейер для указанной категории.
-   Размер кванта времени звукового графа определяет количество примеров, которые обрабатываются одновременно. По умолчанию размер кванта времени составляет 10 мс на основе частоты дискретизации по умолчанию. Если указать пользовательский размер кванта времени, установив свойство [**DesiredSamplesPerQuantum**](https://msdn.microsoft.com/library/windows/apps/dn914205), необходимо также задать значение **ClosestToDesired** для свойства [**QuantumSizeSelectionMode**](https://msdn.microsoft.com/library/windows/apps/dn914208). В противном случае указанное значение будет проигнорировано. Если используется это значение, система выберет размер кванта времени, максимально соответствующий указанному вами. Чтобы определить фактический размер кванта времени, проверьте свойство [**SamplesPerQuantum**](https://msdn.microsoft.com/library/windows/apps/dn914243) класса **AudioGraph** после его создания.
-   Если вы планируете всего лишь использовать звуковой граф с файлами без вывода на звуковое устройство, мы рекомендуем использовать размер кванта времени по умолчанию, не задавая свойство [**DesiredSamplesPerQuantum**](https://msdn.microsoft.com/library/windows/apps/dn914205).
-   Свойство [**DesiredRenderDeviceAudioProcessing**](https://msdn.microsoft.com/library/windows/apps/dn958522) определяет объем выходных данных звукового графа, обрабатываемых первичным устройством рендеринга. Параметр **Default** позволяет системе использовать обработку звука по умолчанию для определенной категории рендеринга звуковых данных. С помощью такой обработки вы можете значительно улучшить качество звука на некоторых устройствах, особенно мобильных устройствах с небольшими динамиками. Параметр **Raw** позволяет повысить производительность благодаря уменьшению количества обрабатываемых сигналов, но также может привести к ухудшению качества звука на некоторых устройствах.
-   Если для режима [**QuantumSizeSelectionMode**](https://msdn.microsoft.com/library/windows/apps/dn914208) задано значение **LowestLatency**, звуковой граф автоматически будет использовать параметр **Raw** для [**DesiredRenderDeviceAudioProcessing**](https://msdn.microsoft.com/library/windows/apps/dn958522).
-   Свойство [**EncodingProperties**](https://msdn.microsoft.com/library/windows/apps/dn958523) определяет формат звуковых данных, используемый графом. Поддерживаются только 32-разрядные форматы с плавающей точкой.
-   [
            **PrimaryRenderDevice**](https://msdn.microsoft.com/library/windows/apps/dn958524) устанавливает первичное устройство рендеринга для звукового графа. Если это свойство не задано, будет использоваться системное устройство по умолчанию. Первичное устройство рендеринга используется для подсчета размеров квантов времени для других узлов графа. Если в системе нет устройств рендеринга звуковых данных, звуковой граф не будет создан.

Вы можете позволить звуковому графу использовать устройство рендеринга звуковых данных по умолчанию, либо с помощью класса [**Windows.Devices.Enumeration.DeviceInformation**](https://msdn.microsoft.com/library/windows/apps/br225393) получить список таких устройств, доступных в системе, вызвав метод [**FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/br225432) и передав селектор устройств рендеринга звуковых данных, возвращенных методом [**Windows.Media.Devices.MediaDevice.GetAudioRenderSelector**](https://msdn.microsoft.com/library/windows/apps/br226817). Вы можете выбрать один из возвращенных объектов **DeviceInformation** программным путем или отобразить пользовательский интерфейс и выбрать с его помощью устройство для задания свойства [**PrimaryRenderDevice**](https://msdn.microsoft.com/library/windows/apps/dn958524).

[!code-cs[EnumerateAudioRenderDevices](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetEnumerateAudioRenderDevices)]

##  Узел ввода устройства

Узел ввода устройства передает в граф звуковые данные с устройства аудиозаписи, подключенного к системе, например микрофона. Создайте объект [**DeviceInputNode**](https://msdn.microsoft.com/library/windows/apps/dn914082), использующий системное устройство аудиозаписи по умолчанию, вызвав метод [**CreateDeviceInputNodeAsync**](https://msdn.microsoft.com/library/windows/apps/dn914218). Укажите категорию [**AudioRenderCategory**](https://msdn.microsoft.com/library/windows/apps/dn297724), чтобы система оптимизировала звуковой конвейер для указанной категории.

[!code-cs[DeclareDeviceInputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetDeclareDeviceInputNode)]


[!code-cs[CreateDeviceInputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetCreateDeviceInputNode)]

Чтобы указать конкретное устройство аудиозаписи для узла ввода устройства, можно использовать класс [**Windows.Devices.Enumeration.DeviceInformation**](https://msdn.microsoft.com/library/windows/apps/br225393), который позволяет получить список устройств аудиозаписи, доступных в системе, путем вызова метода [**FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/br225432) и передачи селектора устройств рендеринга звуковых данных, возвращенного методом [**Windows.Media.Devices.MediaDevice.GetAudioRenderSelector**](https://msdn.microsoft.com/library/windows/apps/br226817). Вы можете выбрать один из возвращенных объектов **DeviceInformation** программным путем или отобразить пользовательский интерфейс, с помощью которого пользователь сможет выбрать устройство для последующей передачи методу [**CreateDeviceInputNodeAsync**](https://msdn.microsoft.com/library/windows/apps/dn914218).

[!code-cs[EnumerateAudioCaptureDevices](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetEnumerateAudioCaptureDevices)]

##  Узел вывода устройства

Узел вывода устройства отправляет аудиоданные из графа на устройство рендеринга звуковых данных, такое как динамики или гарнитура. Создайте узел [**DeviceOutputNode**](https://msdn.microsoft.com/library/windows/apps/dn914098), вызвав метод [**CreateDeviceOutputNodeAsync**](https://msdn.microsoft.com/library/windows/apps/dn958525). Узел вывода использует свойство [**PrimaryRenderDevice**](https://msdn.microsoft.com/library/windows/apps/dn958524) звукового графа.

[!code-cs[DeclareDeviceOutputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetDeclareDeviceOutputNode)]

[!code-cs[CreateDeviceOutputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetCreateDeviceOutputNode)]

##  Узел ввода файла

Узел ввода файла позволяет передать в граф данные из звукового файла. Создайте узел [**AudioFileInputNode**](https://msdn.microsoft.com/library/windows/apps/dn914108), вызвав метод [**CreateFileInputNodeAsync**](https://msdn.microsoft.com/library/windows/apps/dn914226).

[!code-cs[DeclareFileInputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetDeclareFileInputNode)]


[!code-cs[CreateFileInputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetCreateFileInputNode)]

-   Узлы ввода файлов поддерживают следующие форматы файлов: mp3, wav, wma, m4a.
-   Задайте свойство [**StartTime**](https://msdn.microsoft.com/library/windows/apps/dn914130), чтобы указать смещение времени, при котором должно начаться воспроизведение файла. Если это свойство имеет значение Null, файл будет воспроизведен с самого начала. Задайте свойство [**EndTime**](https://msdn.microsoft.com/library/windows/apps/dn914118), чтобы указать смещение времени, при котором должно завершиться воспроизведение файла. Если это свойство имеет значение Null, воспроизведение прекратится в самом конце файла. Значение времени начала должно быть меньше, чем значение времени окончания, а значение времени окончания должно быть меньше или равно длительности звукового файла, указанной в значении свойства [**Duration**](https://msdn.microsoft.com/library/windows/apps/dn914116).
-   Найдите нужное положение в пределах звукового файла. Для этого вызовите метод [**Seek**](https://msdn.microsoft.com/library/windows/apps/dn914127) и укажите значение смещения времени, на которое необходимо переместить положение воспроизведения файла. Указанное значение должно находиться в диапазоне свойств [**StartTime**](https://msdn.microsoft.com/library/windows/apps/dn914130) и [**EndTime**](https://msdn.microsoft.com/library/windows/apps/dn914118). Получите текущее положение воспроизведения для узла с помощью свойства [**Position**](https://msdn.microsoft.com/library/windows/apps/dn914124), доступного только для чтения.
-   Включите повторение звукового файла, установив свойство [**LoopCount**](https://msdn.microsoft.com/library/windows/apps/dn914120). Если это значение отлично от Null, оно указывает на количество раз, в течение которых файл будет воспроизведен после первого раза. Например, если задать для свойства **LoopCount** значение 1 или 5, файл будет воспроизведен 2 и 6 раз соответственно. Если указать для свойства **LoopCount** значение Null, файл будет воспроизведен неограниченное количество раз. Чтобы остановить повторное воспроизведение, задайте значение 0.
-   Регулируйте скорость воспроизведения звукового файла путем установки свойства [**PlaybackSpeedFactor**](https://msdn.microsoft.com/library/windows/apps/dn914123). Значение 1 указывает на первоначальную скорость передачи файла, а значения 0,5 и 2 — на скорость, меньшую и большую в два раза соответственно.

##  Узел вывода файла

Узел вывода файла позволяет направить звуковые данные из графа в звуковой файл. Создайте узел [**AudioFileOutputNode**](https://msdn.microsoft.com/library/windows/apps/dn914133), вызвав метод [**CreateFileOutputNodeAsync**](https://msdn.microsoft.com/library/windows/apps/dn914227).

[!code-cs[DeclareFileOutputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetDeclareFileOutputNode)]


[!code-cs[CreateFileOutputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetCreateFileOutputNode)]

-   Узлы вывода файлов поддерживают следующие форматы файлов: mp3, wav, wma, m4a.
-   Необходимо вызвать [**AudioFileOutputNode.Stop**](https://msdn.microsoft.com/library/windows/apps/dn914144), чтобы остановить обработку узла, перед вызовом [**AudioFileOutputNode.FinalizeAsync**](https://msdn.microsoft.com/library/windows/apps/dn914140). В противном случае будет создано исключение.

##  Узел ввода аудиокадра

Узел ввода аудиокадра позволяет отправить в звуковой граф звуковые данные, созданные с помощью собственного кода. Это делает возможными различные сценарии, такие как создание синтезатор пользовательского программного обеспечения. Создайте узел [**AudioFrameInputNode**](https://msdn.microsoft.com/library/windows/apps/dn914147), вызвав [**CreateFrameInputNode**](https://msdn.microsoft.com/library/windows/apps/dn914230).

[!code-cs[DeclareFrameInputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetDeclareFrameInputNode)]


[!code-cs[CreateFrameInputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetCreateFrameInputNode)]

Событие [**FrameInputNode.QuantumStarted**](https://msdn.microsoft.com/library/windows/apps/dn958507) возникает, когда звуковой граф готов начать обработку следующего кванта времени звуковых данных. Этому событию нужно предоставить свои пользовательские звуковые данные из обработчика.

[!code-cs[QuantumStarted](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetQuantumStarted)]

-   Объект [**FrameInputNodeQuantumStartedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn958533), переданный в обработчик событий **QuantumStarted**, предоставляет свойство [**RequiredSamples**](https://msdn.microsoft.com/library/windows/apps/dn958534), которое указывает, сколько примеров необходимо заполнить звуковому графу для обработки кванта времени.
-   Вызовите метод [**AudioFrameInputNode.AddFrame**](https://msdn.microsoft.com/library/windows/apps/dn914148), чтобы передать в граф объект [**AudioFrame**](https://msdn.microsoft.com/library/windows/apps/dn930871), заполненный звуковыми данными.
-   Ниже приведен пример реализации вспомогательного метода **GenerateAudioData**.

Чтобы заполнить класс [**AudioFrame**](https://msdn.microsoft.com/library/windows/apps/dn930871) звуковыми данными, необходимо получить доступ к базовому буферу памяти аудиокадра. Для этого нужно инициализировать COM-интерфейс **IMemoryBufferByteAccess**, добавив следующий код в ваше пространство имен.

[!code-cs[ComImportIMemoryBufferByteAccess](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetComImportIMemoryBufferByteAccess)]

Следующий код представляет собой пример реализации вспомогательного метода **GenerateAudioData**, создающего [**AudioFrame**](https://msdn.microsoft.com/library/windows/apps/dn930871) и заполняющего его звуковыми данными.

[!code-cs[GenerateAudioData](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetGenerateAudioData)]

-   Поскольку этот метод получает доступ к необработанному буферу, который лежит в основе типов среды выполнения Windows, его необходимо объявить с помощью ключевого слова **unsafe**. Необходимо также настроить проект в Microsoft Visual Studio, чтобы разрешить компиляцию небезопасного кода. Для этого откройте страницу проекта **Свойства**, щелкните страницу свойств **Сборка** и установите флажок **Разрешить небезопасный код**.
-   Инициализируйте новый экземпляр класса [**AudioFrame**](https://msdn.microsoft.com/library/windows/apps/dn930871) в пространстве имен **Windows.Media**, передав нужный размер буфера в конструктор. Размер буфера — это число примеров, умноженное на размер каждого примера.
-   Получите [**AudioBuffer**](https://msdn.microsoft.com/library/windows/apps/dn958454) аудиокадра, вызвав [**LockBuffer**](https://msdn.microsoft.com/library/windows/apps/dn930878).
-   Получите экземпляр COM-интерфейса [**IMemoryBufferByteAccess**](https://msdn.microsoft.com/library/windows/desktop/mt297505) из звукового буфера путем вызова метода [**CreateReference**](https://msdn.microsoft.com/library/windows/apps/dn958457).
-   Получите указатель на необработанные данные звукового буфера, вызвав метод [**IMemoryBufferByteAccess.GetBuffer**](https://msdn.microsoft.com/library/windows/desktop/mt297506), и приведите его к типу примеров звуковых данных.
-   Заполните буфер данными и верните класс [**AudioFrame**](https://msdn.microsoft.com/library/windows/apps/dn930871) для отправки в звуковой граф.

##  Узел вывода аудиокадра

Узел вывода аудиокадра позволяет получать и обрабатывать выходные звуковые данных из звукового графа с помощью пользовательского кода, который вы создали. Пример такого сценария — анализ сигналов аудиовыхода. Создайте узел [**AudioFrameOutputNode**](https://msdn.microsoft.com/library/windows/apps/dn914166), вызвав [**CreateFrameOutputNode**](https://msdn.microsoft.com/library/windows/apps/dn914233).

[!code-cs[DeclareFrameOutputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetDeclareFrameOutputNode)]

[!code-cs[CreateFrameOutputNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetCreateFrameOutputNode)]

Событие [**AudioGraph.QuantumProcessed**](https://msdn.microsoft.com/library/windows/apps/dn914240) возникает, когда звуковой граф завершит обработку кванта времени звуковых данных. Вы можете получить доступ к звуковым данным в обработчике для этого события.

[!code-cs[QuantumProcessed](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetQuantumProcessed)]

-   Вызовите метод [**GetFrame**](https://msdn.microsoft.com/library/windows/apps/dn914171), чтобы получить объект [**AudioFrame**](https://msdn.microsoft.com/library/windows/apps/dn930871), заполненный звуковыми данными из графа.
-   Ниже приведен пример реализации вспомогательного метода **ProcessFrameOutput**.

[!code-cs[ProcessFrameOutput](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetProcessFrameOutput)]

-   Как и в приведенном выше примере узла ввода аудиокадра, потребуется объявить COM-интерфейс **IMemoryBufferByteAccess** и настроить проект на разрешение небезопасного кода, чтобы получить доступ к базовому звуковому буферу.
-   Получите [**AudioBuffer**](https://msdn.microsoft.com/library/windows/apps/dn958454) аудиокадра, вызвав [**LockBuffer**](https://msdn.microsoft.com/library/windows/apps/dn930878).
-   Получите экземпляр COM-интерфейса **IMemoryBufferByteAccess** из звукового буфера путем вызова метода [**CreateReference**](https://msdn.microsoft.com/library/windows/apps/dn958457)..
-   Получите указатель на необработанные данные звукового буфера, вызвав метод **IMemoryBufferByteAccess.GetBuffer**, и приведите его к типу примеров звуковых данных.

## Соединения узлов и узлы субмикширования

Узлы ввода всех типов обеспечивают метод **AddOutgoingConnection**, направляющий созданные узлом звуковые данные на узел, который передается в метод. В следующем примере класс [**AudioFileInputNode**](https://msdn.microsoft.com/library/windows/apps/dn914108) подключается к классу [**AudioDeviceOutputNode**](https://msdn.microsoft.com/library/windows/apps/dn914098), что представляет собой простой сценарий воспроизведения звукового файла на динамике устройства.

[!code-cs[AddOutgoingConnection1](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetAddOutgoingConnection1)]

Вы можете создать несколько соединений между узлом ввода и другими узлами. В следующем примере добавляется другое соединение между классами [**AudioFileInputNode**](https://msdn.microsoft.com/library/windows/apps/dn914108) и [**AudioFileOutputNode**](https://msdn.microsoft.com/library/windows/apps/dn914133). Теперь звук из звукового файла воспроизводится на динамике устройства, а также записывается в звуковой файл.

[!code-cs[AddOutgoingConnection2](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetAddOutgoingConnection2)]

Узлы вывода также могут получать несколько соединений с других узлов. В следующем примере создается соединение между классом [**AudioDeviceInputNode**](https://msdn.microsoft.com/library/windows/apps/dn914082) и узлом [**AudioDeviceOutput**](https://msdn.microsoft.com/library/windows/apps/dn914098). Так как на узле вывода есть соединения от узлов ввода файла и устройства, выходные данные будут включать содержимое из обоих источников. Метод **AddOutgoingConnection** обеспечивает перегрузку, которая позволяет задать значение усиления для сигнала, проходящего через соединение.

[!code-cs[AddOutgoingConnection3](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetAddOutgoingConnection3)]

Хотя узлы вывода могут принимать соединения из нескольких узлов, вы можете создать промежуточный набор сигналов из одного или нескольких узлов, прежде чем передать конечный набор для вывода. Например, вы можете установить уровень или применить эффекты к подмножеству звуковых сигналов в графе. Для этого можно воспользоваться элементом [**AudioSubmixNode**](https://msdn.microsoft.com/library/windows/apps/dn914247). Вы можете подключиться к узлу субмикширования из одно или нескольких узлов ввода или других узлов субмикширования. В следующем примере новый узел субмикширования создается с помощью метода [**AudioGraph.CreateSubmixNode**](https://msdn.microsoft.com/library/windows/apps/dn914236). Затем в узел субмикширования добавляются соединения из узлов ввода файла и аудиокадра. Наконец, узел субмикширования подключается к узлу вывода файла.

[!code-cs[CreateSubmixNode](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetCreateSubmixNode)]

## Запуск и остановка узлов звукового графа

При вызове метода [**AudioGraph.Start**](https://msdn.microsoft.com/library/windows/apps/dn914244) звуковой граф начинает обработку звуковых данных. Каждый тип узла обеспечивает методы **Start** и **Stop**, которые отвечают за запуск или остановку обработки данных на отдельных узлах. При вызове метода [**AudioGraph.Stop**](https://msdn.microsoft.com/library/windows/apps/dn914245) прекращается обработка всех звуковых данных на всех узлах независимо от состояния отдельных узлов, но даже в режиме остановки звукового графа вы можете задать состояние каждого узла. Например, когда граф остановлен, вы можете вызывать метод **Stop** на отдельном узле, а затем вызвать метод **AudioGraph.Start**. При этом отдельный узел не возобновит работу.

Все типы узла предоставляют свойство **ConsumeInput**, которое (если для него установлено значение «false») позволяет узлу продолжить обработку звуковых данных, однако останавливает ввод данных из других узлов.

Все типы узла предоставляют метод **Reset**, который обеспечивает удаление на узле всех звуковых данных, находящихся в его буфере.

## Добавление звуковых эффектов

API звукового графа позволяет добавить звуковые эффекты в узлы всех типов в графе. Каждый из узлов ввода, узлов вывода и узлов субмикширования может включать неограниченное количество звуковых эффектов, которое определяется исключительно возможностями оборудования. В следующем примере показано, как добавить встроенный эффект эха на узел субмикширования.

[!code-cs[AddEffect](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetAddEffect)]

-   Все звуковые эффекты реализуют интерфейс [**IAudioEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn608044). Каждый узел предоставляет свойство **EffectDefinitions**, представляющее список эффектов, которые применяются к этому узлу. Эффект добавляется путем внесения его объекта определения в список.
-   Пространство имен **Windows.Media.Audio** включает несколько классов определений эффектов. К ним относятся следующие:
    -   [**EchoEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn914276)
    -   [**EqualizerEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn914287)
    -   [**LimiterEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn914306)
    -   [**ReverbEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn914313)
-   Вы можете создать собственные звуковые эффекты, реализующие интерфейс [**IAudioEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn608044), и применить их к любому узлу в звуковом графе.
-   Каждый тип узла предоставляет метод **DisableEffectsByDefinition**, который отключает все эффекты в списке **EffectDefinitions** узла, добавленные с помощью указанного определения. Метод **EnableEffectsByDefinition** включает эффекты с указанным определением.

 

 






<!--HONumber=Mar16_HO1-->


