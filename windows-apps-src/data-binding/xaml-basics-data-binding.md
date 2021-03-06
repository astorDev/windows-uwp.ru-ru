---
title: создание привязок данных
description: В этой статье рассматриваются основы использования привязок данных на языке XAML.
keywords: XAML, UWP, Getting Started
ms.date: 08/30/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 707c2ed110498f4ef18fea31ace87d1fd2434112
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67820345"
---
# <a name="create-data-bindings"></a>создание привязок данных

Предположим, что вы спроектировали и реализовали красивый пользовательский интерфейс с изображениями-заполнителями, шаблоном с текстом-заполнителем и элементами управления без каких-либо функций. Далее необходимо подключить его к реальным данным и превратить из прототипа в действующее приложение. 

В этом руководстве вы узнаете, как заменить шаблон привязками данных и создать другие прямые ссылки между пользовательским интерфейсом и данными. Вы также узнаете, как форматировать или преобразовывать данные для отображения, а также как синхронизировать пользовательский интерфейс и данные. По завершении работы с этим руководством вы сможете упростить и лучше организовать код XAML и C#, что сделает его обслуживание и расширение заметно удобнее.

Начнем с упрощенной версии примера PhotoLab. Эта начальная версия включает в себя полный уровень данных и базовые макеты страниц на языке XAML, но для удобства просмотра кода в ней отсутствуют многие возможности. В этом руководстве не будет создано полноценное приложение, поэтому ознакомьтесь с окончательной версией, в которой присутствуют такие возможности, как пользовательские анимации и поддержка телефонов. Окончательную версию можно найти в корневой папке репозитория [Windows-appsample-photo-lab](https://github.com/Microsoft/Windows-appsample-photo-lab). 

## <a name="prerequisites"></a>Предварительные условия

* [Visual Studio 2019 и последняя версия пакета SDK для Windows 10](https://developer.microsoft.com/windows/downloads).

## <a name="part-0-get-the-code"></a>Часть 0. Получение кода
Это практическое занятие начинается в репозитории примера PhotoLab в папке [xaml-basics-starting-points/data-binding](https://github.com/Microsoft/Windows-appsample-photo-lab/tree/master/xaml-basics-starting-points/data-binding). После клонирования или скачивания репозитория проект можно изменить. Для этого откройте файл PhotoLab.sln в Visual Studio 2019.

В приложении PhotoLab есть две основные страницы:

**MainPage.xaml** — отображает представление фотоальбома, а также некоторые сведения о каждом файле изображения.
![MainPage](../design/basics/images/xaml-basics/mainpage.png)

**DetailPage.xaml** — отображает одну выбранную фотографию. Всплывающее меню редактирования позволяет изменить, переименовать и сохранить фотографию.
![DetailPage](../design/basics/images/xaml-basics/detailpage.png)

## <a name="part-1-replace-the-placeholders"></a>Часть 1. Замена заполнителей

В этой части мы создадим однократные привязки в шаблоне данных XAML для отображения реальных изображений и их метаданных вместо заполнителей. 

Однократные привязки предназначены для неизменных данных, доступных только для чтения, поэтому они отличаются высокой производительностью и удобством создания, что позволяет отображать большие наборы данных в элементах управления **GridView** и **ListView**. 

**Замена заполнителей однократными привязками**

1. Откройте папку xaml-basics-starting-points\data-binding и запустите файл PhotoLab.sln. 

2. Убедитесь, что в качестве платформы решения выбрана архитектура x86 или x64, а не ARM, а затем запустите приложение. Ниже показано состояние приложения с заполнителями пользовательского интерфейса до добавления привязок. 

    ![Запущенное приложение с заполнителями вместо изображений и текста](../design/basics/images/xaml-basics/gallery-with-placeholder-templates.png)

3. Откройте файл MainPage.xaml и найдите шаблон **DataTemplate** с именем **ImageGridView_DefaultItemTemplate**. Этот шаблон будет обновлен для использования привязок данных. 

    **До**
    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate">
    ```

    **ImageGridView** использует значение **x:Key** для выбора этого шаблона для отображения объектов данных. 

4. Добавьте в шаблон значение **x:DataType**. 

    **После**
    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate" 
                  x:DataType="local:ImageFileInfo">
    ```

    **x:DataType** указывает, для какого типа предназначен этот шаблон. В этом случае шаблон предназначен для класса **ImageFileInfo** (где "local:" указывает на локальное пространство имен, как определено в объявлении xmlns в верхней части файла).
    
    **x:DataType** требуется при использовании выражений **x:Bind** в шаблоне данных, как описывается далее. 

5. В шаблоне **DataTemplate** найдите элемент **Image** с именем **ItemImage** и замените его значение **Source**, как показано ниже. 

    **До**
    ```xaml
    <Image x:Name="ItemImage" 
           Source="/Assets/StoreLogo.png" 
           Stretch="Uniform" />
    ```
    
    **После**
    ```xaml
    <Image x:Name="ItemImage" 
           Source="{x:Bind ImageSource}" 
           Stretch="Uniform" />
    ```
    
    **x:Name** определяет элемент XAML, чтобы можно было добавить ссылку на него в другом месте кода XAML и кода программной части. 

    Выражения **x:Bind** получают значение от свойства **data-object** и предоставляют это значение свойству пользовательского интерфейса. В шаблонах в качестве значения свойства используется значение, установленное для **x:DataType**. В этом случае источником данных является свойство **ImageFileInfo.ImageSource**. 
    
    > [!NOTE] 
    > Также значение **x:Bind** сообщает редактору о типе данных, поэтому вместо ввода имени свойства в выражении **x:Bind** можно использовать функцию IntelliSense. Попробуйте сделать это в коде, который только что вставили: поместите курсор сразу после **x:Bind** и нажмите клавишу ПРОБЕЛ, чтобы просмотреть список свойств, к которым можно выполнить привязку.

6. Замените значения других элементов управления пользовательского интерфейса таким же образом. (Попробуйте сделать это с помощью функции IntelliSense вместо копирования и вставки.)

    **До**
    ```xaml
    <TextBlock Text="Placeholder" ... />
    <StackPanel ... >
        <TextBlock Text="PNG file" ... />
        <TextBlock Text="50 x 50" ... />
    </StackPanel>
    <telerikInput:RadRating Value="3" ... />
    ```
    
    **После**
    ```xaml
    <TextBlock Text="{x:Bind ImageTitle}" ... />
    <StackPanel ... >
        <TextBlock Text="{x:Bind ImageFileType}" ... />
        <TextBlock Text="{x:Bind ImageDimensions}" ... />
    </StackPanel>
    <telerikInput:RadRating Value="{x:Bind ImageRating}" ... />
    ```

Запустите приложение и посмотрите, как оно выглядит на данном этапе. Больше никаких заполнителей! Отличное начало. 

![Запущенное приложение с реальными изображениями и текстом вместо заполнителей](../design/basics/images/xaml-basics/gallery-with-populated-templates.png)

> [!Note]
> Если вы хотите еще поэкспериментировать, попробуйте добавить новый элемент TextBlock в шаблон данных и используйте функцию IntelliSense и x:Bind, чтобы найти свойство для отображения. 

## <a name="part-2-use-binding-to-connect-the-gallery-ui-to-the-images"></a>Часть 2. Использование привязки для связи пользовательского интерфейса коллекции изображений

В этой части мы создадим однократные привязки на странице XAML, чтобы связать представление коллекции с набором изображений. Для этого необходимо заменить существующий процедурный код, выполняющий эту функцию, в коде программной части. Также мы создадим кнопку **Delete** (Удалить), чтобы проверить, как изменится представление коллекции при удалении изображений из набора. Также вы узнаете, как привязывать события к обработчикам событий, чтобы обеспечить большую гибкость по сравнению с традиционными обработчиками событий. 

Все рассмотренные на данный момент привязки находятся внутри шаблонов данных и ссылаются на свойства класса, указанного значением **x:DataType**. Как насчет остального кода XAML на вашей странице? 

Выражения **x:Bind** за пределами шаблонов данных всегда привязаны к самой странице. Это означает, что можно добавить ссылку на любой элемент, добавленный в код программной части или объявленный в XAML, включая пользовательские свойства и свойства других элементов управления пользовательского интерфейса на странице (если только у них есть значение **x:Name**). 

В примере PhotoLab такую привязку можно использовать для создания прямой связи между основным элементом управления **GridView** и коллекцией изображений, вместо того чтобы делать это в коде программной части. В дальнейшем будут представлены другие примеры. 

**Привязка основного элемента управления GridView к коллекции изображений**

1. В файле MainPage.xaml.cs найдите метод **OnNavigatedTo** и удалите код, устанавливающий значение **ItemsSource**.

    **До**
    ```c#
    ImageGridView.ItemsSource = Images;
    ```

    **После**
    ```c#
    // Replaced with XAML binding:
    // ImageGridView.ItemsSource = Images;
    ```

2. В файле MainPage.xaml найдите **GridView** с именем **ImageGridView** и добавьте атрибут **ItemsSource**. В качестве значения используйте выражение **x:Bind**, которое ссылается на свойство **Images**, реализованное в коде программной части. 

    **До**
    ```xaml
    <GridView x:Name="ImageGridView" 
    ```

    **После**
    ```xaml
    <GridView x:Name="ImageGridView" 
              ItemsSource="{x:Bind Images}" 
    ```

    Свойство **Images** относится к типу **ObservableCollection\<ImageFileInfo\>** , поэтому отдельные элементы, отображаемые в **GridView**, относятся к типу **ImageFileInfo**. Это соответствует значению **x:DataType**, описанному в части 1. 

Все привязки, которые мы рассмотрели на данный момент, являются однократными привязками с доступом только для чтения. Это поведение по умолчанию для обычных выражений **x:Bind**. Данные загружаются только при инициализации, что обеспечивает высокую производительность привязок. Это отличный вариант для поддержки нескольких сложных представлений больших наборов данных. 

Даже только что добавленная привязка **ItemsSource** является однократной привязкой с доступом только для чтения к неизменному значению свойства. Однако в этом случае необходимо провести важное различие. Неизменное значение свойства **Images** является однократным определенным экземпляром коллекции, инициализированным один раз, как показано здесь.

```Csharp
private ObservableCollection<ImageFileInfo> Images { get; }
    = new ObservableCollection<ImageFileInfo>();
```

Значение свойства **Images** не изменяется, но так как это свойство относится к типу **ObservableCollection\<T\>** , *содержимое* коллекции может меняться; при этом привязка позволит автоматически обнаружить изменения и обновить пользовательский интерфейс. 

Чтобы проверить это, мы временно добавим кнопку, которая удаляет выбранное на данный момент изображение. Этой кнопки нет в окончательной версии, так как при выборе изображения вы перейдете на страницу сведений. Однако поведение **ObservableCollection\<T\>** по-прежнему играет важную роль в окончательной версии примера PhotoLab, так как код XAML инициализируется в конструкторе страниц (путем вызова метода **InitializeComponent**), но коллекция **Images** заполняется в дальнейшем через метод **OnNavigatedTo**. 

**Добавление кнопки удаления**

1. В файле MainPage.xaml найдите **CommandBar** с именем **MainCommandBar** и добавьте новую кнопку перед кнопкой масштабирования. (Элементы управления масштабированием пока что не работают. Мы привяжем их в следующей части руководства.)

    ```xaml
    <AppBarButton Icon="Delete"
                  Label="Delete selected image"
                  Click="{x:Bind DeleteSelectedImage}" />
    ```

    Если вы уже знакомы с XAML, это значение **Click** может показаться необычным. В предыдущих версиях XAML в качестве этого значения использовался метод с определенной подписью обработчика событий, включавшей в себя, как правило, параметры отправителя событий и объект аргументов, характерных для разных событий. Этот способ также можно использовать, если вам нужны аргументы событий, но с помощью x:Bind можно обеспечивать связь и с другими методами. Например, если вам не нужны данные событий, можно выполнить привязку к методам без параметров, как в нашем случае.

    <!-- TODO add doc links about event binding - and doc links in general? -->

2. В файл MainPage.xaml.cs добавьте метод **DeleteSelectedImage**.

    ```c#
    private void DeleteSelectedImage() =>
        Images.Remove(ImageGridView.SelectedItem as ImageFileInfo);
    ```

    Этот метод удаляет выбранное изображение из коллекции **Images**. 

Теперь запустите приложение и удалите несколько изображений с помощью кнопки. Как видно, пользовательский интерфейс обновляется автоматически благодаря привязке данных и типу **ObservableCollection\<T\>** . 

> [!Note]
> В качестве дополнительной задачи попробуйте добавить две кнопки для перемещения выбранного изображения вверх или вниз по списку, затем используйте x:Bind, чтобы привязать их события Click к двум новым методам, аналогичным DeleteSelectedImage.
 
## <a name="part-3-set-up-the-zoom-slider"></a>Часть 3. Настройка ползунка масштабирования 

В этой части мы создадим односторонние привязки между элементом управления в шаблоне данных и ползунком масштабирования, расположенным за пределами шаблона. Также вы узнаете о возможности использования привязки данных со многими свойствами элементов управления, а не только с самыми очевидными, таким как **TextBlock.Text** и **Image.Source**. 

**Привязка шаблона данных изображения к ползунку масштабирования**

* Найдите **DataTemplate** с именем **ImageGridView_DefaultItemTemplate** и замените значения **Height** и **Width** элемента управления **Grid** в верхней части шаблона.

    **До**
    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate" 
                  x:DataType="local:ImageFileInfo">
        <Grid Height="200"
              Width="200"
              Margin="{StaticResource LargeItemMargin}">
    ```
    
    **После**
    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate" 
                  x:DataType="local:ImageFileInfo">
        <Grid Height="{Binding Value, ElementName=ZoomSlider}"
              Width="{Binding Value, ElementName=ZoomSlider}"
              Margin="{StaticResource LargeItemMargin}">
    ```
    
    <!-- TODO talk about dependency properties --> 
    
    Вы обратили внимание на то, что это выражения **Binding**, а не **x:Bind**? Это старый способ создания привязок данных, который практически полностью устарел. **x:Bind** выполняет практически все действия, что и **Binding**, а также многие другие. Однако при использовании **x:Bind** в шаблоне данных он привязывается к типу, объявленному в значении **x:DataType**. Как привязать какой-либо элемент в шаблоне к элементу в коде XAML страницы или в коде программной части? Для этого нужно использовать старомодное выражение **Binding**. 
    
    Выражения **Binding** не распознают значение **x:DataType**, но у них есть значения **ElementName**, которые работают практически так же. Эти значения сообщают механизму привязки, что **Binding Value** — это привязка к свойству **Value** указанного элемента на странице (то есть элемента с этим значением **x:Name**). Если требуется выполнить привязку к свойству в коде программной части, результат был бы похож на ```{Binding MyCodeBehindProperty, ElementName=page}```, где **page** ссылается на значение **x:Name**, установленное в элементе **Page** в коде XAML. 
    
    > [!NOTE]
    > По умолчанию выражения **Binding** являются *односторонними*, то есть они автоматически обновляют пользовательский интерфейс при изменении значения привязанного свойства. 
    > 
    > И наоборот, по умолчанию привязка **x:Bind** является *однократной*, то есть все изменения в привязанном свойстве игнорируются. Такова ситуация по умолчанию, поскольку это наиболее высокопроизводительный вариант, а большинство привязок относятся к статическим данным с доступом только для чтения. 
    >
    > Из этого важно понять, что при использовании **x:Bind** со свойствами, которые могут изменять свои значения, необходимо добавить ```Mode=OneWay``` или ```Mode=TwoWay```. Примеры этого приведены в следующем разделе.

Запустите приложение и измените размеры шаблона изображения с помощью ползунка. Как видно, результат вполне ощутим без использования больших объемов кода. 

![Запущенное приложение с отображенным ползунком масштабирования](../design/basics/images/xaml-basics/gallery-with-zoom-control.png)

> [!NOTE]
> В качестве дополнительной задачи попробуйте привязать другие свойства пользовательского интерфейса к свойству **Value** ползунка масштабирования или к другим ползункам, добавленным после ползунка масштабирования. Например, можно привязать свойство **FontSize** элемента **TitleTextBlock** к новому ползунку со значением по умолчанию **24**. Следует установить минимальное и максимальное значения в разумных пределах.

## <a name="part-4-improve-the-zoom-experience"></a>Часть 4. Повышение удобства масштабирования 

В этой части мы добавим пользовательское свойство **ItemSize** в код программной части и создадим односторонние привязки между шаблоном изображения и новым свойством. Значение **ItemSize** будет изменяться ползунком масштабирования и другими факторами, такими как переключатель **Fit to screen** (По размеру экрана) и размер окна, благодаря чему пользователям будет удобнее взаимодействовать с приложением. 

В отличие от встроенных свойств элементов управления, пользовательские свойства не обновляют пользовательский интерфейс автоматически, даже при использовании одно- и двухсторонних привязок. Они эффективно работают с **однократными** привязками, но если вы хотите, чтобы изменения в свойствах отображались в пользовательском интерфейсе, необходимо выполнить ряд действий. 

**Создание свойства ItemSize для обновления пользовательского интерфейса**

1. В файле MainPage.xaml.cs измените подпись класса **MainPage**, чтобы он реализовал интерфейс **INotifyPropertyChanged**.

    **До**
    ```c#
    public sealed partial class MainPage : Page
    ```

    **После**
    ```c#
    public sealed partial class MainPage : Page, INotifyPropertyChanged
    ```

    Таким образом система привязки узнает, что у класса MainPage есть событие PropertyChanged (добавляется ниже), которое могут ожидать привязки для обновления пользовательского интерфейса. 

2. Добавьте событие **PropertyChanged** в класс **MainPage**.

    ```c#
    public event PropertyChangedEventHandler PropertyChanged;
    ```

    Это событие обеспечивает полноценную реализацию, необходимую интерфейсу **INotifyPropertyChanged**. Однако чтобы это событие дало результат, необходимо явным образом породить его в пользовательских свойствах. 

3. Добавьте свойство **ItemSize** и породите событие **PropertyChanged** в его методе задания.

    ```c#
    public double ItemSize
    {
        get => _itemSize;
        set
        {
            if (_itemSize != value)
            {
                _itemSize = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(ItemSize)));
            }
        }
    }
    private double _itemSize;
    ```

    Свойство **ItemSize** предоставляет значение частного поля **_itemSize**. Использование подобного резервного поля позволяет свойству проверить, идентично ли новое значение старому, прежде чем будет вызвано потенциально ненужное событие **PropertyChanged**.

    Само событие вызывается методом **Invoke**. Знак вопроса нужен, чтобы проверить, имеет ли событие **PropertyChanged** нулевое значение, то есть добавлены ли обработчики событий. Каждая односторонняя или двухсторонняя привязка незаметно добавляет обработчик событий, но если прослушиватели отсутствуют, никаких дальнейших действий выполнено не будет. Однако если значение события **PropertyChanged** не нулевое, вызывается метод **Invoke** со ссылкой на источник события (саму страницу, представленную ключевым словом **this**) и объект **event-args**, указывающий имя свойства. Благодаря этим сведениям все односторонние или двухсторонние привязки к свойству **ItemSize** будут в курсе любых изменений и смогут обновить привязанный пользовательский интерфейс. 

4. В файле MainPage.xaml найдите **DataTemplate** с именем **ImageGridView_DefaultItemTemplate** и замените значения **Height** и **Width** элемента управления **Grid** в верхней части шаблона. (Если вы выполнили привязку между элементами управления в предыдущей части этого руководства, нужно лишь заменить **Value** на **ItemSize** и **ZoomSlider** на **page**. Обязательно выполните это действие как для Height, так и для Width.)

    **До**
    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate" 
                  x:DataType="local:ImageFileInfo">
        <Grid Height="{Binding Value, ElementName=ZoomSlider}"
            Width="{Binding Value, ElementName=ZoomSlider}"
            Margin="{StaticResource LargeItemMargin}">
    ```
    
    **После**
    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate" 
                  x:DataType="local:ImageFileInfo">
        <Grid Height="{Binding ItemSize, ElementName=page}"
              Width="{Binding ItemSize, ElementName=page}"
              Margin="{StaticResource LargeItemMargin}">
    ```

Теперь пользовательский интерфейс может реагировать на изменения **ItemSize**, поэтому можно начать вносить сами изменения. Как упоминалось выше, значение **ItemSize** вычисляется на основе текущего состояния различных элементов управления пользовательского интерфейса, но вычисление должно выполняться при изменении состояния этих элементов управления. Для этого необходимо использовать привязку событий, чтобы определенные изменения в пользовательском интерфейсе вызывали вспомогательный метод, обновляющий **ItemSize**. 

**Обновление значения свойства ItemSize**

1. Добавьте метод **DetermineItemSize** в файл MainPage.xaml.cs.

    ```c#
    private void DetermineItemSize()
    {
        if (FitScreenToggle != null
            && FitScreenToggle.IsOn == true
            && ImageGridView != null
            && ZoomSlider != null)
        {
            // The 'margins' value represents the total of the margins around the
            // image in the grid item. 8 from the ItemTemplate root grid + 8 from
            // the ItemContainerStyle * (Right + Left). If those values change, 
            // this value needs to be updated to match.
            int margins = (int)this.Resources["LargeItemMarginValue"] * 4;
            double gridWidth = ImageGridView.ActualWidth -
                (int)this.Resources["DesktopWindowSidePaddingValue"];
    
            if (AnalyticsInfo.VersionInfo.DeviceFamily == "Windows.Mobile" &&
                UIViewSettings.GetForCurrentView().UserInteractionMode ==
                    UserInteractionMode.Touch)
            {
                margins = (int)this.Resources["SmallItemMarginValue"] * 4;
                gridWidth = ImageGridView.ActualWidth -
                    (int)this.Resources["MobileWindowSidePaddingValue"];
            }
    
            double ItemWidth = ZoomSlider.Value + margins;
            // We need at least 1 column.
            int columns = (int)Math.Max(gridWidth / ItemWidth, 1);
    
            // Adjust the available grid width to account for margins around each item.
            double adjustedGridWidth = gridWidth - (columns * margins);
    
            ItemSize = (adjustedGridWidth / columns);
        }
        else
        {
            ItemSize = ZoomSlider.Value;
        }
    }
    ```

2. В файле MainPage.xaml перейдите в начало файла и добавьте привязку события **SizeChanged** в элемент **Page**.

    **До**
    ```xaml
    <Page x:Name="page"  
    ```

    **После**
    ```xaml
    <Page x:Name="page" 
          SizeChanged="{x:Bind DetermineItemSize}"
    ```

3. Найдите **Slider** с именем **ZoomSlider** и добавьте привязку события **ValueChanged**.

    **До**
    ```xaml
    <Slider x:Name="ZoomSlider"
    ```

    **После**
    ```xaml
    <Slider x:Name="ZoomSlider"
            ValueChanged="{x:Bind DetermineItemSize}"
    ```

4. Найдите **ToggleSwitch** с именем **FitScreenToggle** и добавьте привязку события **Toggled**.

    **До**
    ```xaml
    <ToggleSwitch x:Name="FitScreenToggle"
    ```

    **После**
    ```xaml
    <ToggleSwitch x:Name="FitScreenToggle"
                  Toggled="{x:Bind DetermineItemSize}"
    ```

Запустите приложение и измените размеры шаблона изображения с помощью ползунка масштабирования и переключателя **Fit to screen** (По размеру экрана). Как видно, последние изменения повышают удобство масштабирования и изменения размера, не нарушая организацию кода. 

![Запущенное приложение с масштабированием по размеру экрана](../design/basics/images/xaml-basics/gallery-with-fit-to-screen.png)

> [!NOTE]
> В качестве дополнительной задачи попробуйте добавить **TextBlock** после **ZoomSlider** и привязать свойство **Text** к свойству **ItemSize**. Так как оно находится за пределами шаблона данных, можно использовать **x:Bind** вместо **Binding**, как в предыдущих привязках **ItemSize**.  
}

## <a name="part-5-enable-user-edits"></a>Часть 5. Предоставление пользователям возможности вносить изменения

В этой части мы создадим двухсторонние привязки, чтобы дать пользователям возможность обновлять значения, включая название, оценку и различные визуальные эффекты изображения. 

Для этого необходимо обновить существующую страницу **DetailPage**, которая предоставляет средство просмотра одного изображения, элемент управления масштабированием и пользовательский интерфейс для редактирования.  

Однако в первую очередь необходимо подключить страницу **DetailPage**, чтобы приложение переходило к ней, когда пользователь щелкает изображение в представлении коллекции.

**Подключение DetailPage**

1. В файле MainPage.xaml найдите **GridView** с именем **ImageGridView** и добавьте значение **ItemClick**. 

    > [!TIP] 
    > Если изменение ввести ниже, а не скопировать и вставить, отобразится всплывающее окно IntelliSense с текстом \<New Event Handler\> (Новый обработчик событий). При нажатии клавиши TAB в качестве значения будет использовано имя обработчика метода по умолчанию, а метод, показанный в следующем шаге, будет автоматически снабжен заглушкой. Затем можно перейти к методу в коде программной части с помощью клавиши F12. 

    **До**
    ```xaml
    <GridView x:Name="ImageGridView"
    ```

    **После**
    ```xaml
    <GridView x:Name="ImageGridView"
              ItemClick="ImageGridView_ItemClick"
    ```

    > [!NOTE] 
    > В этом случае мы используем стандартный обработчик событий вместо выражения x:Bind. Причина этого в том, что нам необходимо видеть данные событий, как показано далее. 

2. В файле MainPage.xaml.cs добавьте обработчик событий (или заполните его, если вы воспользовались подсказкой на предыдущем шаге).

    ```c#
    private void ImageGridView_ItemClick(object sender, ItemClickEventArgs e)
    {
        this.Frame.Navigate(typeof(DetailPage), e.ClickedItem);
    }
    ```

    Этот метод переходит на страницу сведений, передает выбранный пользователем элемент, который является объектом **ImageFileInfo**, используемым **DetailPage.OnNavigatedTo** для инициализации страницы. В этом руководстве вам не придется реализовывать данный метод, но вы можете ознакомиться с его функциями. 
    
3. (Необязательно.) Удалите или закомментируйте все элементы управления, добавленные в предыдущих точках воспроизведения, которые работают с выбранным на данный момент изображением. Если их оставить, ничего плохого не произойдет, но при этом будет гораздо сложнее выбрать изображение без перехода на страницу сведений. 

Подключив две страницы, запустите приложение и изучите его. Работает все, кроме элементов управления на панели редактирования: они не реагируют при попытке изменения значений. 

Как видно, в текстовом поле названия отображается название, а также его можно изменить. Для подтверждения изменений необходимо перевести фокус на другой элемент управления, но название в верхнем левом углу экрана еще не обновляется. 

Все элементы управления уже привязаны с помощью обычных выражений **x:Bind**, рассмотренных в части 1. Если вы помните, это значит, что все эти привязки являются однократными, поэтому изменения значений не регистрируются. Чтобы устранить эту проблему, нужно лишь превратить их в двухсторонние привязки. 

**Обеспечение интерактивности элементов управления для редактирования**

1. В файле DetailPage.xaml найдите элемент **TextBlock** с именем **TitleTextBlock** и элемент управления **RadRating** после него, а затем измените их выражения **x:Bind**, чтобы они включали в себя **Mode=TwoWay**.

    **До**
    ```xaml
    <TextBlock x:Name="TitleTextBlock"
               Text="{x:Bind item.ImageTitle}"
               ... >
    <telerikInput:RadRating Value="{x:Bind item.ImageRating}"
                            ... >
    ```

    **После**
    ```xaml
    <TextBlock x:Name="TitleTextBlock" 
               Text="{x:Bind item.ImageTitle, Mode=TwoWay}" 
               ... >
    <telerikInput:RadRating Value="{x:Bind item.ImageRating, Mode=TwoWay}" 
                            ... >
    ```

2. Сделайте то же самое для всех ползунков эффектов, расположенных после элемента управления оценкой.

    ```xaml
    <Slider Header="Exposure"    ... Value="{x:Bind item.Exposure, Mode=TwoWay}" ...
    <Slider Header="Temperature" ... Value="{x:Bind item.Temperature, Mode=TwoWay}" ... 
    <Slider Header="Tint"        ... Value="{x:Bind item.Tint, Mode=TwoWay}" ... 
    <Slider Header="Contrast"    ... Value="{x:Bind item.Contrast, Mode=TwoWay}" ... 
    <Slider Header="Saturation"  ... Value="{x:Bind item.Saturation, Mode=TwoWay}" ...
    <Slider Header="Blur"        ... Value="{x:Bind item.Blur, Mode=TwoWay}" ... 
    ```

Двухсторонний режим ожидаемо означает, что данные перемещаются в обоих направлениях при внесении изменений на любой из сторон. 

Как и в случае с ранее рассмотренными односторонними привязками, двухсторонние привязки будут обновлять пользовательский интерфейс при каждом изменении привязанных свойств благодаря реализации **INotifyPropertyChanged** в классе **ImageFileInfo**. Однако при двухсторонней привязке значения также будут перемещаться из пользовательского интерфейса в привязанные свойства при каждом взаимодействии пользователя с элементом управления. На стороне XAML больше не требуется никаких действий. 

Запустите приложение и попробуйте воспользоваться элементами управления для редактирования. Как видно, вносимые изменения влияют на значения изображений и сохраняются при возврате на главную страницу. 

## <a name="part-6-format-values-through-function-binding"></a>Часть 6. Форматирование значений через привязку функции

Остается последняя проблема. При перемещении ползунков эффектов метки рядом с ними не меняются. 

![Ползунки эффектов со значениями меток по умолчанию](../design/basics/images/xaml-basics/effect-sliders-before-label-fix.png)

Последняя часть этого руководства посвящена добавлению привязок для форматирования отображаемых значений ползунков.

**Привязка меток ползунков эффектов и форматирование отображаемых значений**

1. Найдите **TextBlock** после ползунка **Exposure** (Экспозиция) и замените **Text** значением выражения привязки, показанного здесь.

    **До**
    ```xaml
    <Slider Header="Exposure" ... />
    <TextBlock ... Text="0.00" />
    ```

    **После**
    ```xaml
    <Slider Header="Exposure" ... />
    <TextBlock ... Text="{x:Bind item.Exposure.ToString('N', culture), Mode=OneWay}" />
    ```

    Это называется привязкой функции, так как выполняется привязка к возвращаемому значению метода. Метод должен быть доступен через код программной части страницы или через тип **x:DataType** при использовании шаблона данных. В этом случае используется знакомый метод .NET **ToString**, доступный через свойство элемента страницы, а затем через свойство **Exposure** этого элемента. (В этом примере показано, как выполнить привязку к методам и свойствам, расположенным глубоко в цепи связей.)

    Привязка функции — отличный способ форматирования отображаемых значений, так как при этом можно передать другие источники привязки, например аргументы методов, а выражение привязки будет ожидать передачи этих значений, как и должно происходить в одностороннем режиме. В этом примере аргумент **culture** является ссылкой на неизменное поле, реализованное в коде программной части, но он также мог бы быть свойством, вызывающим события **PropertyChanged**. В таком случае в результате любых изменений значения свойства выражение **x:Bind** вызывало бы **ToString** с новым значением, а затем обновляло бы пользовательский интерфейс с использованием полученного результата. 

2. Сделайте то же самое для элементов **TextBlock**, служащих метками для других ползунков эффектов.

    ```xaml
    <Slider Header="Temperature" ... />
    <TextBlock ... Text="{x:Bind item.Temperature.ToString('N', culture), Mode=OneWay}" />

    <Slider Header="Tint" ... />
    <TextBlock ... Text="{x:Bind item.Tint.ToString('N', culture), Mode=OneWay}" />

    <Slider Header="Contrast" ... />
    <TextBlock ... Text="{x:Bind item.Contrast.ToString('N', culture), Mode=OneWay}" />

    <Slider Header="Saturation" ... />
    <TextBlock ... Text="{x:Bind item.Saturation.ToString('N', culture), Mode=OneWay}" />

    <Slider Header="Blur" ... />
    <TextBlock ... Text="{x:Bind item.Blur.ToString('N', culture), Mode=OneWay}" />
    ```

Теперь при запуске приложения все работает, включая метки ползунков. 

![Ползунки эффектов с работающими метками](../design/basics/images/xaml-basics/effect-sliders-after-label-fix.png)

> [!NOTE]
> Попробуйте использовать привязку функции с элементом **TextBlock** из последней точки воспроизведения и привязать его к новому методу, который возвращает строку в формате "000 x 000" при передаче значения **ItemSize**.


## <a name="conclusion"></a>Заключение

В этом руководстве вы узнали о привязке данных и некоторых доступных возможностях. Прежде чем мы закончим, следует иметь в виду, что не все элементы можно привязать. Иногда значения, которые вы пытаетесь соединить, несовместимы со свойствами, которые вы пытаетесь привязать. Привязка подходит для решения множества задач, но работает не во всех случаях.

Примером задачи, которую невозможно решить с помощью привязки, служит ситуация, когда элемент управления лишен подходящих для привязки свойств, например если это функция масштабирования страницы сведений. Ползунок масштабирования должен взаимодействовать с элементом **ScrollViewer**, отображающим изображение, но **ScrollViewer** можно обновить только через его метод **ChangeView**. В этом случае мы используем стандартные обработчики событий, чтобы обеспечить синхронизацию **ScrollViewer** и ползунка масштабирования. Подробные сведения приведены в разделах, посвященных методам **ZoomSlider_ValueChanged** и **MainImageScroll_ViewChanged** **DetailPage**.

Тем не менее, привязка — эффективный и универсальный способ упростить код и отделить логику пользовательского интерфейса от логики данных. Это сильно поможет вам в регулировке обоих типов логики и сократит вероятность появления в них ошибок. 

В качестве примера разделения пользовательского интерфейса и данных можно привести свойство **ImageFileInfo.ImageTitle**. Это свойство (и свойство **ImageRating**) несколько отличается от свойства **ItemSize**, созданного в части 4, так как его значение хранится в метаданных файлов (предоставляемых через тип **ImageProperties**), а не в поле. Кроме того, **ImageTitle** возвращает значение **ImageName** (используется имя файла) если в метаданных файлов нет названия. 

```c#
public string ImageTitle
{
    get => String.IsNullOrEmpty(ImageProperties.Title) ? ImageName : ImageProperties.Title;
    set
    {
        if (ImageProperties.Title != value)
        {
            ImageProperties.Title = value;
            var ignoreResult = ImageProperties.SavePropertiesAsync();
            OnPropertyChanged();
        }
    }
}
```

Как видно, метод установки значений обновляет свойство **ImageProperties.Title**, а затем вызывает **SavePropertiesAsync** для записи нового значения в файл. (Это асинхронный метод, но ключевое слово **await** невозможно и не следует использовать в свойстве, так как методы получения и задания свойств должны выполняться мгновенно. Поэтому вместо использования ключевого слова необходимо вызвать метод и проигнорировать возвращенный им объект **Task**.) 

<!-- TODO more screenshots? -->

## <a name="going-further"></a>Дальнейшая работа

Завершив это практическое занятие, вы получили достаточный объем знаний о привязке, чтобы в дальнейшем решать задачи самостоятельно.

Как вы могли заметить, при изменении уровня масштабирования на странице сведений он автоматически сбрасывается при возврате и повторном выборе того же изображения. Сможете ли вы разобраться, как сохранить и восстановить уровень масштабирования отдельно для каждого изображения? Всего хорошего!
    
В этом руководстве должны быть все необходимые сведения, но если вам нужны дополнительные рекомендации, воспользуйтесь доступной документацией по привязке данных. Начните с этого:

+ [Расширение разметки {x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension)
+ [Подробно о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth)