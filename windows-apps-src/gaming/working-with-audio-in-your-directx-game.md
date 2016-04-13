---
title: Звук для игр
description: Благодаря сведениям в этом разделе вы научитесь создавать и встраивать музыку и звуки в игры DirectX, а также обрабатывать звуковые сигналы для создания динамичного и позиционного звука.
ms.assetid: ab29297a-9588-c79b-24c5-3b94b85e74a8
---

# Звук для игр


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Благодаря сведениям в этом разделе вы научитесь создавать и встраивать музыку и звуки в игры DirectX, а также обрабатывать звуковые сигналы для создания динамичного и позиционного звука.

Мы рекомендуем пользоваться библиотекой XAudio2 в DirectX для звукового программирования способом, описанным в этом разделе. XAudio2 является библиотекой звуковых файлов низкого уровня, которая обеспечивает обработку сигналов и микширование звука для игр; она поддерживает широкий диапазон форматов.

Вы также можете импортировать простые звуки и музыку с помощью [Microsoft Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms694197). Microsoft Media Foundation разработана для проигрывания аудио- и видео- файлов и потоков мультимедиа; она будет особенно полезной для кинематографических сцен или неинтерактивных компонентов вашей игры.

## Краткий обзор основных понятий


Ниже представлены несколько основных понятий в сфере звукового программирования, которые мы используем в данном разделе.

-   Сигналы являются основной единицей звукового программирования, аналогично пикселю в графическом программировании. Цифровые процессоры сигналов (ЦПС), которые их обрабатывают при работе со звуком для игры, соответствуют построителям текстуры. Они могут преобразовывать сигналы, сочетать их или фильтровать. Программируя ЦПС, вы можете изменять звуковые и музыкальные эффекты вашей игры с необходимой степенью сложности.
-   Голоса являются комбинацией двух или более сигналов. Существует три типа голосовых объектов XAudio2: исходные, комбинированные и обработанные. Исходные голосовые объекты берутся из звуковых данных, предоставленных клиентом. Данные исходных и комбинированных голосов используются для получения одного или более комбинированных или обработанных голосов. Комбинированные и обработанные голоса смешивают звуковые сигналы из всех голосов, чьи данные они получают, и работают с итоговым звуком. Обработанные голоса записываются в виде звуковых данных на аудиоустройство.
-   Микширование — это процесс сведения нескольких дискретных голосов, например звуковых эффектов и фонового звука, проигрываемого во время сцены, в один звуковой поток. Субмикширование — процесс сочетания нескольких дискретных сигналов, например разных звуков работающего двигателя, и создания голоса.
-   Форматы аудио Музыкальные и звуковые эффекты для вашей игры могут храниться в различных цифровых форматах. Существуют несжатые форматы, например WAV, и сжатые, например MP3 и OGG. Чем больше сжат образец (это обычно определяется скоростью его цифрового потока — чем ниже скорость, тем больше теряется при сжатии), тем хуже его качество. Качество может меняться из-за схем сжатия и скорости цифрового потока, поэтому экспериментируйте с форматами, чтобы узнать, какой из них лучше всего подходит вашей игре.
-   Частота дискретизации и качество Звуки могут быть записаны при разной частоте дискретизации. Звуки, записанные при более низкой частоте, характеризуются заметно худшим качеством. Частота дискретизации для качества CD — 44,1 кГц (44100 Гц). Если вам не нужен звук высокого качества, вы можете выбрать более низкую частоту дискретизации. Более высокие частоты лучше подходят для работы в профессиональных приложениях для воспроизведения аудио, но, возможно, вам они не потребуются, если в вашей игре не нужен звук профессионального качества.
-   Генераторы (или источники) звука В XAudio2 источником звука является место, из которого исходит звук, будь то просто короткий фоновый шум или сложная рок-композиция, проигрываемая музыкальным автоматом в игре. Вы определяете источники звука, вводя их координаты в игровом мире.
-   Прослушиватели звука Прослушивателем звука часто является игрок, или, возможно, компонент приложения в более сложной игре, который обрабатывает звук, полученный от прослушивателя. Вы можете субмикшировать этот звук в аудиопоток, чтобы воспроизвести игроку или использовать его в игре, например, для пробуждения в приложении стражника, отмеченного как прослушиватель.

