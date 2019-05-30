---
title: Звук для игр
description: Узнайте, как создавать и встраивать музыку и звуки в игры DirectX, а также обрабатывать звуковые сигналы для создания динамичного и позиционного звука.
ms.assetid: ab29297a-9588-c79b-24c5-3b94b85e74a8
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, звук, directx
ms.localizationpriority: medium
ms.openlocfilehash: fa90b22e2661a748454231fea8838bb51b3c621c
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66367312"
---
# <a name="audio-for-games"></a>Звук для игр



Узнайте, как создавать и встраивать музыку и звуки в игры DirectX, а также обрабатывать звуковые сигналы для создания динамичного и позиционного звука.

Мы рекомендуем пользоваться библиотекой XAudio2 в DirectX для звукового программирования способом, описанным в этом разделе. XAudio2 является библиотекой звуковых файлов низкого уровня, которая обеспечивает обработку сигналов и микширование звука для игр; она поддерживает широкий диапазон форматов.

Вы также можете импортировать простые звуки и музыку с помощью [Microsoft Media Foundation](https://docs.microsoft.com/windows/desktop/medfound/microsoft-media-foundation-sdk). Microsoft Media Foundation разработана для проигрывания аудио- и видео- файлов и потоков мультимедиа; она будет особенно полезной для кинематографических сцен или неинтерактивных компонентов вашей игры.

## <a name="concepts-at-a-glance"></a>Краткий обзор основных понятий


Ниже представлены несколько основных понятий в сфере звукового программирования, которые мы используем в данном разделе.

-   Сигналы являются основной единицей звукового программирования, аналогично пикселю в графическом программировании. Цифровые процессоры сигналов (ЦПС), которые их обрабатывают при работе со звуком для игры, соответствуют построителям текстуры. Они могут преобразовывать сигналы, сочетать их или фильтровать. Программируя ЦПС, вы можете изменять звуковые и музыкальные эффекты вашей игры с необходимой степенью сложности.
-   Голоса являются комбинацией двух или более сигналов. Существует три типа голосовых объектов XAudio2: исходные, комбинированные и обработанные. Исходные голосовые объекты берутся из звуковых данных, предоставленных клиентом. Данные исходных и комбинированных голосов используются для получения одного или более комбинированных или обработанных голосов. Комбинированные и обработанные голоса смешивают звуковые сигналы из всех голосов, чьи данные они получают, и работают с итоговым звуком. Обработанные голоса записываются в виде звуковых данных на аудиоустройство.
-   Микширование — это процесс сведения нескольких дискретных голосов, например звуковых эффектов и фонового звука, проигрываемого во время сцены, в один звуковой поток. Субмикширование — процесс сочетания нескольких дискретных сигналов, например разных звуков работающего двигателя, и создания голоса.
-   Форматы аудио Музыкальные и звуковые эффекты для вашей игры могут храниться в различных цифровых форматах. Существуют несжатые форматы, например WAV, и сжатые, например MP3 и OGG. Чем больше сжат образец (это обычно определяется скоростью его цифрового потока — чем ниже скорость, тем больше теряется при сжатии), тем хуже его качество. Качество может меняться из-за схем сжатия и скорости цифрового потока, поэтому экспериментируйте с форматами, чтобы узнать, какой из них лучше всего подходит вашей игре.
-   Частота дискретизации и качество Звуки могут быть записаны при разной частоте дискретизации. Звуки, записанные при более низкой частоте, характеризуются заметно худшим качеством. Частота дискретизации для качества CD — 44,1 кГц (44100 Гц). Если вам не нужен звук высокого качества, вы можете выбрать более низкую частоту дискретизации. Более высокие частоты лучше подходят для работы в профессиональных приложениях для воспроизведения аудио, но, возможно, вам они не потребуются, если в вашей игре не нужен звук профессионального качества.
-   Генераторы (или источники) звука В XAudio2 источником звука является место, из которого исходит звук, будь то просто короткий фоновый шум или сложная рок-композиция, проигрываемая музыкальным автоматом в игре. Вы определяете источники звука, вводя их координаты в игровом мире.
-   Прослушиватели звука Прослушивателем звука часто является игрок, или, возможно, компонент приложения в более сложной игре, который обрабатывает звук, полученный от прослушивателя. Вы можете субмикшировать этот звук в аудиопоток, чтобы воспроизвести игроку или использовать его в игре, например, для пробуждения в приложении стражника, отмеченного как прослушиватель.

## <a name="design-considerations"></a>Вопросы разработки


Звук является очень важной частью разработки игры. Многие игроки легко назовут не одну заурядную игру, получившую легендарную известность только благодаря запоминающейся музыке, великолепному озвучиванию, микшированию звука или общему звуковому оформлению. Музыка и звук определяют индивидуальность игры, образуют основной мотив, который выделяет игру из разряда других похожих игр, делая ее узнаваемой. Усилия, затраченные вами на разработку звукового оформления игры, обязательно окупятся.

Позиционный трехмерный звук может дать ощущение погружения в игру в дополнение к ощущениям, подаренным трехмерной графикой. Если вы разрабатываете сложную игру, которая симулирует реальную жизнь или требует выполнения в кинематографическом стиле, подумайте об использовании трехмерных позиционных звуковых методов, которые реально способны захватить игрока.

## <a name="directx-audio-development-roadmap"></a>Схема разработки аудио в DirectX


### <a name="xaudio2-conceptual-resources"></a>Ресурсы с основными понятиями о XAudio2

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
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/xaudio2-introduction">Введение в XAudio2</a></p></td>
<td align="left"><p>В разделе представлен перечень возможностей программирования звука, поддерживаемого XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/getting-started">Приступая к работе с XAudio2</a></p></td>
<td align="left"><p>В разделе представлены сведения по ключевым понятиям XAudio2, версиям XAudio2 и формату аудио RIFF.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/common-audio-concepts">Общие аудио основные понятия программирования</a></p></td>
<td align="left"><p>В разделе представлен обзор общих понятий аудио, с которыми должен быть знаком разработчик аудио.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/xaudio2-voices">Голоса XAudio2</a></p></td>
<td align="left"><p>В данном разделе содержится обзор речи в XAudio2, используемой для субмикширования звуковых данных, а также оперирования и управления ими.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/xaudio2-callbacks">Обратные вызовы XAudio2</a></p></td>
<td align="left"><p>В разделе описаны обратные вызовы в XAudio 2, используемые для предотвращения прерывания воспроизведения звука.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/audio-graphs">Графы аудио XAudio2</a></p></td>
<td align="left"><p>В разделе представлены схемы обработки звука в XAudio2, использующие набор клиентских звуковых потоков в качестве входящих данных, обрабатывающие их и выдающие результат обработки на аудиоустройство.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/xaudio2-audio-effects">Звуковые эффекты XAudio2</a></p></td>
<td align="left"><p>В разделе описаны звуковые эффекты в XAudio2, использующие входящие звуковые данные для выполнения определенных операций над ними (например, добавления эффекта реверберации) с последующей передачей далее.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/xaudio2-streaming-audio-data">Потоковая передача аудио данных с помощью XAudio2</a></p></td>
<td align="left"><p>В разделе описана потоковая передача звука при помощи XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/x3daudio">X3DAudio</a></p></td>
<td align="left"><p>В разделе описан X3DAudio, API, используемый совместно с XAudio2 для создания иллюзии объемного звучания.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/programming-reference">Справочник по программированию XAudio2</a></p></td>
<td align="left"><p>Этот раздел представляет собой полный справочник по API XAudio2.</p></td>
</tr>
</tbody>
</table>

 

### <a name="xaudio2-how-to-resources"></a>Ресурсы, содержащие руководства по работе с XAudio2

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
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--initialize-xaudio2">Практическое руководство. Инициализировать XAudio2</a></p></td>
<td align="left"><p>Узнайте, как инициализировать XAudio2 для воспроизведения звука путем создания экземпляра модуля XAudio2 и создания управляющей речи.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--load-audio-data-files-in-xaudio2">Практическое руководство. Загрузка файлов звуковые данные в XAudio2</a></p></td>
<td align="left"><p>Узнайте, как заполнять структуры, необходимые для воспроизведения звуковых данных в XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--play-a-sound-with-xaudio2">Практическое руководство. Воспроизведение звука с XAudio2</a></p></td>
<td align="left"><p>Узнайте, как воспроизвести предварительно загруженные звуковые данные в XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--use-submix-voices">Практическое руководство. Использовать Submix голоса</a></p></td>
<td align="left"><p>Узнайте, как организовать речевые группы для отправки выходящего результата в один субмикшированный голосовой канал.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--use-source-voice-callbacks">Практическое руководство. Использовать обратные вызовы голосовой источника</a></p></td>
<td align="left"><p>Узнайте, как использовать обратные вызовы речевых источников в XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--use-engine-callbacks">Практическое руководство. Использовать обратные вызовы ядра</a></p></td>
<td align="left"><p>Узнайте, как использовать обратные вызовы модуля в XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--build-a-basic-audio-processing-graph">Практическое руководство. Построить график базовую обработку аудио</a></p></td>
<td align="left"><p>Узнайте, как создать схему обработки звука на основе единой управляющей речи и единого речевого источника.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--dynamically-add-or-remove-voices-from-an-audio-graph">Практическое руководство. Динамически добавить или удалить голоса из графа аудио</a></p></td>
<td align="left"><p>Узнайте, как добавить или удалить голоса submix из графа, который был создан, описанные в <a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--build-a-basic-audio-processing-graph">как: Построить график базовую обработку аудио</a>.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--create-an-effect-chain">Практическое руководство. Создать цепочку эффект</a></p></td>
<td align="left"><p>Узнайте, как применить цепь эффектов к речи для обеспечения возможности специальной обработки звуковых данных такой речи.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--create-an-xapo">Практическое руководство. Создание XAPO</a></p></td>
<td align="left"><p>Узнайте, как реализовать <a href="https://docs.microsoft.com/windows/desktop/api/xapo/nn-xapo-ixapo"><strong>IXAPO</strong></a> для создания объекта звуковой обработки XAudio2 (XAPO).</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--add-run-time-parameter-support-to-an-xapo">Практическое руководство. Добавлена поддержка параметров времени выполнения в XAPO</a></p></td>
<td align="left"><p>Узнайте, как добавить в XAPO поддержку параметра времени выполнения за счет реализации интерфейса <a href="https://docs.microsoft.com/windows/desktop/api/xapo/nn-xapo-ixapoparameters"><strong>IXAPOParameters</strong></a>.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--use-an-xapo-in-xaudio2">Практическое руководство. Использовать XAPO в XAudio2</a></p></td>
<td align="left"><p>Узнайте, как применять эффект, реализованный как XAPO, в цепи эффектов XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--use-xapofx-in-xaudio2">Практическое руководство. Использовать XAPOFX в XAudio2</a></p></td>
<td align="left"><p>Узнайте, как применять один из эффектов в составе XAPOFX в цепи эффектов XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--stream-a-sound-from-disk">Практическое руководство. Stream звука с диска</a></p></td>
<td align="left"><p>Узнайте, как организовать поток звуковых данных в XAudio2 путем создания отдельного потока для чтения звукового буфера и применения обратных вызовов для управления данным потоком.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--integrate-x3daudio-with-xaudio2">Практическое руководство. Интеграция X3DAudio с XAudio2</a></p></td>
<td align="left"><p>Узнайте, как применять X3DAudio, чтобы получить значения объема и высоты основного тона для речевого сигнала XAudio2, а также параметры для встроенного эффекта реверберации XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/how-to--group-audio-methods-as-an-operation-set">Практическое руководство. Группа аудио методы, как набор операции</a></p></td>
<td align="left"><p>Узнайте, как применять наборы операций XAudio2 для одновременного действия группы вызовов методов.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/xaudio2/debugging-audio-glitches-in-xaudio2">Отладка ошибки при воспроизведении звука в XAudio2</a></p></td>
<td align="left"><p>Узнайте, как задать уровень ведения журнала отладки для XAudio2.</p></td>
</tr>
</tbody>
</table>

 

### <a name="media-foundation-resources"></a>Ресурсы Media Foundation

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
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/medfound/about-the-media-foundation-sdk">О Media Foundation</a></p></td>
<td align="left"><p>В разделе содержатся общие сведения об API Media Foundation и инструментах их поддержки.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/medfound/media-foundation-programming--essential-concepts">Media Foundation: Основные понятия</a></p></td>
<td align="left"><p>В разделе представлены некоторые понятия, понимание которых необходимо для написания приложения на платформе Media Foundation.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/medfound/media-foundation-architecture">Архитектура Media Foundation</a></p></td>
<td align="left"><p>В разделе описана общая структура Microsoft Media Foundation, а также используемые данной платформой базисные элементы мультимедиа и схема обработки данных.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/medfound/audio-video-capture">Записи аудио/видео</a></p></td>
<td align="left"><p>В разделе описано применение Microsoft Media Foundation для записи звука и видео.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/medfound/audio-video-playback">Воспроизведение аудио/видео</a></p></td>
<td align="left"><p>В разделе описана реализация воспроизведения звука/видео в вашем приложении.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/medfound/supported-media-formats-in-media-foundation">Поддерживаемые форматы файлов мультимедиа в Media Foundation</a></p></td>
<td align="left"><p>В разделе содержится перечень форматов мультимедиа, которые Microsoft Media Foundation поддерживает изначально. (Сторонние поставщики могут обеспечивать поддержку дополнительных форматов за счет написания специальных подключаемых модулей.)</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/medfound/encoding-and-file-authoring">Создание файла и кодировки</a></p></td>
<td align="left"><p>В разделе описано применение Microsoft Media Foundation для кодировки звука и видео, а также разработки файлов мультимедиа.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/medfound/windows-media-codecs">Кодеки Windows Media</a></p></td>
<td align="left"><p>В разделе описаны возможности применения аудио- и видеокодеков Windows Media для организации и использования сжатых потоков данных.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/medfound/media-foundation-programming-reference">Справочник по программированию Media Foundation</a></p></td>
<td align="left"><p>Этот раздел содержит справочную информацию по API Media Foundation.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/desktop/medfound/media-foundation-sdk-samples">Примеры в пакете SDK Media Foundation</a></p></td>
<td align="left"><p>В разделе содержится перечень примеров приложений, демонстрирующих методику применения Media Foundation.</p></td>
</tr>
</tbody>
</table>

 

### <a name="windows-runtime-xaml-media-types"></a>Типы мультимедиа XAML в среде выполнения Windows

Если вы используете [межпрограммное взаимодействие DirectX — XAML](https://docs.microsoft.com/previous-versions/windows/apps/hh825871(v=win.10)), вы можете внедрить API-интерфейсы мультимедиа на XAML из среды выполнения Windows в свои приложения UWP на C++ с использованием DirectX, чтобы упростить игровые сценарии.

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
<td align="left"><p><a href="https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement"><strong>Windows.UI.Xaml.Controls.MediaElement</strong></a></p></td>
<td align="left"><p>Элемент XAML, представляющий объект, содержащий звук, видео либо оба типа мультимедиа.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/uwp/audio-video-camera/index">Звук, видео и камера</a></p></td>
<td align="left"><p>Узнайте, как встраивать базовое аудио и видео в приложения универсальной платформы для Windows (UWP).</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/media-playback">MediaElement</a></p></td>
<td align="left"><p>Узнайте, как воспроизводить локально хранящийся файл мультимедиа в приложении UWP.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/media-playback">MediaElement</a></p></td>
<td align="left"><p>Узнайте, как выполнять потоковую передачу файла мультимедиа с низкой задержкой в приложении UWP.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://docs.microsoft.com/windows/uwp/audio-video-camera/media-casting">Приведение мультимедиа</a></p></td>
<td align="left"><p>Узнайте, как использовать контракт «Воспроизведение на устройстве» для потоковой передачи мультимедиа из приложения UWP на другое устройство.</p></td>
</tr>
</tbody>
</table>

 

## <a name="reference"></a>Ссылка


-   [Общие сведения о XAudio2](https://docs.microsoft.com/windows/desktop/xaudio2/xaudio2-introduction)
-   [Руководство по программированию в XAudio2](https://docs.microsoft.com/windows/desktop/xaudio2/programming-guide)
-   [Общие сведения о Microsoft Media Foundation](https://docs.microsoft.com/windows/desktop/medfound/microsoft-media-foundation-sdk)

 

## <a name="related-topics"></a>См. также


-   [Руководство по программированию в XAudio2](https://docs.microsoft.com/windows/desktop/xaudio2/programming-guide)

 

 




