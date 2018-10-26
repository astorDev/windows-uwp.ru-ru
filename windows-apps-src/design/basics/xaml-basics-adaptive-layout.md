---
author: muhsinking
title: Учебник по созданию адаптивных макетов
description: В этой статье рассматриваются основы создания адаптивных макетов на XAML
keywords: XAML, UWP, начало работы
ms.author: mukin
ms.date: 08/30/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 000aa2d8f3684aa813b85076d9124a87a71b6a8c
ms.sourcegitcommit: d0e836dfc937ebf7dfa9c424620f93f3c8e0a7e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5653882"
---
# <a name="tutorial-create-adaptive-layouts"></a>Учебник: создание адаптивных макетов

В этом учебнике рассматриваются основы использования возможностей языка XAML по созданию адаптивных и специально разработанных макетов. Эти возможности помогают создавать приложения, которые выглядят отлично на любом устройстве. Вы узнаете, как создать новый шаблон DataTemplate, добавить точки прикрепления окна и адаптировать макет приложения с помощью элементов VisualStateManager и AdaptiveTrigger. С помощью этих средств мы оптимизируем программу редактирования изображений для устройств с небольшими экранами. 

Программа редактирования изображений, над которой вы будете работать, содержит две страницы/экрана.

**Главная страница**— отображает представление фотоальбома, а также некоторые сведения о каждой файле изображения.

![MainPage](../basics/images/xaml-basics/mainpage.png)

**Страница сведений**— отображает одну выбранную фотографию. Всплывающее меню редактирования позволяет изменить, переименовать и сохранить фотографию.

![DetailPage](../basics/images/xaml-basics/detailpage.png)

## <a name="prerequisites"></a>Что вам понадобится