## Вопросы разработки


Звук является очень важной частью разработки игры. Многие игроки легко назовут не одну заурядную игру, получившую легендарную известность только благодаря запоминающейся музыке, великолепному озвучиванию, микшированию звука или общему звуковому оформлению. Музыка и звук определяют индивидуальность игры, образуют основной мотив, который выделяет игру из разряда других похожих игр, делая ее узнаваемой. Усилия, затраченные вами на разработку звукового оформления игры, обязательно окупятся.

Позиционный трехмерный звук может дать ощущение погружения в игру в дополнение к ощущениям, подаренным трехмерной графикой. Если вы разрабатываете сложную игру, которая симулирует реальную жизнь или требует выполнения в кинематографическом стиле, подумайте об использовании трехмерных позиционных звуковых методов, которые реально способны захватить игрока.

## Схема разработки аудио в DirectX


### Ресурсы с основными понятиями о XAudio2

XAudio2 является библиотекой микширования звука для DirectX, предназначенной в основном для разработки высокопроизводительных звуковых модулей для игр. Разработчикам игр, желающим добавить в свои современные игры звуковые эффекты и фоновую музыку, XAudio2 предлагает модуль обработки и микширования звука с низкой задержкой и поддержкой динамических буферов, синхронным образцово-точным воспроизведением и неявным преобразованием производительности источника.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Раздел</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[Introduction to XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415813)</p></td>
<td align="left"><p>В разделе представлен перечень возможностей программирования звука, поддерживаемого XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Getting Started with XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415762)</p></td>
<td align="left"><p>В разделе представлены сведения по ключевым понятиям XAudio2, версиям XAudio2 и формату аудио RIFF.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Common Audio Programming Concepts](https://msdn.microsoft.com/library/windows/desktop/ee415692)</p></td>
<td align="left"><p>В разделе представлен обзор общих понятий аудио, с которыми должен быть знаком разработчик аудио.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[XAudio2 Voices](https://msdn.microsoft.com/library/windows/desktop/ee415825)</p></td>
<td align="left"><p>В данном разделе содержится обзор речи в XAudio2, используемой для субмикширования звуковых данных, а также оперирования и управления ими.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[XAudio2 Callbacks](https://msdn.microsoft.com/library/windows/desktop/ee415745)</p></td>
<td align="left"><p>В разделе описаны обратные вызовы в XAudio 2, используемые для предотвращения прерывания воспроизведения звука.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[XAudio2 Audio Graphs](https://msdn.microsoft.com/library/windows/desktop/ee415739)</p></td>
<td align="left"><p>В разделе представлены схемы обработки звука в XAudio2, использующие набор клиентских звуковых потоков в качестве входящих данных, обрабатывающие их и выдающие результат обработки на аудиоустройство.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[XAudio2 Audio Effects](https://msdn.microsoft.com/library/windows/desktop/ee415756)</p></td>
<td align="left"><p>В разделе описаны звуковые эффекты в XAudio2, использующие входящие звуковые данные для выполнения определенных операций над ними (например, добавления эффекта реверберации) с последующей передачей далее.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Streaming Audio Data with XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415821)</p></td>
<td align="left"><p>В разделе описана потоковая передача звука при помощи XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[X3DAudio](https://msdn.microsoft.com/library/windows/desktop/ee415714)</p></td>
<td align="left"><p>В разделе описан X3DAudio, API, используемый совместно с XAudio2 для создания иллюзии объемного звучания.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[XAudio2 Programming Reference](https://msdn.microsoft.com/library/windows/desktop/ee415899)</p></td>
<td align="left"><p>Этот раздел представляет собой полный справочник по API XAudio2.</p></td>
</tr>
</tbody>
</table>

 

### Ресурсы, содержащие руководства по работе с XAudio2

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Раздел</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[How to: Initialize XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415779)</p></td>
<td align="left"><p>Узнайте, как инициализировать XAudio2 для воспроизведения звука путем создания экземпляра модуля XAudio2 и создания управляющей речи.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Load Audio Data Files in XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415781)</p></td>
<td align="left"><p>Узнайте, как заполнять структуры, необходимые для воспроизведения звуковых данных в XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Play a Sound with XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415787)</p></td>
<td align="left"><p>Узнайте, как воспроизвести предварительно загруженные звуковые данные в XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Use Submix Voices](https://msdn.microsoft.com/library/windows/desktop/ee415794)</p></td>
<td align="left"><p>Узнайте, как организовать речевые группы для отправки выходящего результата в один субмикшированный голосовой канал.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Use Source Voice Callbacks](https://msdn.microsoft.com/library/windows/desktop/ee415769)</p></td>
<td align="left"><p>Узнайте, как использовать обратные вызовы речевых источников в XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Use Engine Callbacks](https://msdn.microsoft.com/library/windows/desktop/ee415774)</p></td>
<td align="left"><p>Узнайте, как использовать обратные вызовы модуля в XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Build a Basic Audio Processing Graph](https://msdn.microsoft.com/library/windows/desktop/ee415767)</p></td>
<td align="left"><p>Узнайте, как создать схему обработки звука на основе единой управляющей речи и единого речевого источника.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Dynamically Add or Remove Voices From an Audio Graph](https://msdn.microsoft.com/library/windows/desktop/ee415772)</p></td>
<td align="left"><p>Узнайте, как добавить или удалить тембр субмикширования из графа, созданного в ходе реализации этапа [How to: Build a Basic Audio Processing Graph](https://msdn.microsoft.com/library/windows/desktop/ee415767).</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Create an Effect Chain](https://msdn.microsoft.com/library/windows/desktop/ee415789)</p></td>
<td align="left"><p>Узнайте, как применить цепь эффектов к речи для обеспечения возможности специальной обработки звуковых данных такой речи.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Create an XAPO](https://msdn.microsoft.com/library/windows/desktop/ee415730)</p></td>
<td align="left"><p>Узнайте, как реализовать [<strong>IXAPO</strong>](https://msdn.microsoft.com/library/windows/desktop/ee415893) для создания объекта звуковой обработки XAudio2 (XAPO).</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Add Run-time Parameter Support to an XAPO](https://msdn.microsoft.com/library/windows/desktop/ee415728)</p></td>
<td align="left"><p>Узнайте, как добавить в XAPO поддержку параметра времени выполнения за счет реализации интерфейса [<strong>IXAPOParameters</strong>](https://msdn.microsoft.com/library/windows/desktop/ee415896).</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Use an XAPO in XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415733)</p></td>
<td align="left"><p>Узнайте, как применять эффект, реализованный как XAPO, в цепи эффектов XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Use XAPOFX in XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415723)</p></td>
<td align="left"><p>Узнайте, как применять один из эффектов в составе XAPOFX в цепи эффектов XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Stream a Sound from Disk](https://msdn.microsoft.com/library/windows/desktop/ee415791)</p></td>
<td align="left"><p>Узнайте, как организовать поток звуковых данных в XAudio2 путем создания отдельного потока для чтения звукового буфера и применения обратных вызовов для управления данным потоком.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Integrate X3DAudio with XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415798)</p></td>
<td align="left"><p>Узнайте, как применять X3DAudio, чтобы получить значения объема и высоты основного тона для речевого сигнала XAudio2, а также параметры для встроенного эффекта реверберации XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Group Audio Methods as an Operation Set](https://msdn.microsoft.com/library/windows/desktop/ee415783)</p></td>
<td align="left"><p>Узнайте, как применять наборы операций XAudio2 для одновременного действия группы вызовов методов.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Debugging Audio Glitches in XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415765)</p></td>
<td align="left"><p>Узнайте, как задать уровень ведения журнала отладки для XAudio2.</p></td>
</tr>
</tbody>
</table>

 

### Ресурсы Media Foundation

Media Foundation (MF) — это платформа мультимедиа для потокового воспроизведения звука и видео. API Media Foundation могут применяться для организации потоков звука и видео, закодированных и сжатых при помощи различных алгоритмов. Данная платформа не предназначена для отработки игровых сценариев в реальном времени; она обеспечивает мощные инструменты и обширную поддержку шифровки и дешифровки для более линейных видов записи и воспроизведения компонентов звука и видео.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Раздел</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[About Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms696274)</p></td>
<td align="left"><p>В разделе содержатся общие сведения об API Media Foundation и инструментах их поддержки.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Media Foundation: Essential Concepts](https://msdn.microsoft.com/library/windows/desktop/ee663601)</p></td>
<td align="left"><p>В разделе представлены некоторые понятия, понимание которых необходимо для написания приложения на платформе Media Foundation.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Media Foundation Architecture](https://msdn.microsoft.com/library/windows/desktop/ms696219)</p></td>
<td align="left"><p>В разделе описана общая структура Microsoft Media Foundation, а также используемые данной платформой базисные элементы мультимедиа и схема обработки данных.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Audio/Video Capture](https://msdn.microsoft.com/library/windows/desktop/dd317910)</p></td>
<td align="left"><p>В разделе описано применение Microsoft Media Foundation для записи звука и видео.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Audio/Video Playback](https://msdn.microsoft.com/library/windows/desktop/dd317914)</p></td>
<td align="left"><p>В разделе описана реализация воспроизведения звука/видео в вашем приложении.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Supported Media Formats in Media Foundation](https://msdn.microsoft.com/library/windows/desktop/dd757927)</p></td>
<td align="left"><p>В разделе содержится перечень форматов мультимедиа, которые Microsoft Media Foundation поддерживает изначально. (Сторонние поставщики могут обеспечивать поддержку дополнительных форматов за счет написания специальных подключаемых модулей.)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Encoding and File Authoring](https://msdn.microsoft.com/library/windows/desktop/dd318778)</p></td>
<td align="left"><p>В разделе описано применение Microsoft Media Foundation для кодировки звука и видео, а также разработки файлов мультимедиа.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Windows Media Codecs](https://msdn.microsoft.com/library/windows/desktop/ff819508)</p></td>
<td align="left"><p>В разделе описаны возможности применения аудио- и видеокодеков Windows Media для организации и использования сжатых потоков данных.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Media Foundation Programming Reference](https://msdn.microsoft.com/library/windows/desktop/ms704847)</p></td>
<td align="left"><p>В разделе содержится справочная информация по API Media Foundation.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Media Foundation SDK Samples](https://msdn.microsoft.com/library/windows/desktop/aa371827)</p></td>
<td align="left"><p>В разделе содержится перечень примеров приложений, демонстрирующих методику применения Media Foundation.</p></td>
</tr>
</tbody>
</table>

 

### Типы мультимедиа XAML в среде выполнения Windows

Если вы применяете [межпрограммное взаимодействие DirectX — XAML](https://msdn.microsoft.com/library/windows/apps/hh825871), то можно внедрить API мультимедиа на XAML из среды выполнения Windows в свои приложения Магазина Windows на C++ с использованием DirectX для упрощения игровых сценариев.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Раздел</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[<strong>Windows.UI.Xaml.Controls.MediaElement</strong>](https://msdn.microsoft.com/library/windows/apps/br242926)</p></td>
<td align="left"><p>Элемент XAML, представляющий объект, содержащий звук, видео либо оба типа мультимедиа.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Audio, video, and camera](https://msdn.microsoft.com/library/windows/apps/mt203788)</p></td>
<td align="left"><p>Узнайте, как встраивать базовое аудио и видео в приложения универсальной платформы для Windows (UWP).</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[MediaElement](https://msdn.microsoft.com/library/windows/apps/mt187272)</p></td>
<td align="left"><p>Узнайте, как воспроизводить локально хранящийся файл мультимедиа в приложении UWP.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[MediaElement](https://msdn.microsoft.com/library/windows/apps/mt187272)</p></td>
<td align="left"><p>Узнайте, как выполнять потоковую передачу файла мультимедиа с низкой задержкой в приложении UWP.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Media casting](https://msdn.microsoft.com/library/windows/apps/mt282143)</p></td>
<td align="left"><p>Узнайте, как использовать контракт «Воспроизведение на устройстве» для потоковой передачи мультимедиа из приложения UWP на другое устройство.</p></td>
</tr>
</tbody>
</table>

 

## Справочные материалы


-   [Введение в XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415813)
-   [Руководство по программированию для XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415737)
-   [Общие сведения о Microsoft Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms694197)

> **Примечание**  
Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). При разработке приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 

## Связанные разделы


-   [Руководство по программированию для XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415737)

 

 






<!--HONumber=Mar16_HO1-->


