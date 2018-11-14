---
author: mtoepke
title: Звук для игр
description: Благодаря сведениям в этом разделе вы научитесь создавать и встраивать музыку и звуки в игры DirectX, а также обрабатывать звуковые сигналы для создания динамичного и позиционного звука.
ms.assetid: ab29297a-9588-c79b-24c5-3b94b85e74a8
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, звук, directx
ms.localizationpriority: medium
ms.openlocfilehash: a0b0ae219ea7fd014b39eb8eb7a09049f7c632a2
ms.sourcegitcommit: bdc40b08cbcd46fc379feeda3c63204290e055af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "6150405"
---
# <a name="audio-for-games"></a>Звук для игр



Благодаря сведениям в этом разделе вы научитесь создавать и встраивать музыку и звуки в игры DirectX, а также обрабатывать звуковые сигналы для создания динамичного и позиционного звука.

Мы рекомендуем пользоваться библиотекой XAudio2 в DirectX для звукового программирования способом, описанным в этом разделе. XAudio2 является библиотекой звуковых файлов низкого уровня, которая обеспечивает обработку сигналов и микширование звука для игр; она поддерживает широкий диапазон форматов.

Вы также можете импортировать простые звуки и музыку с помощью [Microsoft Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms694197). Microsoft Media Foundation разработана для проигрывания аудио- и видео- файлов и потоков мультимедиа; она будет особенно полезной для кинематографических сцен или неинтерактивных компонентов вашей игры.

## <a name="concepts-at-a-glance"></a>Краткий обзор основных понятий


Ниже представлены несколько основных понятий в сфере звукового программирования, которые мы используем в данном разделе.

