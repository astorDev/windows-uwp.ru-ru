---
author: jwmsft
title: Руководство по созданию пользовательского интерфейса
description: В этой статье рассматриваются основы создания пользовательских интерфейсов в XAML
keywords: XAML, UWP, начало работы
ms.author: jimwalk
ms.date: 08/30/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 5d54df07cd5f2ccc32098b17fd7c656900cba978
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5570943"
---
# <a name="tutorial-create-a-user-interface"></a>Руководство: создание пользовательского интерфейса

В этом руководстве вы узнаете, как создать базовый пользовательский интерфейс для программы редактирования изображений, используя следующие инструменты: 

+ Средства XAML в Visual Studio, такие как конструктор XAML, панель инструментов, редактор XAML, панель "Свойства" и структура документа для добавления элементов правления и содержимого в пользовательский интерфейс.
+ Наиболее общие панели макетов XAML, такие как RelativePanel, Grid и StackPanel.

Программа редактирования изображений содержит две страницы/экрана.

**Главная страница**— отображает представление фотоальбома, а также некоторые сведения о каждой файле изображения.

![MainPage](images/xaml-basics/mainpage.png)

**Страница сведений**— отображает одну выбранную фотографию. Всплывающее меню редактирования позволяет изменить, переименовать и сохранить фотографию.

![DetailPage](images/xaml-basics/detailpage.png)


## <a name="prerequisites"></a>Что вам понадобится

* Visual Studio 2017: [скачать Visual Studio Community 2017 (бесплатно)](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15&campaign=WinDevCenter&ocid=wdgcx-windevcenter-community-download) 
* Пакет SDK для Windows 10 (10.0.15063.468 или более поздней версии): [скачать последнюю версию Windows SDK (бесплатно)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)

## <a name="part-0-get-the-starter-code-from-github"></a>Часть 0. Получите начальный код на GitHub

В этом руководстве мы начнем с упрощенной версии примера PhotoLab. 

