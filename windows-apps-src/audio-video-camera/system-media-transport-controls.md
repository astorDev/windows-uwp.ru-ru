---
author: drewbatgit
ms.assetid: EFCF84D0-2F4C-454D-97DA-249E9EAA806C
description: "Класс SystemMediaTransportControls позволяет приложению использовать встроенные в ОС Windows системные элементы управления транспортом мультимедиа и обновлять отображаемые элементом управления метаданные о мультимедиа, воспроизводимом приложением."
title: "Системные элементы управления транспортом мультимедиа"
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: 5a94ce4112f7662d3fe9bf3c8a7d3f60b1569931

---

# Системные элементы управления транспортом мультимедиа

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Класс [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677) позволяет приложению использовать встроенные в ОС Windows системные элементы управления транспортом мультимедиа и обновлять отображаемые элементом управления метаданные о мультимедиа, воспроизводимом приложением.

Системные элементы управления транспортом отличаются от элементов управления транспортом в объекте [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926). Системные элементы управления открываются при нажатии клавиши на мультимедийном оборудовании (регулировка громкости на наушниках или клавиши мультимедиа на клавиатуре). Если пользователь нажимает клавишу ПАУЗА на клавиатуре и ваше приложение поддерживает [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677), приложение получает уведомление и может выполнить соответствующее действие.

Приложение также может обновлять сведения о мультимедиа, в том числе название композиции и эскиз, отображаемые классом [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677).