-   Сигналы являются основной единицей звукового программирования, аналогично пикселю в графическом программировании. Цифровые процессоры сигналов (ЦПС), которые их обрабатывают при работе со звуком для игры, соответствуют построителям текстуры. Они могут преобразовывать сигналы, сочетать их или фильтровать. Программируя ЦПС, вы можете изменять звуковые и музыкальные эффекты вашей игры с необходимой степенью сложности.
-   Голоса являются комбинацией двух или более сигналов. Существует три типа голосовых объектов XAudio2: исходные, комбинированные и обработанные. Исходные голосовые объекты берутся из звуковых данных, предоставленных клиентом. Данные исходных и комбинированных голосов используются для получения одного или более комбинированных или обработанных голосов. Комбинированные и обработанные голоса смешивают звуковые сигналы из всех голосов, чьи данные они получают, и работают с итоговым звуком. Обработанные голоса записываются в виде звуковых данных на аудиоустройство.
-   Микширование— это процесс сведения нескольких дискретных голосов, например звуковых эффектов и фонового звука, проигрываемого во время сцены, в один звуковой поток. Субмикширование — процесс сочетания нескольких дискретных сигналов, например разных звуков работающего двигателя, и создания голоса.
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
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415813">Введение в XAudio2</a></p></td>
<td align="left"><p>В разделе представлен перечень возможностей программирования звука, поддерживаемого XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415762">Начало работы с XAudio2</a></p></td>
<td align="left"><p>В разделе представлены сведения по ключевым понятиям XAudio2, версиям XAudio2 и формату аудио RIFF.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415692">Общие понятия программирования звука</a></p></td>
<td align="left"><p>В разделе представлен обзор общих понятий аудио, с которыми должен быть знаком разработчик аудио.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415825">Речь в XAudio2</a></p></td>
<td align="left"><p>В данном разделе содержится обзор речи в XAudio2, используемой для субмикширования звуковых данных, а также оперирования и управления ими.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415745">Обратные вызовы в XAudio2</a></p></td>
<td align="left"><p>В разделе описаны обратные вызовы в XAudio 2, используемые для предотвращения прерывания воспроизведения звука.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415739">Схемы обработки звука в XAudio2</a></p></td>
<td align="left"><p>В разделе представлены схемы обработки звука в XAudio2, использующие набор клиентских звуковых потоков в качестве входящих данных, обрабатывающие их и выдающие результат обработки на аудиоустройство.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415756">Звуковые эффекты в XAudio2</a></p></td>
<td align="left"><p>В разделе описаны звуковые эффекты в XAudio2, использующие входящие звуковые данные для выполнения определенных операций над ними (например, добавления эффекта реверберации) с последующей передачей далее.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415821">Потоковая передача звуковых данных при помощи XAudio2</a></p></td>
<td align="left"><p>В разделе описана потоковая передача звука при помощи XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415714">X3DAudio</a></p></td>
<td align="left"><p>В разделе описан X3DAudio, API, используемый совместно с XAudio2 для создания иллюзии объемного звучания.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415899">Справочник по программированию в XAudio2</a></p></td>
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
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415779">Руководство: инициализация XAudio2</a></p></td>
<td align="left"><p>Узнайте, как инициализировать XAudio2 для воспроизведения звука путем создания экземпляра модуля XAudio2 и создания управляющей речи.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415781">Руководство: загрузка файлов звуковых данных в XAudio2</a></p></td>
<td align="left"><p>Узнайте, как заполнять структуры, необходимые для воспроизведения звуковых данных в XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415787">Руководство: воспроизведение звука при помощи XAudio2</a></p></td>
<td align="left"><p>Узнайте, как воспроизвести предварительно загруженные звуковые данные в XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415794">Руководство: использование субмикшированной речи</a></p></td>
<td align="left"><p>Узнайте, как организовать речевые группы для отправки выходящего результата в один субмикшированный голосовой канал.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415769">Руководство: использование обратных вызовов речевых источников</a></p></td>
<td align="left"><p>Узнайте, как использовать обратные вызовы речевых источников в XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415774">Руководство: использование обратных вызовов модуля</a></p></td>
<td align="left"><p>Узнайте, как использовать обратные вызовы модуля в XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415767">Руководство: создание базовой схемы обработки звука</a></p></td>
<td align="left"><p>Узнайте, как создать схему обработки звука на основе единой управляющей речи и единого речевого источника.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415772">Руководство: динамическое добавление речи или удаление ее из звуковой схемы</a></p></td>
<td align="left"><p>Узнайте, как добавить субмикшированную речь или удалить ее из схемы, созданной в ходе реализации этапа <a href="https://msdn.microsoft.com/library/windows/desktop/ee415767">Руководство: создание базовой схемы обработки звука</a>.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415789">Руководство: создание цепи эффектов</a></p></td>
<td align="left"><p>Узнайте, как применить цепь эффектов к речи для обеспечения возможности специальной обработки звуковых данных такой речи.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415730">Руководство: создание XAPO</a></p></td>
<td align="left"><p>Узнайте, как реализовать <a href="https://msdn.microsoft.com/library/windows/desktop/ee415893"><strong>IXAPO</strong></a> для создания объекта звуковой обработки XAudio2 (XAPO).</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415728">Руководство: добавление в XAPO поддержки параметра времени выполнения</a></p></td>
<td align="left"><p>Узнайте, как добавить в XAPO поддержку параметра времени выполнения за счет реализации интерфейса <a href="https://msdn.microsoft.com/library/windows/desktop/ee415896"><strong>IXAPOParameters</strong></a>.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415733">Руководство: использование XAPO в XAudio2</a></p></td>
<td align="left"><p>Узнайте, как применять эффект, реализованный как XAPO, в цепи эффектов XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415723">Руководство: использование XAPOFX в XAudio2</a></p></td>
<td align="left"><p>Узнайте, как применять один из эффектов в составе XAPOFX в цепи эффектов XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415791">Руководство: организация звукового потока с диска</a></p></td>
<td align="left"><p>Узнайте, как организовать поток звуковых данных в XAudio2 путем создания отдельного потока для чтения звукового буфера и применения обратных вызовов для управления данным потоком.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415798">Руководство: интегрирование X3DAudio с XAudio2</a></p></td>
<td align="left"><p>Узнайте, как применять X3DAudio, чтобы получить значения объема и высоты основного тона для речевого сигнала XAudio2, а также параметры для встроенного эффекта реверберации XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415783">Руководство: группировка звуковых методов как набора операций</a></p></td>
<td align="left"><p>Узнайте, как применять наборы операций XAudio2 для одновременного действия группы вызовов методов.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee415765">Отладка временных сбоев звука в XAudio2</a></p></td>
<td align="left"><p>Узнайте, как задать уровень ведения журнала отладки для XAudio2.</p></td>
</tr>
</tbody>
</table>

 

### <a name="media-foundation-resources"></a>Ресурсы Media Foundation

