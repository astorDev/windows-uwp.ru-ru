---
author: drewbatgit
Description: This article describes how to create a Windows Runtime component that implements the IBasicAudioEffect interface to allow you to create custom effects for audio streams.
title: Пользовательские звуковые эффекты
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 360faf3f-7e73-4db4-8324-3391f801d827
ms.localizationpriority: medium
ms.openlocfilehash: a9c62dac489be07a08acc4ad17ecb79bcc54f57a
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7445265"
---
# <a name="custom-audio-effects"></a>Пользовательские звуковые эффекты

В этой статье описано, как создать компонент среды выполнения Windows, реализующий интерфейс [**IBasicAudioEffect**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.IBasicAudioEffect) для создания пользовательских эффектов для аудиопотоков. Пользовательские эффекты можно использовать с помощью нескольких различных API среды выполнения Windows, включая [MediaCapture](https://msdn.microsoft.com/library/windows/apps/br241124), который предоставляет доступ к камере устройства, [**MediaComposition**](https://msdn.microsoft.com/library/windows/apps/dn652646), который позволяет создавать сложные композиции из мультимедийных клипов, и [**AudioGraph**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Audio.AudioGraph), который позволяет быстро собрать граф различных узлов звукового ввода, вывода и субмикширования.

## <a name="add-a-custom-effect-to-your-app"></a>Добавление пользовательского эффекта в приложение


Пользовательский аудиоэффект определяется в классе, реализующем интерфейс [**IBasicAudioEffect**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.IBasicAudioEffect). Этот класс невозможно включить непосредственно в проект приложения. Вместо этого необходимо использовать компонент среды выполнения Windows для размещения вашего класса аудиоэффекта.

**Добавление компонента среды выполнения Windows для вашего аудиоэффекта**

1.  Открыв свое решение в Microsoft Visual Studio, перейдите в меню **Файл** и выберите пункт **Добавить-&gt;Новый проект**.
2.  Выберите тип проекта **Компонент среды выполнения Windows (универсальное приложение)**.
3.  Для соответствия этому примеру задайте проекту название *AudioEffectComponent*. Это название будет использоваться в коде далее.
4.  Нажмите кнопку **ОК**.
5.  Шаблон проекта создает класс под названием Class1.cs. В **Обозревателе решений** щелкните правой кнопкой мыши по значку для Class1.cs и выберите **Переименовать**.
6.  Переименуйте файл в *ExampleAudioEffect.cs*. Visual Studio отобразит уведомление с вопросом о том, не следует ли обновить все упоминания с использованием нового имени. Щелкните **Да**.
7.  Откройте **ExampleAudioEffect.cs** и дополните определение класса для реализации интерфейса [**IBasicAudioEffect**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.IBasicAudioEffect).


[!code-cs[ImplementIBasicAudioEffect](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetImplementIBasicAudioEffect)]

Для получения доступа ко всем типам, используемым в примерах в этой статье, необходимо добавить следующие пространства имен в файл класса эффекта.

[!code-cs[EffectUsing](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetEffectUsing)]

## <a name="implement-the-ibasicaudioeffect-interface"></a>Реализация интерфейса IBasicAudioEffect

Ваш аудиоэффект должен реализовывать все методы и свойства интерфейса [**IBasicAudioEffect**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.IBasicAudioEffect). Этот раздел содержит простую пошаговую реализацию этого интерфейса для создания базового эффекта эха.

### <a name="supportedencodingproperties-property"></a>Свойство SupportedEncodingProperties

Система проверяет свойство [**SupportedEncodingProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.IBasicAudioEffect.SupportedEncodingProperties), чтобы определить, какие свойства кодирования поддерживаются вашим эффектом. Обратите внимание, что если потребитель вашего эффекта не может кодировать аудио с использованием указанных вами свойств, система вызовет метод [**Close**](https://msdn.microsoft.com/library/windows/apps/dn764782) на вашем эффекте и удалит этот эффект из аудиоконвейера. В этом примере объекты [**AudioEncodingProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaProperties.AudioEncodingProperties) создаются и добавляются в возвращенный список для поддержки кодирования со следующими параметрами: 44,1 кГц и 48 кГц, 32-разрядное число с плавающей точкой, моно.

[!code-cs[SupportedEncodingProperties](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetSupportedEncodingProperties)]

### <a name="setencodingproperties-method"></a>Метод SetEncodingProperties

Система вызывает [**SetEncodingProperties**](https://msdn.microsoft.com/library/windows/apps/dn919884) на эффекте, чтобы предоставить вам информацию о свойствах кодирования для аудиопотока, на котором работает эффект. Чтобы реализовать эффект эха, в этом примере используется буфер для хранения одной секунды звуковых данных. Этот метод также предоставляет возможность задать размер буфера в зависимости от количества отсчетов в одной секунде аудио, то есть в зависимости от частоты дискретизации, с которой закодирован звук. Эффект задержки также использует счетчик целых чисел для отслеживания текущей позиции в буфере задержки. Так как **SetEncodingProperties** вызывается каждый раз, когда эффект добавляется в звуковой конвейер, это будет подходящим моментом для инициализации этого значения с установкой на 0. Возможно, вам также потребуется сохранить объект **AudioEncodingProperties**, переданный в этот метод, чтобы использовать его в каком-либо другом месте в вашем эффекте.

[!code-cs[DeclareEchoBuffer](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetDeclareEchoBuffer)]
[!code-cs[SetEncodingProperties](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetSetEncodingProperties)]


### <a name="setproperties-method"></a>Метод SetProperties

Метод [**SetProperties**](https://msdn.microsoft.com/library/windows/apps/br240986) позволяет использующему ваш эффект приложению настраивать параметры эффекта. Свойства передаются в виде сопоставления имен свойств и значений [**IPropertySet**](https://msdn.microsoft.com/library/windows/apps/br226054).

[!code-cs[SetProperties](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetSetProperties)]

В этом простом примере производится микширование текущего отсчета аудио со значением из буфера задержки в соответствии со значением свойства **Mix**. Свойство объявляется, и TryGetValue используется для получения значения, заданного вызывающим приложением. Если значение не задано, по умолчанию используется значение 0,5. Обратите внимание, что это свойство доступно только для чтения. Необходимо задать значение свойства с помощью **SetProperties**.

[!code-cs[MixProperty](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetMixProperty)]

### <a name="processframe-method"></a>Метод ProcessFrame

Именно в методе [**ProcessFrame**](https://msdn.microsoft.com/library/windows/apps/dn764784) ваш эффект изменяет звуковые данные аудиопотока. Метод вызывается один раз на кадр, и ему передается объект [**ProcessAudioFrameContext**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.ProcessAudioFrameContext). Этот объект содержит входной объект [**AudioFrame**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.AudioFrame), содержащий входящий кадр, который требуется обработать, и выходной объект **AudioFrame**, в который вы записываете звуковые данные, которые затем будут переданы остальным компонентам аудиоконвейера. Аудиокадр — это буфер звуковых отсчетов, представляющий короткий фрагмент звуковых данных.

Для доступа к буферу данных **AudioFrame** требуется COM-взаимодействие, поэтому необходимо включить пространство имен **System.Runtime.InteropServices** в ваш файл класса эффекта и затем добавить следующий код в пространство имен вашего эффекта для импорта интерфейса доступа к аудиобуферу.

[!code-cs[ComImport](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetComImport)]

> [!NOTE]
> Поскольку этот метод получает доступ к необработанному буферу изображений в его исходной форме, необходимо в настройках проекта разрешить использование небезопасного кода.
> 1.  В обозревателе решений щелкните правой кнопкой мыши по проекту AudioEffectComponent и выберите **Свойства**.
> 2.  Выберите вкладку **Сборка**.
> 3.  Установите флажок **Разрешить небезопасный код**.

 

Теперь вы можете добавить реализацию метода **ProcessFrame** в свой эффект. Сначала этот метод получает объект [**AudioBuffer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.AudioBuffer) от аудиокадров и ввода, и вывода. Обратите внимание, что кадр вывода открывается для записи, а кадр ввода — для чтения. Далее выполняется получение [**IMemoryBufferReference**](https://msdn.microsoft.com/library/windows/apps/dn921671) для каждого буфера путем вызова [**CreateReference**](https://msdn.microsoft.com/library/windows/apps/dn949046). Затем производится получение фактических данных буфера путем преобразования объектов **IMemoryBufferReference** в описанный выше интерфейс COM-взаимодействия **IMemoryByteAccess** и последующего вызова **GetBuffer**.

Теперь, когда буферы данных получены, можно выполнять чтение из буфера ввода и запись в буфер вывода. Для каждого отсчета в inputbuffer значение извлекается и умножается на 1 - **Mix**, чтобы задать исходное значение сигнала эффекта. Далее отсчет извлекается из текущей позиции в буфере эха и умножается на **Mix**, чтобы задать измененное значение эффекта. Отсчет на выводе задается равным сумме исходного и измененного значений. Наконец, каждый отсчет ввода сохраняется в буфер эха, и текущий индекс отсчета увеличивается.

[!code-cs[ProcessFrame](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetProcessFrame)]



### <a name="close-method"></a>Метод Close

Система вызывает метод [**Close**](https://msdn.microsoft.com/library/windows/apps/dn764782) [**Close**](https://msdn.microsoft.com/library/windows/apps/dn764782) для вашего класса, когда соответствующий эффект должен будет завершить работу. Этот метод следует использовать для утилизации всех созданных вами ресурсов. Аргументом этого метода является [**MediaEffectClosedReason**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.MediaEffectClosedReason), и он позволяет узнать, был ли эффект закрыт в нормальном режиме либо произошла ошибка, либо эффект не поддерживает необходимый формат кодирования.

[!code-cs[Close](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetClose)]

### <a name="discardqueuedframes-method"></a>Метод DiscardQueuedFrames

Метод [**DiscardQueuedFrames**](https://msdn.microsoft.com/library/windows/apps/dn764790) вызывается, когда ваш эффект необходимо сбросить. Типовой сценарий такой ситуации — ваш эффект хранит ранее обработанные кадры для использования в обработке текущего кадра. При вызове этого метода необходимо утилизировать набор предыдущих сохраненных вами кадров. Этот метод можно использовать для сброса любого состояния, связанного с предыдущими кадрами, а не только с накопленными аудиокадрами.

[!code-cs[DiscardQueuedFrames](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetDiscardQueuedFrames)]

### <a name="timeindependent-property"></a>Свойство TimeIndependent

Свойство TimeIndependent [**TimeIndependent**](https://msdn.microsoft.com/library/windows/apps/dn764803) позволяет системе узнать, требуется ли вашему эффекту однородная синхронизация. Если установлено значение true, система может использовать оптимизации, которые улучшают работу эффекта.

[!code-cs[TimeIndependent](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetTimeIndependent)]

### <a name="useinputframeforoutput-property"></a>Свойство UseInputFrameForOutput

Задайте свойству [**UseInputFrameForOutput**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.IBasicAudioEffect.UseInputFrameForOutput) значение **true**, чтобы сообщить системе, что ваш эффект будет записывать свой вывод в аудиобуфер [**InputFrame**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.ProcessAudioFrameContext.InputFrame) контекста [**ProcessAudioFrameContext**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.ProcessAudioFrameContext), переданный в [**ProcessFrame**](https://msdn.microsoft.com/library/windows/apps/dn764784), вместо записи в [**OutputFrame**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.ProcessAudioFrameContext.OutputFrame). 

[!code-cs[UseInputFrameForOutput](./code/AudioGraph/AudioEffectComponent/ExampleAudioEffect.cs#SnippetUseInputFrameForOutput)]


## <a name="adding-your-custom-effect-to-your-app"></a>Добавление пользовательского эффекта в приложение


Для использования вашего аудиоэффекта из приложения необходимо добавить ссылку на проект эффекта в приложение.

1.  В обозревателе решений внутри проекта приложения щелкните правой кнопкой мыши по разделу **Ссылки** и выберите **Добавить ссылку**.
2.  Разверните вкладку **Проекты**, выберите **Решение** и установите флажок для имени вашего проекта эффекта. В этом примере используется имя *AudioEffectComponent*.
3.  Нажмите **ОК**

Если ваш класс звукового эффекта объявлен в другом пространстве имен, не забудьте включить это пространство имен в свой файл кода.

[!code-cs[UsingAudioEffectComponent](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetUsingAudioEffectComponent)]

### <a name="add-your-custom-effect-to-an-audiograph-node"></a>Добавьте ваш пользовательский эффект в узел AudioGraph
Общие сведения об использовании звуковых графов см. в разделе [Звуковые графы](audio-graphs.md). В следующем фрагменте кода показано, как добавить созданный этой статье в качестве примера эффект эха в узел звукового графа. Во-первых, создается [**PropertySet**](https://msdn.microsoft.com/library/windows/apps/Windows.Foundation.Collections.PropertySet), и задается значение для свойства **Mix**, определяемое эффектом. Далее вызывается конструктор [**AudioEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Effects.AudioEffectDefinition) с передачей полного имени класса типа пользовательского эффекта и набора свойств. Наконец, определение эффекта добавляется в свойство [**EffectDefinitions**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Audio.AudioFileInputNode.EffectDefinitions) существующего узла [**FileInputNode**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Audio.CreateAudioFileInputNodeResult.FileInputNode), что приводит к обработке издаваемого звука пользовательским эффектом. 

[!code-cs[AddCustomEffect](./code/AudioGraph/cs/MainPage.xaml.cs#SnippetAddCustomEffect)]

После его добавления в узел пользовательский эффект можно отключить путем вызова [**DisableEffectsByDefinition**](https://msdn.microsoft.com/library/windows/apps/dn958480) и передачи объекта **AudioEffectDefinition**. Дополнительные сведения об использовании звуковых графов в приложении см. в разделе [AudioGraph](audio-graphs.md).

### <a name="add-your-custom-effect-to-a-clip-in-a-mediacomposition"></a>Добавление настраиваемого эффекта в клип с помощью MediaComposition

В следующем фрагменте кода показано, как добавить пользовательский звуковой эффект в видеоклип и фоновую аудиодорожку в мультимедийной композиции. Общие инструкции по созданию мультимедийных композиций из видеоклипов и добавлению фоновых аудиодорожек см. в разделе [Мультимедийные композиции и редактирование](media-compositions-and-editing.md).

[!code-cs[AddCustomAudioEffect](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetAddCustomAudioEffect)]



## <a name="related-topics"></a>Смежные разделы
* [Простой доступ к просмотру камеры](simple-camera-preview-access.md)
* [Создание и редактирование композиций мультимедиа](media-compositions-and-editing.md)
* [Документация по Win2D](http://go.microsoft.com/fwlink/p/?LinkId=519078)
* [Воспроизведение мультимедиа](media-playback.md)

 