**Примечание.**  
Код, описанный в этом обзоре, реализован в [примере системных элементов управления транспортировкой мультимедиа UWP](http://go.microsoft.com/fwlink/?LinkId=619488). Скачайте этот пример, чтобы просмотреть код в контексте или использовать пример как отправную точку для настройки вашего приложения.

## Настройка элементов управления транспортом

В XAML-файле страницы определите класс [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926), которым будут управлять системные элементы управления транспортом мультимедиа. События [**CurrentStateChanged**](https://msdn.microsoft.com/library/windows/apps/br227375) и [**MediaOpened**](https://msdn.microsoft.com/library/windows/apps/br227394) используются для обновления системных элементов управления транспортом мультимедиа и будут описаны в этой статье.

[!code-xml[MediaElementSystemMediaTransportControls](./code/SMTCWin10/cs/MainPage.xaml#SnippetMediaElementSystemMediaTransportControls)]

Добавьте в XAML-файл кнопку, с помощью которой пользователь сможет выбирать файл для воспроизведения.

[!code-xml[OpenButton](./code/SMTCWin10/cs/MainPage.xaml#SnippetOpenButton)]

В коде страницы добавьте директивы using для указанных ниже пространств имен.

[!code-cs[Пространство имен](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetNamespace)]

Добавьте обработчик нажатия кнопки, использующий класс [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847), чтобы пользователь мог выбирать файл, а затем вызовите метод [**SetSource**](https://msdn.microsoft.com/library/windows/apps/br244338), чтобы сделать его активным файлом для элемента **MediaElement**.

[!code-cs[OpenMediaFile](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetOpenMediaFile)]

Получите экземпляр класса [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677), вызвав метод [**GetForCurrentView**](https://msdn.microsoft.com/library/windows/apps/dn278708).

Включите кнопки, которые будут использоваться приложением, настроив соответствующее свойство "is enabled" объекта **SystemMediaTransportControls**, например [**IsPlayEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278714), [**IsPauseEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278713), [**IsNextEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278712) и [**IsPreviousEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278715). Полный список доступных элементов управления см. в справочной документации для **SystemMediaTransportControls**.

Зарегистрируйте обработчик события [**ButtonPressed**](https://msdn.microsoft.com/library/windows/apps/dn278706), чтобы получать уведомления, когда пользователь нажимает кнопку.

[!code-cs[SystemMediaTransportControlsSetup](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsSetup)]

## Обработка нажатий кнопок системных элементов управления транспортом мультимедиа

При нажатии одной из включенных кнопок системные элементы управления транспортом создают событие [**ButtonPressed**](https://msdn.microsoft.com/library/windows/apps/dn278706). Свойство [**Button**](https://msdn.microsoft.com/library/windows/apps/dn278685) объекта [**SystemMediaTransportControlsButtonPressedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn278683), переданное в обработчик события, является участником [**SystemMediaTransportControlsButton**](https://msdn.microsoft.com/library/windows/apps/dn278681) перечисления, которое указывает, какие из включенных кнопок нажаты.

Чтобы можно было из обработчика событий [**ButtonPressed**](https://msdn.microsoft.com/library/windows/apps/dn278706) обновлять объекты в потоке пользовательского интерфейса, например объект [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926), необходимо маршалировать вызовы через объект [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211). Это необходимо, из-за того что обработчик событий **ButtonPressed** не вызывается из потока пользовательского интерфейса. Поэтому при попытке непосредственного изменения пользовательского интерфейса возникнет исключение.

[!code-cs[SystemMediaTransportControlsButtonPressed](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsButtonPressed)]

## Обновление системных элементов управления транспортом мультимедиа с помощью сведений о текущем состоянии мультимедиа

При изменении состояния мультимедиа необходимо уведомлять об этом класс [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677), чтобы система могла обновить элементы управления согласно текущему состоянию. Чтобы сделать это, задайте для свойства [**PlaybackStatus**](https://msdn.microsoft.com/library/windows/apps/dn278719) соответствующее значение [**MediaPlaybackStatus**](https://msdn.microsoft.com/library/windows/apps/dn278665) из события [**CurrentStateChanged**](https://msdn.microsoft.com/library/windows/apps/br227375) объекта [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926), возникшего при изменении состояния мультимедиа.

[!code-cs[SystemMediaTransportControlsStateChange](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsStateChange)]

## Обновление системных элементов управления транспортом мультимедиа с помощью эскизов и сведений о мультимедиа

С помощью класса [**SystemMediaTransportControlsDisplayUpdater**](https://msdn.microsoft.com/library/windows/apps/dn278686) можно обновить сведения о мультимедиа, отображаемые элементами управления транспортом, например название композиции или обложку альбома для воспроизводимого элемента мультимедиа. Получите экземпляр этого класса с помощью свойства [**SystemMediaTransportControls.DisplayUpdater**](https://msdn.microsoft.com/library/windows/apps/dn278707). Для обычных сценариев рекомендуем способ передачи метаданных, который заключается в вызове метода [**CopyFromFileAsync**](https://msdn.microsoft.com/library/windows/apps/dn278694) и передачи в него воспроизводящегося файла мультимедиа. Модуль обновления отображения автоматически извлечет метаданные и изображение эскиза из файла.

Вызовите метод [**Update**](https://msdn.microsoft.com/library/windows/apps/dn278701), чтобы заставить системные элементы управления транспортом мультимедиа обновить свой интерфейс, используя новые метаданные и эскиз.

[!code-cs[SystemMediaTransportControlsUpdater](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsUpdater)]

Если это необходимо для вашего сценария, можно вручную обновить метаданные, отображаемые системными элементами управления транспортом, настроив значения свойств [**MusicProperties**](https://msdn.microsoft.com/library/windows/apps/dn278696), [**ImageProperties**](https://msdn.microsoft.com/library/windows/apps/dn278695) или [**VideoProperties**](https://msdn.microsoft.com/library/windows/apps/dn278702), предоставленных классом [**DisplayUpdater**](https://msdn.microsoft.com/library/windows/apps/dn278707).

[!code-cs[SystemMediaTransportControlsUpdaterManual](./code/SMTCWin10/cs/MainPage.xaml.cs#SystemMediaTransportControlsUpdaterManual)]

## Обновление свойств временной шкалы системных элементов управления транспортом мультимедиа

Системные элементы управления транспортом отображают информацию о временной шкале для воспроизводимого элемента мультимедиа, включая текущую позицию воспроизведения, время начала и время окончания элемента мультимедиа. Чтобы обновить свойства временной шкалы системных элементов управления транспортом, создайте объект [**SystemMediaTransportControlsTimelineProperties**](https://msdn.microsoft.com/library/windows/apps/mt218746). Настройте свойства объекта, чтобы отразить текущее состояние воспроизводящегося элемента мультимедиа. Вызовите метод [**SystemMediaTransportControls.UpdateTimelineProperties**](https://msdn.microsoft.com/library/windows/apps/mt218760), чтобы заставить элементы управления обновить временную шкалу.

[!code-cs[UpdateTimelineProperties](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetUpdateTimelineProperties)]

-   Необходимо указать значение для свойств [**StartTime**](https://msdn.microsoft.com/library/windows/apps/mt218751), [**EndTime**](https://msdn.microsoft.com/library/windows/apps/mt218747) и [**Position**](https://msdn.microsoft.com/library/windows/apps/mt218755), чтобы системные элементы управления отображали временную шкалу для воспроизводящегося элемента.

-   С помощью свойств [
              **MinSeekTime**
            ](https://msdn.microsoft.com/library/windows/apps/mt218749) и [**MaxSeekTime**](https://msdn.microsoft.com/library/windows/apps/mt218748) можно указать диапазон в пределах временной шкалы, в котором пользователь может выполнять поиск. Для этого можно использовать типичный сценарий, заключающийся в том, чтобы разрешить поставщикам содержимого включать рекламные паузы в их мультимедиа.

    Необходимо настроить свойства [**MinSeekTime**](https://msdn.microsoft.com/library/windows/apps/mt218749) и [**MaxSeekTime**](https://msdn.microsoft.com/library/windows/apps/mt218748), чтобы можно было создать событие [**PositionChangeRequest**](https://msdn.microsoft.com/library/windows/apps/mt218755).

-   Рекомендуется синхронизировать системные элементы управления с процессом воспроизведения мультимедиа. Для этого во время воспроизведения необходимо обновлять эти свойства приблизительно каждые 5 секунд, а также при изменении состояния воспроизведения, например при приостановке или при поиске новой позиции.

## Реакция на изменение свойств проигрывателя

Существует набор свойств системных элементов управления транспортом, относящихся к текущему состоянию мультимедиапроигрывателя, а не к состоянию воспроизводимого элемента мультимедиа. Каждое из этих свойств сопоставлено событию, которое создается, когда пользователь изменяет связанный элемент управления. Эти свойства и события перечислены ниже.

| Свойство                                                                  | Событие                                                                                                   |
|---------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [**AutoRepeatMode**](https://msdn.microsoft.com/library/windows/apps/mt218753) | [**AutoRepeatModeChangeRequested**](https://msdn.microsoft.com/library/windows/apps/mt218754) |
| [**PlaybackRate**](https://msdn.microsoft.com/library/windows/apps/mt218756)     | [**PlaybackRateChangeRequested**](https://msdn.microsoft.com/library/windows/apps/mt218757)     |
| [**ShuffleEnabled**](https://msdn.microsoft.com/library/windows/apps/mt218758) | [**ShuffleEnabledChangeRequested**](https://msdn.microsoft.com/library/windows/apps/mt218759) |

 
Чтобы можно было обрабатывать взаимодействие с пользователем с помощью одного из этих элементов управления, сначала зарегистрируйте обработчик для связанного события.

[!code-cs[RegisterPlaybackChangedHandler](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetRegisterPlaybackChangedHandler)]

Прежде всего убедитесь, что в обработчике события необходимое значение находится в допустимом и ожидаемом диапазоне. Если это так, настройте соответствующее свойство объекта [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926), а затем — соответствующее свойство объекта [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677).

[!code-cs[PlaybackChangedHandler](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetPlaybackChangedHandler)]

-   Чтобы возникло одно из этих событий свойств проигрывателя, необходимо настроить начальное значение для свойства. Например, событие [**PlaybackRateChangeRequested**](https://msdn.microsoft.com/library/windows/apps/mt218757) не будет создано до тех пор, пока вы хотя бы один раз не присвоите значение свойству [**PlaybackRate**](https://msdn.microsoft.com/library/windows/apps/mt218756).

## Использование системных элементов управления транспортом мультимедиа для воспроизведения звука в фоновом режиме

Чтобы использовать системные элементы управления транспортом мультимедиа для воспроизведения звука в фоновом режиме, необходимо включить кнопки воспроизведения и приостановки, задав для свойств [**IsPlayEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278714) и [**IsPauseEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278713) значение true. Кроме того, ваше приложение должно обрабатывать событие [**ButtonPressed**](https://msdn.microsoft.com/library/windows/apps/dn278706).

Чтобы получить экземпляр класса [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677) из фоновой задачи приложения, необходимо использовать свойство [**BackgroundMediaPlayer.Current.SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn926635) вместо метода [**GetForCurrentView**](https://msdn.microsoft.com/library/windows/apps/dn278708), который можно использовать только из приложения переднего плана.

Дополнительные сведения о воспроизведении звука в фоновом режиме см. в разделе [Фоновый звук](background-audio.md).

 

 







<!--HONumber=Jun16_HO4-->