Media Foundation (MF)— это платформа мультимедиа для потокового воспроизведения звука и видео. API Media Foundation могут применяться для организации потоков звука и видео, закодированных и сжатых при помощи различных алгоритмов. Данная платформа не предназначена для отработки игровых сценариев в реальном времени; она обеспечивает мощные инструменты и обширную поддержку шифровки и дешифровки для более линейных видов записи и воспроизведения компонентов звука и видео.

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
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ms696274">О Media Foundation</a></p></td>
<td align="left"><p>В разделе содержатся общие сведения об API Media Foundation и инструментах их поддержки.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ee663601">Media Foundation: основные понятия</a></p></td>
<td align="left"><p>В разделе представлены некоторые понятия, понимание которых необходимо для написания приложения на платформе Media Foundation.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ms696219">Архитектура Media Foundation</a></p></td>
<td align="left"><p>В разделе описана общая структура Microsoft Media Foundation, а также используемые данной платформой базисные элементы мультимедиа и схема обработки данных.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/dd317910">Захват аудио- и видеоданных</a></p></td>
<td align="left"><p>В разделе описано применение Microsoft Media Foundation для записи звука и видео.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/dd317914">Воспроизведение звука/видео</a></p></td>
<td align="left"><p>В разделе описана реализация воспроизведения звука/видео в вашем приложении.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/dd757927">Поддерживаемые форматы мультимедиа в Media Foundation</a></p></td>
<td align="left"><p>В разделе содержится перечень форматов мультимедиа, которые Microsoft Media Foundation поддерживает изначально. (Сторонние поставщики могут обеспечивать поддержку дополнительных форматов за счет написания специальных подключаемых модулей.)</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/dd318778">Кодировка и разработка файлов</a></p></td>
<td align="left"><p>В разделе описано применение Microsoft Media Foundation для кодировки звука и видео, а также разработки файлов мультимедиа.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ff819508">Кодеки Windows Media</a></p></td>
<td align="left"><p>В разделе описаны возможности применения аудио- и видеокодеков Windows Media для организации и использования сжатых потоков данных.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/ms704847">Справочник по программированию в Media Foundation</a></p></td>
<td align="left"><p>Этот раздел содержит справочную информацию по API Media Foundation.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/desktop/aa371827">Примеры пакетов SDK Media Foundation</a></p></td>
<td align="left"><p>В разделе содержится перечень примеров приложений, демонстрирующих методику применения Media Foundation.</p></td>
</tr>
</tbody>
</table>

 

### <a name="windows-runtime-xaml-media-types"></a>Типы мультимедиа XAML в среде выполнения Windows

Если вы используете [межпрограммное взаимодействие DirectX — XAML](https://msdn.microsoft.com/library/windows/apps/hh825871), вы можете внедрить API-интерфейсы мультимедиа на XAML из среды выполнения Windows в свои приложения UWP на C++ с использованием DirectX, чтобы упростить игровые сценарии.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Статья</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/br242926"><strong>Windows.UI.Xaml.Controls.MediaElement</strong></a></p></td>
<td align="left"><p>Элемент XAML, представляющий объект, содержащий звук, видео либо оба типа мультимедиа.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/mt203788">Звук, видео и камера</a></p></td>
<td align="left"><p>Узнайте, как встраивать базовое аудио и видео в приложения универсальной платформы для Windows (UWP).</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/mt187272">MediaElement</a></p></td>
<td align="left"><p>Узнайте, как воспроизводить локально хранящийся файл мультимедиа в приложении UWP.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/mt187272">MediaElement</a></p></td>
<td align="left"><p>Узнайте, как выполнять потоковую передачу файла мультимедиа с низкой задержкой в приложении UWP.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/mt282143">Трансляция мультимедиа</a></p></td>
<td align="left"><p>Узнайте, как использовать контракт «Воспроизведение на устройстве» для потоковой передачи мультимедиа из приложения UWP на другое устройство.</p></td>
</tr>
</tbody>
</table>

 

## <a name="reference"></a>Справочные материалы


-   [Введение в XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415813)
-   [Руководство по программированию для XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415737)
-   [Общие сведения о Microsoft Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms694197)

 

## <a name="related-topics"></a>Статьи по теме


-   [Руководство по программированию для XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415737)

 

 