1. Последовательно выберите пункты [https://github.com/Microsoft/Windows-appsample-photo-lab](https://github.com/Microsoft/Windows-appsample-photo-lab). Вы перейдете на страницу GitHub с примером кода. 
2. Далее необходимо клонировать или скачать пример. Нажмите кнопку **Клонировать или скачать**. Откроется подменю.
    <figure>
        <img src="images/xaml-basics/clone-repo.png" alt="The Clone or download menu on GitHub">
        <figcaption>Меню <b>Клонировать или скачать</b> на странице GitHub с примером приложения для обработки фотографий.</figcaption>
    </figure>

    **Если вы не знакомы с GitHub:**
    
    А. Нажмите кнопку **Скачать ZIP-файл** и сохраните его локально. Будет выполнено скачивание ZIP-файла с необходимыми файлами проекта.
    Б. Извлеките файл. Используйте проводник, чтобы перейти к скачанному ZIP-файлу, щелкните его правой кнопкой мыши и выберите **Извлечь все...**. В. Перейдите к локальной копии примера кода, а затем в каталог `Windows-appsample-photo-lab-master\xaml-basics-starting-points\user-interface`.    

    **Если вы знакомы с GitHub:**

    А. Клонируйте основную ветвь репозитория локально.
    Б. Перейдите в каталог `Windows-appsample-photo-lab\xaml-basics-starting-points\user-interface`.

3. Откройте проект, щелкнув `Photolab.sln`.

## <a name="part-1-add-a-textblock-using-xaml-designer"></a>Часть 1. Добавление TextBlock с помощью конструктора XAML

Visual Studio предоставляет несколько средств для удобства создания пользовательского интерфейса XAML. Конструктор XAML позволяет перетаскивать элементы управления в область конструктора, чтобы увидеть, как они будут выглядеть, прежде чем запускать приложение. Панель "Свойства" дает возможность просмотреть и настроить все свойства элемента управления, которые являются активными в конструкторе. Панель "Структура документа" отображает структуру родительских и дочерних элементов визуального дерева XAML для пользовательского интерфейса. Редактор XAML позволяет напрямую изменять разметку XAML.

Ниже представлен пользовательский интерфейс Visual Studio с пометками около каждого инструмента.

![Макет Visual Studio](images/xaml-basics/visual-studio-tools.png)

Каждый из этих инструментов помогает в создании пользовательского интерфейса, и в этом учебнике мы воспользуемся каждым из них. Прежде всего, добавим элемент управления с помощью конструктора XAML. 

**Добавление элемента правления с помощью конструктора XAML.**

1. Дважды щелкните файл **MainPage.xaml** в обозревателе решений, чтобы открыть его. Отобразится главная страница приложения без каких-либо добавленных элементов пользовательского интерфейса.

2. Прежде чем продолжить, необходимо внести некоторые изменения в Visual Studio.

    - Убедитесь, что в качестве платформы решения выбрана архитектура x86 или x64, а не ARM.
    - Настройте главную страницу конструктора XAML на предварительный просмотр рабочего стола с диагональю 13,3 дюйма.

    Оба параметра находятся в верхней части окна, как показано здесь.

    ![Параметры VS](images/xaml-basics/layout-vs-settings.png)

    Теперь можно запустить приложение, но оно будет практически пустым. Добавим элементы пользовательского интерфейса, чтобы сделать его интереснее.

3. На панели инструментов разверните пункт **Общие элементы управления XAML** и найдите элемент управления [TextBlock](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock). Перетащите элемент TextBlock в область конструктора ближе к верхнему левому углу страницы.

    На страницу добавляется TextBlock, и конструктор устанавливает определенные свойства, основываясь на своем предположении о требуемом макете. Вокруг TextBlock появится синее выделение, указывающее на то, что этот объект теперь стал активным. Обратите внимание на поля и другие параметры, добавленные конструктором. Код XAML будет выглядеть следующим образом. Не стоит беспокоиться, если он отформатирован несколько иначе. В нашем примере он сокращен для удобства чтения.

    ```xaml
    <TextBlock x:Name="textBlock"
               HorizontalAlignment="Left"
               Margin="351,44,0,0"
               TextWrapping="Wrap"
               Text="TextBlock"
               VerticalAlignment="Top"/>
    ```

    В дальнейших шагах мы обновим эти значения.

4. На панели "Свойства" измените значение поля "Имя" элемента TextBlock с **textBlock** на **TitleTextBlock**. (Убедитесь, что TextBlock по-прежнему является активным объектом.)

5. В разделе **Общие** измените значение поля "Текст" на **Коллекция**.

    ![Свойства TextBlock](images/xaml-basics/text-block-properties.png)

    Теперь код XAML в редакторе XAML будет выглядеть следующим образом.

    ```xaml
    <TextBlock x:Name="TitleTextBlock"
               HorizontalAlignment="Left"
               Margin="351,44,0,0"
               TextWrapping="Wrap"
               Text="Collection"
               VerticalAlignment="Top"/>
    ```

6. Для расположение элемента TextBlock необходимо сначала удалить значения свойств, добавленные Visual Studio. На панели "Структура документа" щелкните правой кнопкой мыши **TitleTextBlock** и выберите **Макет > Сбросить все**.

![Структура документа](images/xaml-basics/doc-outline-reset.png)

7. На панели "Свойства" введите **margin** в поле поиска, чтобы быстро найти свойство **Margin**. Задайте значения левого и нижнего полей равными 24.

    ![Поля TextBlock](images/xaml-basics/margins.png)

    Поля обеспечивают базовое расположение элемента на странице. Ими удобно выполнять точную настройку макета, но не следует использовать большие значения полей, подобные добавленным Visual Studio, так как это усложняет адаптацию пользовательского интерфейса под разные размеры экрана.

    Дополнительные сведения см. в статье [Выравнивание, поля и заполнение](../layout/alignment-margin-padding.md).

8. На панели структуры документа правой кнопкой мыши щелкните **TitleTextBlock**, а затем **Изменить стиль > Применить ресурс > TitleTextBlockStyle**. При этом к тексту названия будет применен определенный системой стиль.

    ```xaml
    <TextBlock x:Name="TitleTextBlock"
               TextWrapping="Wrap"
               Text="Collection"
               Margin="24,0,0,24"
               Style="{StaticResource TitleTextBlockStyle}"/>
    ```

9. На панели "Свойства" введите **textwrapping** в поле поиска, чтобы быстро найти свойство **TextWrapping**. Щелкните _метку свойства_ для свойства **TextWrapping**, чтобы открыть его меню. (_Маркер свойства_ — это маленький квадратный символ справа от каждого значения свойства. _Метка свойства_ окрашена в черный цвет и указывает, что свойству задано значение не по умолчанию.) В меню **Свойство** выберите **Сбросить**, чтобы сбросить свойство TextWrapping.

    Visual Studio добавляет это свойство, но оно уже задано в примененном стиле, поэтому здесь оно не потребуется.

Мы добавили первую часть пользовательского интерфейса в приложение. Теперь запустите приложение и посмотрите, как оно выглядит.

Вы могли обратить внимание, что в конструкторе XAML приложение отображало белый текст на черном фоне, но после запуска отображается черный текст на белом фоне. Это связано с тем, что в Windows есть темная и светлая темы, а тема по умолчанию зависит от устройства. На компьютере по умолчанию используется светлая тема. Щелкните значок в виде шестеренки в верхней части конструктора XAML, чтобы открыть параметры предварительного просмотра устройства, и измените тему на светлую, чтобы в конструкторе XAML приложение выглядело так же, как и на компьютере.

> [!NOTE]
> В этой части учебника мы добавили элемент управления путем перетаскивания. Также можно добавить элемент управления путем двойного щелчка по нему на панели инструментов. Попробуйте сделать это и обратите внимание, чем отличается код XAML, созданный Visual Studio.

## <a name="part-2-add-a-gridview-control-using-the-xaml-editor"></a>Часть 2. Добавление элемента управления GridView с помощью редактора XAML

В части 1 мы попробовали использовать конструктор XAML и некоторые другие средства в составе Visual Studio. В этой части мы будем использовать редактор XAML для непосредственной работы с разметкой XAML. По мере изучения XAML этот способ может показаться вам более эффективным.

Прежде всего, необходимо заменить элемент [Grid](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid) корневого макета на [**RelativePanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel). Элемент RelativePanel упрощает изменение расположения блоков пользовательского интерфейса относительно панели и других элементов пользовательского интерфейса. Его преимущества продемонстрированы в учебнике [Адаптивный макет XAML](xaml-basics-adaptive-layout.md). 

Затем мы добавим элемент управления [GridView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.gridview) для отображения данных.

**Добавление элемента управления с помощью редактора XAML**

1. В редакторе XAML измените корневой элемент **Grid** на **RelativePanel**.

    **До**
    ```xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
          <TextBlock x:Name="TitleTextBlock"
                     Text="Collection"
                     Margin="24,0,0,24"
                     Style="{StaticResource TitleTextBlockStyle}"/>
    </Grid>
    ```

    **После**
    ```xaml
    <RelativePanel Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <TextBlock x:Name="TitleTextBlock"
                   Text="Collection"
                   Margin="24,0,0,24"
                   Style="{StaticResource TitleTextBlockStyle}"/>
    </RelativePanel>
    ```

    Дополнительные сведения об использовании **RelativePanel** в макете см. в разделе [Панели макета](https://docs.microsoft.com/windows/uwp/layout/layout-panels#relativepanel).

2. Под элементом **TextBlock** добавьте **элемент управления GridView** с именем "ImageGridView". Настройте _присоединенные свойства_ **RelativePanel**, чтобы разместить элемент управления под текстом названия и растянуть его во всю ширину экрана.

    **Добавьте этот код XAML**

    ```xaml
    <GridView x:Name="ImageGridView"
              Margin="0,0,0,8"
              RelativePanel.AlignLeftWithPanel="True"
              RelativePanel.AlignRightWithPanel="True"
              RelativePanel.Below="TitleTextBlock"/>
    ```

    **После элемента TextBlock**
    ```xaml
    <RelativePanel Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <TextBlock x:Name="TitleTextBlock"
                   Text="Collection"
                   Margin="24,0,0,24"
                   Style="{StaticResource TitleTextBlockStyle}"/>
        
        <!-- Add the GridView here. -->

    </RelativePanel>
    ```

    Подробнее о присоединенных свойствах панелей см. в разделе [Панели макета](https://docs.microsoft.com/windows/uwp/layout/layout-panels).

3. Чтобы элемент **GridView** отображал какие-либо данные, необходимо предоставить ему коллекцию данных. Откройте файл MainPage.xaml.cs и найдите метод **GetItemsAsync**. Этот метод заполняет коллекцию с именем "Images", которая является свойством, добавленным на страницу MainPage.

    После цикла **foreach** в **GetItemsAsync** добавьте эту строку кода.

    ```csharp
    ImageGridView.ItemsSource = Images;
    ```

    Она установит для свойства **ItemsSource** элемента GridView значение коллекции **Images** приложения и предоставит элементу **GridView** данные для отображения.

На этом этапе можно запустить приложение и убедиться, что все работает должным образом. Приложение должно выглядеть следующим образом.

![Пользовательский интерфейс приложения: контрольная точка 1](images/xaml-basics/layout-0.png)

Вы заметите, что приложение пока что не показывает изображения. По умолчанию оно отображает значение ToString типа данных в коллекции. Далее мы создадим шаблон данных, чтобы определить способ отображения данных.

> [!NOTE]
> Дополнительные сведения об использовании **RelativePanel** в макете см. в статье [Панели макета](https://docs.microsoft.com/windows/uwp/layout/layout-panels#relativepanel). Ознакомьтесь с ней и поэкспериментируйте с различными макетами путем изменения значений присоединенных свойств RelativePanel в элементах **TextBlock** и **GridView**.

## <a name="part-3-add-a-datatemplate-to-display-your-data"></a>Часть 3. Добавление DataTemplate для отображения данных

Теперь мы создадим шаблон [DataTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.datatemplate), который сообщает элементу GridView, как следует отображать данные. Полную информацию о шаблонах данных см. в разделе [Контейнеры и шаблоны элементов](../controls-and-patterns/item-containers-templates.md).

В этой части мы будем добавлять только заполнители, чтобы создать необходимый макет. В учебнике [Привязка данных XAML](../../data-binding/xaml-basics-data-binding.md) эти заполнители будут заменяться реальными данными из класса **ImageFileInfo**. Ознакомиться с объектом данных можно в файле ImageFileInfo.cs.

**Добавление шаблона данных в представление сетки**

1. Откройте файл MainPage.xaml.

2. Для отображение оценки используется элемент управления **RadRating** из пакета NuGet [Пользовательский интерфейс Telerik для UWP](https://github.com/telerik/UI-For-UWP). Добавьте ссылку на пространство имен XAML, которая указывает это пространство имен для элементов управления Telerik. Разместите ее в открывающий тег **Page** сразу после других записей "xmlns:".

    **Добавьте этот код XAML**

    ```xaml
    xmlns:telerikInput="using:Telerik.UI.Xaml.Controls.Input"
    ```

    **После последней записи "xmlns:"**

    ```xaml
    <Page x:Name="page"
      x:Class="PhotoLab.MainPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      xmlns:local="using:PhotoLab"
      xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
      xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
      xmlns:telerikInput="using:Telerik.UI.Xaml.Controls.Input"
      mc:Ignorable="d"
      NavigationCacheMode="Enabled">
    ```

    Подробнее о пространствах имен XAML см. в разделе [Пространства имен XAML и их сопоставление](https://docs.microsoft.com/windows/uwp/xaml-platform/xaml-namespaces-and-namespace-mapping).

3. На панели "Структура документа" щелкните правой кнопкой мыши **ImageGridView**. В контекстном меню выберите **Изменить дополнительные шаблоны > Изменить созданные элементы (ItemTemplate) > Создать пустой...**. Откроется диалоговое окно **Создать ресурс**.

4. В этом диалоговом окне измените значение ключа Name на **ImageGridView_DefaultItemTemplate**, а затем нажмите кнопку **ОК**.

    После нажатия кнопки **ОК** произойдет ряд действий.

    - В раздел Page.Resources файла MainPage.xaml будет добавлен элемент **DataTemplate**.

        ```xaml
        <Page.Resources>
            <DataTemplate x:Key="ImageGridView_DefaultItemTemplate">
                <Grid/>
            </DataTemplate>
        </Page.Resources>
        ```

    - Область панели "Структура документа" устанавливается на этот элемент **DataTemplate**.

        После создания шаблона данных можно нажать стрелку "вверх" в верхнем левом углу панели "Структура документа", чтобы вернуться к области страницы.

    - Свойству **ItemTemplate** элемента GridView устанавливается значение ресурса **DataTemplate**.

    ```xaml
        <GridView x:Name="ImageGridView"
                  Margin="0,0,0,8"
                  RelativePanel.AlignLeftWithPanel="True"
                  RelativePanel.AlignRightWithPanel="True"
                  RelativePanel.Below="TitleTextBlock"
                  ItemTemplate="{StaticResource ImageGridView_DefaultItemTemplate}"/>
    ```

5. В ресурсе **ImageGridView_DefaultItemTemplate** задайте свойствам Height и Width элемента **Grid** значение **300**, а свойству Margin— **8**. Затем добавьте две строки и установите свойству Height второй строки значение **Auto**.

    **До**
    ```xaml
    <Grid/>
    ```

    **После**
    ```xaml
    <Grid Height="300"
          Width="300"
          Margin="8">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
    </Grid>
    ```

    Подробнее о макетах сетки см. в разделе [Панели макета](https://docs.microsoft.com/windows/uwp/layout/layout-panels#grid).

6. Добавьте элементы управления в сетку.

    А. Добавьте элемент управления **Image** в первую строку сетки. Здесь будет отображаться изображение, но на данный момент в качестве заполнителя будет использоваться логотип приложения в Магазине.

    Б. Добавьте элементы управления **TextBlock** для отображения названия, типа файла и размеров изображения. Чтобы сделать это, используйте элементы управления **StackPanel** для расположения текстовых блоков.

    Дополнительные сведения о макете **StackPanel** см. в разделе [Панели макета](https://docs.microsoft.com/windows/uwp/layout/layout-panels#stackpanel)

    В. Добавьте элемент управления **RadRating** во внешний (вертикальный) элемент **StackPanel**. Разместите его после внутреннего (горизонтального) элемента **StackPanel**.

    **Окончательный шаблон**

    ```xaml
    <Grid Height="300"
          Width="300"
          Margin="8">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Image x:Name="ItemImage"
               Source="Assets/StoreLogo.png"
               Stretch="Uniform" />

        <StackPanel Orientation="Vertical"
                    Grid.Row="1">
            <TextBlock Text="ImageTitle"
                       HorizontalAlignment="Center"
                       Style="{StaticResource SubtitleTextBlockStyle}" />
            <StackPanel Orientation="Horizontal"
                        HorizontalAlignment="Center">
                <TextBlock Text="ImageFileType"
                           HorizontalAlignment="Center"
                           Style="{StaticResource CaptionTextBlockStyle}" />
                <TextBlock Text="ImageDimensions"
                           HorizontalAlignment="Center"
                           Style="{StaticResource CaptionTextBlockStyle}"
                           Margin="8,0,0,0" />
            </StackPanel>

            <telerikInput:RadRating Value="3"
                                    IsReadOnly="True">
                <telerikInput:RadRating.FilledIconContentTemplate>
                    <DataTemplate>
                        <SymbolIcon Symbol="SolidStar"
                                    Foreground="White" />
                    </DataTemplate>
                </telerikInput:RadRating.FilledIconContentTemplate>
                <telerikInput:RadRating.EmptyIconContentTemplate>
                    <DataTemplate>
                        <SymbolIcon Symbol="OutlineStar"
                                    Foreground="White" />
                    </DataTemplate>
                </telerikInput:RadRating.EmptyIconContentTemplate>
            </telerikInput:RadRating>

        </StackPanel>
    </Grid>
    ```

Запустите приложение, чтобы просмотреть элемент **GridView** с только что созданным шаблоном элемента. Возможно, мы не увидите элемент управления оценки, так как он состоит из белых звезд на белом фоне. Мы изменим цвет фона позже.

![Пользовательский интерфейс приложения: контрольная точка 2](images/xaml-basics/layout-1.png)

## <a name="part-4-modify-the-item-container-style"></a>Часть 4. Изменение стиля контейнера элементов

Шаблон элемента управления содержит визуальные элементы, отображающие состояние, например выделение, наведение указателя и фокус. Эти визуальные элементы отрисовываются либо над шаблоном данных, либо под ним. В этой части мы изменим свойства **Background** и **Margin** шаблона элемента управления, чтобы у элементов **GridView** был серый фон.

**Изменение контейнера элементов**

1. На панели "Структура документа" щелкните правой кнопкой мыши **ImageGridView**. В контекстном меню выберите **Изменить дополнительные шаблоны > Изменить созданный контейнер элементов (ItemContainerStyle) > Изменить копию...**. Откроется диалоговое окно **Создать ресурс**.

2. В этом диалоговом окне измените значение ключа Name на **ImageGridView_DefaultItemContainerStyle**, а затем нажмите кнопку **ОК**.

    В раздел **Page.Resources** кода XAML добавляется копия стиля по умолчанию.

    ```xaml
    <Style x:Key="ImageGridView_DefaultItemContainerStyle" TargetType="GridViewItem">
        <Setter Property="FontFamily" Value="{ThemeResource ContentControlThemeFontFamily}"/>
        <Setter Property="FontSize" Value="{ThemeResource ControlContentThemeFontSize}"/>
        <Setter Property="Background" Value="{ThemeResource GridViewItemBackground}"/>
        <Setter Property="Foreground" Value="{ThemeResource GridViewItemForeground}"/>
        <Setter Property="TabNavigation" Value="Local"/>
        <Setter Property="IsHoldingEnabled" Value="True"/>
        <Setter Property="HorizontalContentAlignment" Value="Center"/>
        <Setter Property="VerticalContentAlignment" Value="Center"/>
        <Setter Property="Margin" Value="0,0,4,4"/>
        <Setter Property="MinWidth" Value="{ThemeResource GridViewItemMinWidth}"/>
        <Setter Property="MinHeight" Value="{ThemeResource GridViewItemMinHeight}"/>
        <Setter Property="AllowDrop" Value="False"/>
        <Setter Property="UseSystemFocusVisuals" Value="True"/>
        <Setter Property="FocusVisualMargin" Value="-2"/>
        <Setter Property="FocusVisualPrimaryBrush" Value="{ThemeResource GridViewItemFocusVisualPrimaryBrush}"/>
        <Setter Property="FocusVisualPrimaryThickness" Value="2"/>
        <Setter Property="FocusVisualSecondaryBrush" Value="{ThemeResource GridViewItemFocusVisualSecondaryBrush}"/>
        <Setter Property="FocusVisualSecondaryThickness" Value="1"/>
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="GridViewItem">
                <!-- XAML removed for clarity
                    <ListViewItemPresenter ... />
                -->   
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>
    ```

    Стиль **GridViewItem** по умолчанию задает значения многим свойствам. Следует всегда начинать с копии стиля по умолчанию и изменять только необходимые свойства. В противном случае визуальные элементы могут отображаться не так, как ожидается, так как некоторые свойства будут настроены неверно.

    Как и на предыдущем шаге, свойству **ItemContainerStyle** элемента GridView задано значение нового ресурса **Style**.

    ```xaml
        <GridView x:Name="ImageGridView"
                  Margin="0,0,0,8"
                  RelativePanel.AlignLeftWithPanel="True"
                  RelativePanel.AlignRightWithPanel="True"
                  RelativePanel.Below="TitleTextBlock"
                  ItemTemplate="{StaticResource ImageGridView_DefaultItemTemplate}"
                  ItemContainerStyle="{StaticResource ImageGridView_DefaultItemContainerStyle}"/>
    ```

3. Измените значение свойства **Background** на **Gray**.

    **До**
    ```xaml
        <Setter Property="Background" Value="{ThemeResource GridViewItemBackground}"/>
    ```

    **После**
    ```xaml
        <Setter Property="Background" Value="Gray"/>
    ```

4. Измените значение свойства **Margin** на **8**.

    **До**
    ```xaml
        <Setter Property="Margin" Value="0,0,4,4"/>
    ```

    **После**
    ```xaml
        <Setter Property="Margin" Value="8"/>
    ```

Запустите приложение и посмотрите, как оно выглядит на данном этапе. Измените размер окна приложения. Элемент **GridView** отвечает за изменение расположения изображений, но при некоторых значениях ширины в правой части окна приложения остается много пространства. Приложение выглядело бы лучше, если бы изображения располагались по центру. Об этом мы позаботимся в следующей части.

![Пользовательский интерфейс приложения: контрольная точка 3](images/xaml-basics/layout-2.png)

> [!Note]
> Если вы хотите поэкспериментировать, попробуйте изменять значения свойств Background и Margin и посмотрите на результат.

## <a name="part-5-apply-some-final-adjustments-to-the-layout"></a>Часть 5. Последние штрихи в макете

Чтобы расположить изображения по центру страницы, необходимо настроить выравнивание сетки на странице. А возможно, следует настроить выравнивание изображений в **GridView**? Есть ли разница? Давайте посмотрим.

Дополнительные сведения о выравнивании см. в статье [Выравнивание, поля и заполнение](../layout/alignment-margin-padding.md).

(На этом шаге попробуйте установить в качестве значения свойства **Background** элемента **GridView** любой цвет на ваш выбор. Это поможет вам следить за изменениями в макете.)

**Изменение выравнивания изображений**

1. В **Gridview** установите свойству **HorizontalAlignment** значение **Center**.

    **До**
    ```xaml
        <GridView x:Name="ImageGridView"
                  Margin="0,0,0,8"
                  RelativePanel.AlignLeftWithPanel="True"
                  RelativePanel.AlignRightWithPanel="True"
                  RelativePanel.Below="TitleTextBlock"
                  ItemTemplate="{StaticResource ImageGridView_DefaultItemTemplate}"
                  ItemContainerStyle="{StaticResource ImageGridView_DefaultItemContainerStyle}"/>
    ```

    **После**
    ```xaml
        <GridView x:Name="ImageGridView"
                  Margin="0,0,0,8"
                  RelativePanel.AlignLeftWithPanel="True"
                  RelativePanel.AlignRightWithPanel="True"
                  RelativePanel.Below="TitleTextBlock"
                  ItemTemplate="{StaticResource ImageGridView_DefaultItemTemplate}"
                  ItemContainerStyle="{StaticResource ImageGridView_DefaultItemContainerStyle}" 
                  HorizontalAlignment="Center"/>
    ```

2. Запустите приложение и измените размер окна. Прокрутите вниз для просмотра других изображений.

    Изображения располагаются по центру, и приложение выглядит лучше. Тем не менее, полоса прокрутки выровнена по краю элемента **GridView**, а не по краю окна. Чтобы устранить эту проблему, необходимо отцентрировать изображения в элементе **GridView**, а не сам элемент **GridView** на странице. Это дополнительная задача, но в результате внешний вид приложения улучшится.

3. Удалите параметр **HorizontalAlignment** из предыдущего этапа.

4. На панели "Структура документа" щелкните правой кнопкой мыши **ImageGridView**. В контекстном меню выберите **Изменить дополнительные шаблоны > Изменить макет элементов (ItemsPanel) > Изменить копию...**. Откроется диалоговое окно **Создать ресурс**.

5. В этом диалоговом окне измените значение ключа Name на **ImageGridView_ItemsPanelTemplate**, а затем нажмите кнопку **ОК**.

    В раздел **Page.Resources** кода XAML добавляется копия **ItemsPanelTemplate** по умолчанию. (Кроме того, как и раньше, элемент **GridView** обновляется со ссылкой на этот ресурс.)

    ```xaml
    <ItemsPanelTemplate x:Key="ImageGridView_ItemsPanelTemplate">
        <ItemsWrapGrid Orientation="Horizontal" />
    </ItemsPanelTemplate>
    ```

    Мы уже использовали различные панели для расположения элементов управления в приложении, а в элементе **GridView** есть внутренняя панель для управления расположением его элементов. Получив доступ к этой панели (the **ItemsWrapGrid**), можно изменить ее свойства, чтобы поменять расположение элементов внутри **GridView**.

6. В **ItemsWrapGrid** установите свойству **HorizontalAlignment** значение **Center**.

    **До**
    ```xaml
    <ItemsPanelTemplate x:Key="ImageGridView_ItemsPanelTemplate">
        <ItemsWrapGrid Orientation="Horizontal" />
    </ItemsPanelTemplate>
    ```

    **После**
    ```xaml
    <ItemsPanelTemplate x:Key="ImageGridView_ItemsPanelTemplate">
        <ItemsWrapGrid Orientation="Horizontal"
                       HorizontalAlignment="Center"/>
    </ItemsPanelTemplate>
    ```

7. Запустите приложение и измените размер окна еще раз. Прокрутите вниз для просмотра других изображений.

![Пользовательский интерфейс приложения: контрольная точка 4](images/xaml-basics/layout-3.png)

Теперь полоса прокрутки выровнена по краю окна. Отличная работа! Вы создали базовый пользовательский интерфейс для приложения.

## <a name="going-further"></a>Дальнейшая работа

Теперь, когда вы создали базовый пользовательский интерфейс, ознакомьтесь с другими руководствами, которые также основаны на примере PhotoLab: 

* Добавление реальных изображений и данных в [учебнике по привязке данных XAML](../../data-binding/xaml-basics-data-binding.md).
* Создание пользовательского интерфейса, который может адаптироваться к различным размерам экрана, в [учебнике по адаптивному макету XAML](xaml-basics-adaptive-layout.md).


## <a name="get-the-final-version-of-the-photolab-sample"></a>Получите окончательную версию примера PhotoLab

В этом руководстве не будет создано полноценное приложение для редактирования фотографий, поэтому ознакомьтесь с [окончательной версией](https://github.com/Microsoft/Windows-appsample-photo-lab), в которой присутствуют другие возможности, такие как пользовательские анимации и поддержка телефонов.

