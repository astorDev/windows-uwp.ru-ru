---
author: drewbatgit
ms.assetid: 
description: "В этой статье описывается, как импортировать мультимедиа с устройства, включая поиск доступных мультимедийных ресурсов, импорт файлов (фото и файлы образов данных) и удаление импортированных файлов с исходного устройства."
title: "Импорт мультимедиа"
translationtype: Human Translation
ms.sourcegitcommit: 599e7dd52145d695247b12427c1ebdddbfc4ffe1
ms.openlocfilehash: b1b8467e181d1a04317a8a0b8688ffb02621730c

---

# Импорт мультимедиа с устройства

В этой статье описывается, как импортировать мультимедиа с устройства, включая поиск доступных мультимедийных ресурсов, импорт файлов (видео, фото и файлы образов данных) и удаление импортированных файлов с исходного устройства.

> [!NOTE] 
> В этой статье используется код, адаптированный из [**примера приложения UWP MediaImport**](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MediaImport). Можно клонировать или скачать этот пример из [**Git-репозитория примеров универсальных приложений Windows**](https://github.com/Microsoft/Windows-universal-samples), чтобы изучить код в контексте или использовать его в качестве отправной точки для создания собственного приложения.

## Создание простого пользовательского интерфейса для импорта мультимедиа
В примере в этой статье используется минимальный пользовательский интерфейс для включения базовых сценариев импорта мультимедиа. Инструкции по созданию более надежного пользовательского интерфейса приложения для импорта мультимедиа см. в разделе [**Пример MediaImport**](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MediaImport). Следующий код XAML создает панель стека со следующими элементами управления:
* [**Button**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.Button), чтобы инициировать поиск источников импорта мультимедиа.
* [**ComboBox**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.ComboBox) для составления списка обнаруженных источников импорта мультимедиа и выбора из них.
* Элемент управления [**ListView**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.ListView) для отображения элементов мультимедиа из выбранного источника импорта и выбора нужных элементов.
* **Button** для запуска импорта элементов мультимедиа из выбранного источника.
* **Button** для запуска удаления элементов, которые были импортированы из выбранного источника.
* **Button** для отмены асинхронной операции импорта мультимедиа.

[!code-xml[ImportXAML](./code/PhotoImport_Win10/cs/MainPage.xaml#SnippetImportXAML)]

## Настройка файла кода программной части
Добавьте директивы *using*, чтобы включить пространства имен, используемые в этом примере, которые еще не включены в шаблон проекта по умолчанию.

[!code-cs[Использование](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetUsing)]

## Настройка отмены задач для операций импорта мультимедиа

Поскольку операции импорта мультимедиа могут занимать много времени, они выполняются асинхронно с использованием метода [**IAsyncOperationWithProgress**](https://msdn.microsoft.com/en-us/library/windows/apps/br206594.aspx). Объявите переменную-член класса типа [**CancellationTokenSource**](https://msdn.microsoft.com/en-us/library/system.threading.cancellationtokensource), который будет использоваться для отмены выполняемой операции, если пользователь нажимает кнопку отмены.

[!code-cs[DeclareCts](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetDeclareCts)]

Реализуйте обработчик для кнопки отмены. В примерах ниже в этой статье инициализируется объект **CancellationTokenSource**, когда начинается операция. Когда операция завершается, объект принимает значение null. В обработчике кнопки отмены убедитесь, что токен имеет значение null. В противном случае вызовите метод [**Cancel**](https://msdn.microsoft.com/en-us/library/dd321955), чтобы отменить операцию.

[!code-cs[OnCancel](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetOnCancel)]

## Вспомогательные классы привязки данных

В стандартном сценарии импорта мультимедиа пользователю отображается список доступных для импорта элементов мультимедиа. Количество доступных для выбора файлов мультимедиа может быть достаточно большим, поэтому удобно использовать для их изображения эскизы. По этой причине в этом примере используется три вспомогательных класса для постепенной загрузки записей в элемент управления ListView по мере прокрутки списка пользователем.

* 
              Класс **IncrementalLoadingBase**: реализует элементы [**IList**](https://msdn.microsoft.com/en-us/library/system.collections.ilist), [**ISupportIncrementalLoading**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.data.isupportincrementalloading) и [**INotifyCollectionChanged**](https://msdn.microsoft.com/en-us/library/windows/apps/system.collections.specialized.inotifycollectionchanged(v=vs.105).aspx), чтобы обеспечить базовое поведение постепенной загрузки.
* 
              Класс **GeneratorIncrementalLoadingClass**: предоставляет реализацию базового класса постепенной загрузки.
* 
              Класс **ImportableItemWrapper**: тонкая оболочка для класса [**PhotoImportItem**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportItem), позволяющая добавлять привязываемое свойство [**BitmapImage**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Media.Imaging.BitmapImage) изображения эскиза для каждого импортируемого элемента.

Эти классы предоставляются в примере [**MediaImport**](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MediaImport), их можно добавить в проект без изменений. После добавления в проект вспомогательных классов объявите переменную-член класса типа **GeneratorIncrementalLoadingClass** для использования далее в этом примере.

[!code-cs[GeneratorIncrementalLoadingClass](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetGeneratorIncrementalLoadingClass)]


# Найдите доступные источники, из которых можно импортировать мультимедиа

В обработчике нажатия для кнопки поиска источников вызовите статический метод [**PhotoImportManager.FindAllSourcesAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportManager.FindAllSourcesAsync), чтобы запустить системный поиск устройств, из которых можно импортировать мультимедиа. Дождавшись завершения операции, циклически переберите все объекты [**PhotoImportSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportSource) в возвращенном списке и добавьте запись в элемент **ComboBox**, настроив свойство **Tag** для объекта источника, чтобы его было легко извлечь, когда пользователь сделает выбор.

[!code-cs[FindSourcesClick](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetFindSourcesClick)]

Объявите переменную-член класса для хранения выбранного пользователем источника импорта.

[!code-cs[DeclareImportSource](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetDeclareImportSource)]

В обработчике [**SelectionChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.Primitives.Selector.SelectionChanged) для источника импорта **ComboBox** задайте выбранный источник в качестве переменной-члена класса и вызовите вспомогательный метод **FindItems**, который будет показан далее в этой статье. 

[!code-cs[SourcesSelectionChanged](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetSourcesSelectionChanged)]

# Поиск элементов для импорта

Добавьте переменные-члены класса типов [**PhotoImportSession**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportSession) и [**PhotoImportFindItemsResult**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportFindItemsResult), которые будут использоваться в следующих шагах.

[!code-cs[DeclareImport](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetDeclareImport)]

В методе FindItems инициализируйте переменную **CancellationTokenSource**, чтобы ее можно было при необходимости использовать для отмены операции поиска. В блоке **try** создайте новую сессию импорта, вызвав метод [**CreateImportSession**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportSource.CreateImportSession) в объекте [**PhotoImportSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportSource), выбранном пользователем. Создайте новый объект [**Progress**](https://msdn.microsoft.com/en-us/library/hh193692.aspx), чтобы предоставить обратный вызов, отображающий выполнение операции поиска. Затем вызовите метод [**FindItemsAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportSession.FindItemsAsync(Windows.Media.Import.PhotoImportContentTypeFilter,Windows.Media.Import.PhotoImportItemSelectionMode), чтобы начать операцию поиска. С помощью значения [**PhotoImportContentTypeFilter**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportContentTypeFilter) укажите, нужно ли возвращать фото, видео или и то, и другое. С помощью значения [**PhotoImportItemSelectionMode**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportItemSelectionMode) укажите, какие элементы мультимедиа возвращаются, если свойство [**IsSelected**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportItem.IsSelected) этих элементов имеет значение true: все, никакие или только новые. Это свойство привязано к флажку для каждого элемента мультимедиа в шаблоне элемента ListBox.

**FindItemsAsync** возвращает [**IAsyncOperationWithProgress**](https://msdn.microsoft.com/en-us/library/windows/apps/br206594.aspx). Метод расширения [**AsTask**](https://msdn.microsoft.com/en-us/library/hh779750.aspx) используется для создания задачи, которую можно ожидать или можно отменить с помощью токена отмены. Эта задача сообщает о ходе выполнения с использованием предоставленного объекта **Progress**.

Затем инициализируется вспомогательный класс привязки данных, **GeneratorIncrementalLoadingClass**. **FindItemsAsync** по возвращении из состояния ожидания, возвращает объект [**PhotoImportFindItemsResult**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportFindItemsResult). Этот объект содержит данные о состоянии операции поиска, включая сведения об успешном завершении операции и количестве типов обнаруженных элементов мультимедиа. Свойство [**FoundItems**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportFindItemsResult.FoundItems) содержит список объектов [**PhotoImportItem**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportItem), представляющих найденные элементы мультимедиа. Конструктор **GeneratorIncrementalLoadingClass** задает в качестве аргументов общее количество элементов, которые будут постепенно загружены, и функцию, создающую новые элементы, которые при необходимости можно загрузить. В этом случае предоставленное лямбда-выражение создает новый экземпляр оболочки **ImportableItemWrapper** для объекта **PhotoImportItem** и включает эскиз для каждого элемента. После инициализации класса постепенной загрузки задайте для него свойство [**ItemsSource**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.ItemsControl.ItemsSource) элемента управления **ListView** в пользовательском интерфейсе. Теперь обнаруженные элементы мультимедиа будут постепенно загружаться и отображаться в списке.

Затем будут отображаться сведения о выполнении операции поиска. Типичное приложение будет отображать эти сведения в пользовательском интерфейсе, но в этом примере информация просто выводится на консоли отладки. Наконец, задайте для токена отмены значение null, потому что операция завершена.

[!code-cs[FindItems](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetFindItems)]

## Импорт элементов мультимедиа

Перед выполнением операции импорта объявите объект [**PhotoImportImportItemsResult**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportImportItemsResult), чтобы сохранить результаты операции импорта. ОН будет использоваться в дальнейшем для удаления элементов мультимедиа, которые были успешно импортированы из источника.

[!code-cs[DeclareImportResult](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetDeclareImportResult)]

Перед началом операции импорта мультимедиа инициализируйте переменную **CancellationTokenSource** и задайте значение элемента управления [**ProgressBar**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.ProgressBar) равным 0.

Если в элементе управления **ListView** нет выбранных элементов, то импортировать нечего. В противном случае инициализируйте объект [**Progress**](https://msdn.microsoft.com/en-us/library/hh193692.aspx), чтобы предоставить обратный вызов хода выполнения, обновляющий значение элемента управления индикатора выполнения. Зарегистрируйте обработчик для события [**ItemImported**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportFindItemsResult.ItemImported) объекта [**PhotoImportFindItemsResult**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportFindItemsResult), возвращаемого операцией поиска. Это событие будет вызываться всякий раз при импорте элемента. В данном примере оно выводит имя каждого импортированного файла на консоль отладки.

Вызовите метод [**ImportItemsAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportFindItemsResult.ImportItemsAsync), чтобы начать операцию импорта. Как и с операцией поиска, метод расширения [**AsTask**](https://msdn.microsoft.com/en-us/library/hh779750.aspx) используется для преобразования возвращенной операции в задачу, которую можно ожидать, отменить или использовать для оцени хода выполнения.

По окончании операции импорта состояние операции доступно в объекте [**PhotoImportImportItemsResult**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportImportItemsResult), возвращаемом [**ImportItemsAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportFindItemsResult.ImportItemsAsync). В этом примере сведения о состоянии выводятся на консоль отладки, после чего значение токена отмены задается равным нулю.

[!code-cs[ImportClick](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetImportClick)]

## Удаление импортированных элементов
Чтобы удалить успешно импортированные элементы из источника, откуда они были импортированы, сначала инициализируйте токен отмены, чтобы операцию удаления можно было отменить, а затем задайте значение индикатора выполнения равным 0. Убедитесь, что объект [**PhotoImportImportItemsResult**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportImportItemsResult), возвращаемый из объекта [**ImportItemsAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportFindItemsResult.ImportItemsAsync), не равен null. В противном случае снова создайте объект [**Progress**](https://msdn.microsoft.com/en-us/library/hh193692.aspx), чтобы предоставить обратный вызов хода выполнения для операции удаления. Вызовите метод [**DeleteImportedItemsFromSourceAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportImportItemsResult.DeleteImportedItemsFromSourceAsync), чтобы начать удалять импортированные элементы. Воспользуйтесь объектом **AsTask**, чтобы преобразовать результат в ожидаемую задачу с возможностью отслеживания хода выполнения и возможностью отмены. После ожидания возвращенный объект [**PhotoImportDeleteImportedItemsFromSourceResult**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Import.PhotoImportDeleteImportedItemsFromSourceResult) можно использовать для получения и отображения сведений о состоянии операции удаления.

[!code-cs[DeleteClick](./code/PhotoImport_Win10/cs/MainPage.xaml.cs#SnippetDeleteClick)]








 





<!--HONumber=Aug16_HO3-->