* Visual Studio 2017: [скачать Visual Studio Community 2017 (бесплатно)](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15&campaign=WinDevCenter&ocid=wdgcx-windevcenter-community-download) 
* Пакет SDK для Windows 10 (10.0.15063.468 или более поздней версии): [скачать последнюю версию Windows SDK (бесплатно)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Мобильный эмулятор Windows: [скачать мобильный эмулятор Windows 10 (бесплатно)](https://developer.microsoft.com/en-us/windows/downloads/sdk-archive)

## <a name="part-0-get-the-starter-code-from-github"></a>Часть 0. Получите начальный код на GitHub

В этом руководстве мы начнем с упрощенной версии примера PhotoLab. 

1. Последовательно выберите пункты [https://github.com/Microsoft/Windows-appsample-photo-lab](https://github.com/Microsoft/Windows-appsample-photo-lab). Вы перейдете на страницу GitHub с примером кода. 
2. Далее необходимо клонировать или скачать пример. Нажмите кнопку **Клонировать или скачать**. Откроется подменю.
    <figure>
        <img src="../basics/images/xaml-basics/clone-repo.png" alt="The Clone or download menu on GitHub">
        <figcaption>Меню <b>Клонировать или скачать</b> на странице GitHub с примером приложения для обработки фотографий.</figcaption>
    </figure>

    **Если вы не знакомы с GitHub:**
    
    А. Нажмите кнопку **Скачать ZIP-файл** и сохраните его локально. Будет выполнено скачивание ZIP-файла с необходимыми файлами проекта.
    Б. Извлеките файл. Используйте проводник, чтобы перейти к скачанному ZIP-файлу, щелкните его правой кнопкой мыши и выберите **Извлечь все...**. В. Перейдите к локальной копии примера кода, а затем в каталог `Windows-appsample-photo-lab-master\xaml-basics-starting-points\adaptive-layout`.    

    **Если вы знакомы с GitHub:**

    А. Клонируйте основную ветвь репозитория локально.
    Б. Перейдите в каталог `Windows-appsample-photo-lab\xaml-basics-starting-points\adaptive-layout`.

3. Откройте проект, щелкнув `Photolab.sln`.

## <a name="part-1-run-the-mobile-emulator"></a>Часть 1. Запуск мобильного эмулятора

Убедитесь, что на панели инструментов Visual Studio в качестве платформы решения выбрана архитектура x86 или x64, а не ARM, а затем измените целевое с устройство с локального компьютера на один из установленных мобильных эмуляторов (например, Mobile Emulator 10.0.15063 WVGA 5 inch 1GB). Попробуйте запустить приложение "Фотоальбом" в выбранном мобильном эмуляторе с помощью клавиши **F5**.

Сразу после запуска приложения вы, возможно, заметите, что хотя приложение и работает, в таком маленьком окне просмотра выглядит оно не лучшим образом. Гибкий элемент Grid пытается компенсировать ограниченное пространство экрана путем уменьшения количества отображаемых столбцов, но в итоге макет выглядит скучно и плохо вписывается в столь небольшое окно просмотра.

![Мобильный макет: после](../basics/images/xaml-basics/adaptive-layout-mobile-before.png)

## <a name="part-2-build-a-tailored-mobile-layout"></a>Часть 2. Создание специально разработанного мобильного макета
Чтобы это приложение отлично смотрелось на небольших устройствах, мы создадим отдельный набор стилей на странице XAML, который будет использоваться только при обнаружении мобильного устройства.

### <a name="create-a-new-datatemplate"></a>Создание нового шаблона DataTemplate
Мы адаптируем представление галереи приложения путем создания нового шаблона DataTemplate для изображений. Откройте файл MainPage.xaml из обозревателя решений и добавьте следующий код внутри тегов **Page.Resources**.

```XAML
<DataTemplate x:Key="ImageGridView_MobileItemTemplate"
              x:DataType="local:ImageFileInfo">

    <!-- Create image grid -->
    <Grid Height="{Binding ItemSize, ElementName=page}"
          Width="{Binding ItemSize, ElementName=page}">
        
        <!-- Place image in grid, stretching it to fill the pane-->
        <Image x:Name="ItemImage"
               Source="{x:Bind ImagePreview}"
               Stretch="UniformToFill">
        </Image>

    </Grid>
</DataTemplate>
```

Шаблон галереи экономит пространство экрана за счет устранения границы вокруг изображений и удаления метаданных изображений (имени файла, оценок и т. д.) под каждым эскизом. Вместо этого каждый эскиз отображается как простой квадрат.

### <a name="add-metadata-to-a-tooltip"></a>Добавление метаданных в подсказку
Нам по-прежнему необходимо обеспечить пользователю доступ к метаданным для каждого изображения, поэтому мы добавим подсказку к каждому изображению. Добавьте следующий код внутри тегов **Image** только что созданного шаблона DataTemplate.

```XAML
<Image ...>

    <!-- Add a tooltip to the image that displays metadata -->
    <ToolTipService.ToolTip>
        <ToolTip x:Name="tooltip">

            <!-- Arrange tooltip elements vertically -->
            <StackPanel Orientation="Vertical"
                        Grid.Row="1">

                <!-- Image title -->
                <TextBlock Text="{x:Bind ImageTitle, Mode=OneWay}"
                           HorizontalAlignment="Center"
                           Style="{StaticResource SubtitleTextBlockStyle}" />

                <!-- Arrange elements horizontally -->
                <StackPanel Orientation="Horizontal"
                            HorizontalAlignment="Center">

                    <!-- Image file type -->
                    <TextBlock Text="{x:Bind ImageFileType}"
                               HorizontalAlignment="Center"
                               Style="{StaticResource CaptionTextBlockStyle}" />

                    <!-- Image dimensions -->
                    <TextBlock Text="{x:Bind ImageDimensions}"
                               HorizontalAlignment="Center"
                               Style="{StaticResource CaptionTextBlockStyle}"
                               Margin="8,0,0,0" />
                </StackPanel>
            </StackPanel>
        </ToolTip>
    </ToolTipService.ToolTip>
</Image>
```

В результате этого действия при наведении указателя мыши на эскиз (или при нажатии и удержании эскиза на сенсорном экране) отобразятся название, тип файла и размеры изображения.

### <a name="add-a-visualstatemanager-and-statetrigger"></a>Добавление элементов VisualStateManager и StateTrigger

Мы создали новый макет для данных, но приложение еще не может понять, когда использовать этот макет вместо стилей по умолчанию. Чтобы исправить эту ситуацию, необходимо добавить элемент **VisualStateManager**. Добавьте следующий код в корневой элемент страницы— **RelativePanel**.

```XAML
<VisualStateManager.VisualStateGroups>
    <VisualStateGroup>

        <!-- Add a new VisualState for mobile devices -->
        <VisualState x:Key="Mobile">

            <!-- Trigger visualstate when a mobile device is detected -->
            <VisualState.StateTriggers>
                <local:MobileScreenTrigger InteractionMode="Touch" />
            </VisualState.StateTriggers>

        </VisualState>
    </VisualStateGroup>
</VisualStateManager.VisualStateGroups>
```

Это действие добавит новые элементы **VisualState** и **StateTrigger**, которые будут активироваться, если приложение обнаружит, что оно запущено на мобильном приложении (логика для этой операции содержится в файле MobileScreenTrigger.cs, расположенном в каталоге PhotoLab). При запуске **StateTrigger** приложение будет использовать атрибуты макета, назначенные этому элементу **VisualState**.

### <a name="add-visualstate-setters"></a>Добавление методов установки значений VisualState
Далее мы будем использовать методы установки значений **VisualState**, чтобы указать элементу **VisualStateManager**, какие атрибуты следует применять при активации состояния. Каждый метод установки значения предназначен для одного свойства определенного элемента XAML и устанавливает для него заданное значение. Добавьте этот код в только что созданный мобильный элемент **VisualState** под элементом **VisualState.StateTriggers**. 

```XAML
<VisualStateManager.VisualStateGroups>
    <VisualStateGroup>

        <VisualState x:Key="Mobile">
            ...

            <!-- Add setters for mobile visualstate -->
            <VisualState.Setters>

                <!-- Move GridView about the command bar -->
                <Setter Target="ImageGridView.(RelativePanel.Above)"
                        Value="MainCommandBar" />

                <!-- Switch to mobile layout -->
                <Setter Target="ImageGridView.ItemTemplate"
                        Value="{StaticResource ImageGridView_MobileItemTemplate}" />

                <!-- Switch to mobile container styles -->
                <Setter Target="ImageGridView.ItemContainerStyle"
                        Value="{StaticResource ImageGridView_MobileItemContainerStyle}" />

                <!-- Move command bar to bottom of the screen -->
                <Setter Target="MainCommandBar.(RelativePanel.AlignBottomWithPanel)"
                        Value="True" />
                <Setter Target="MainCommandBar.(RelativePanel.AlignLeftWithPanel)"
                        Value="True" />
                <Setter Target="MainCommandBar.(RelativePanel.AlignRightWithPanel)"
                        Value="True" />

                <!-- Adjust the zoom slider to fit mobile screens -->
                <Setter Target="ZoomSlider.Minimum"
                        Value="80" />
                <Setter Target="ZoomSlider.Maximum"
                        Value="180" />
                <Setter Target="ZoomSlider.TickFrequency"
                        Value="20" />
                <Setter Target="ZoomSlider.Value"
                        Value="100" />
            </VisualState.Setters>

        </VisualState>
    </VisualStateGroup>
</VisualStateManager.VisualStateGroups>

```

Эти методы установки значений устанавливают для шаблона **ItemTemplate** галереи изображений значение нового шаблона **DataTemplate**, созданного в первой части, а также выравнивают панель команд и ползунок масштабирования относительно нижней части экрана, чтобы до них было удобнее дотянуться большим пальцем на экране мобильного телефона.

### <a name="run-the-app"></a>Запуск приложения
Теперь попробуйте запустить приложение с помощью мобильного эмулятора. Правильно ли отображается ли новый макет? Должна отобразиться сетка небольших эскизов, как показано ниже. Если по-прежнему отображается старый макет, проверьте код **VisualStateManager** на наличие опечаток.

![Мобильный макет: после](../basics/images/xaml-basics/adaptive-layout-mobile-after.png)

## <a name="part-3-adapt-to-multiple-window-sizes-on-a-single-device"></a>Часть 3. Адаптация под разные размеры экрана на одном устройстве
Создание нового специально разработанного макета решает проблему отзывчивого дизайна для мобильных устройств, но как обстоят дела с планшетами и настольными компьютерами? Приложение может хорошо выглядеть в полноэкранном режиме, но если пользователь уменьшит окно, интерфейс может отображаться неправильно. Мы можем обеспечить конечному пользователю удобный и красивый интерфейс путем использования элемента **VisualStateManager** для адаптации под разные размеры экрана на одном устройстве.

![Маленькое окно: до](../basics/images/xaml-basics/adaptive-layout-small-before.png)

### <a name="add-window-snap-points"></a>Добавление точек прикрепления окна
В первую очередь, необходимо определить "точки прикрепления", в которых будут активироваться разные элементы **VisualState**. Откройте файл App.xaml из обозревателя решений и добавьте следующий код внутри тегов **Application**.

```XAML
<Application.Resources>
    <!--  window width adaptive snap points  -->
    <x:Double x:Key="MinWindowSnapPoint">0</x:Double>
    <x:Double x:Key="MediumWindowSnapPoint">641</x:Double>
    <x:Double x:Key="LargeWindowSnapPoint">1008</x:Double>
</Application.Resources>
```

В результате, будут созданы три точки прикрепления, с помощью которых можно создать новые элементы **VisualState** для трех диапазонов размера окна:
+ Маленький (0–640 пикселей в ширину)
+ Средний (641–1007 пикселей в ширину)
+ Большой (более 1007 пикселей в ширину)

### <a name="create-new-visualstates-and-statetriggers"></a>Создание новых элементов VisualState и StateTrigger
Далее мы создадим элементы **VisualState** и **StateTrigger**, соответствующие каждой точке прикрепления. В файле MainPage.xaml добавьте следующий код в элемент **VisualStateManager**, созданный в части 2.

```XAML
<VisualStateManager.VisualStateGroups>
    <VisualStateGroup>
    ...

        <!-- Large window VisualState -->
        <VisualState x:Key="LargeWindow">

            <!-- Large window trigger -->
            <VisualState.StateTriggers>
                <AdaptiveTrigger MinWindowWidth="{StaticResource LargeWindowSnapPoint}"/>
            </VisualState.StateTriggers>
     
        </VisualState>

        <!-- Medium window VisualState -->
        <VisualState x:Key="MediumWindow">

            <!-- Medium window trigger -->
            <VisualState.StateTriggers>
                <AdaptiveTrigger MinWindowWidth="{StaticResource MediumWindowSnapPoint}"/>
            </VisualState.StateTriggers>
        
        </VisualState>

        <!-- Small window VisualState -->
        <VisualState x:Key="SmallWindow">

            <!-- Small window trigger -->
            <VisualState.StateTriggers >
                <AdaptiveTrigger MinWindowWidth="{StaticResource MinWindowSnapPoint}"/>
            </VisualState.StateTriggers>

        </VisualState>

    </VisualStateGroup>
</VisualStateManager.VisualStateGroups>
```

### <a name="add-setters"></a>Добавление методов установки значений
Наконец, добавьте эти методы установки значений в состояние **SmallWindow**.

```XAML

<VisualState x:Key="SmallWindow">
    ...

    <!-- Small window setters -->
    <VisualState.Setters>

        <!-- Apply mobile itemtemplate and styles -->
        <Setter Target="ImageGridView.ItemTemplate"
                Value="{StaticResource ImageGridView_MobileItemTemplate}" />
        <Setter Target="ImageGridView.ItemContainerStyle"
                Value="{StaticResource ImageGridView_MobileItemContainerStyle}" />

        <!-- Adjust the zoom slider to fit small windows-->
        <Setter Target="ZoomSlider.Minimum"
                Value="80" />
        <Setter Target="ZoomSlider.Maximum"
                Value="180" />
        <Setter Target="ZoomSlider.TickFrequency"
                Value="20" />
        <Setter Target="ZoomSlider.Value"
                Value="100" />
    </VisualState.Setters>

</VisualState>

```

Эти методы установки значений применяют шаблон **DataTemplate** и стили к классическому приложению, если ширина окна просмотра составляет меньше 641 пикселя. Также они регулируют размер ползунка масштабирования под небольшой экран.

### <a name="run-the-app"></a>Запуск приложения

на панели инструментов Visual Studio в качестве целевого устройства выберите **Локальный компьютер** и запустите приложение. После загрузки приложения попробуйте изменить размер окна. Если уменьшить окно до маленького размера, приложение должно переключиться на мобильный макет, созданный в части 2.

![Маленькое окно: после](../basics/images/xaml-basics/adaptive-layout-small-after.png)

## <a name="going-further"></a>Дальнейшая работа

Завершив это практическое занятие, вы получили достаточный объем знаний об адаптивном макете, чтобы в дальнейшем экспериментировать самостоятельно. Попробуйте добавить элемент управления оценкой в добавленную ранее мобильную подсказку. Кроме того, попробуйте выполнить более сложную задачу— оптимизировать макет для большее крупных экранов (например, экранов телевизоров или Surface Studio)

Если у вас возникнут проблемы, дополнительные рекомендации см. в следующих разделах статьи [Определение макетов страниц с помощью XAML](../layout/layouts-with-xaml.md).

+ [Визуальные состояния и триггеры состояния](https://docs.microsoft.com/en-us/windows/uwp/layout/layouts-with-xaml#visual-states-and-state-triggers)
+ [Специально разработанные макеты](https://docs.microsoft.com/en-us/windows/uwp/layout/layouts-with-xaml#tailored-layouts)

Кроме того, если вы хотите узнать больше о процессе создания изначального приложения для редактирования фотографий, ознакомьтесь с этими учебниками по [пользовательским интерфейсам](../basics/xaml-basics-ui.md) и [привязке данных](../../data-binding/xaml-basics-data-binding.md) в XAML.

## <a name="get-the-final-version-of-the-photolab-sample"></a>Получите окончательную версию примера PhotoLab

В этом руководстве не будет создано полноценное приложение для редактирования фотографий, поэтому ознакомьтесь с [окончательной версией](https://github.com/Microsoft/Windows-appsample-photo-lab), в которой присутствуют другие возможности, такие как пользовательские анимации и поддержка телефонов.