---
author: mijacobs
title: Создание пользовательских стилей
description: В этой статье рассматриваются основы оформления элементов пользовательского интерфейса в XAML
keywords: XAML, UWP, начало работы
ms.author: mijacobs
ms.date: 08/31/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 11f279de206a84e61144789ba43a268f2b896fee
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5570192"
---
# <a name="tutorial-create-custom-styles"></a>Учебник: создание собственных пользовательских стилей

В этом учебнике показано, как настроить пользовательский интерфейс приложения XAML. Внимание! В этом учебнике может быть задействован единорог. А может и не быть. (Будет!)  

## <a name="prerequisites"></a>Что вам понадобится
* [Visual Studio 2017 и пакет SDK для Windows 10 (10.0.15063.468 или более поздней версии)](https://developer.microsoft.com/windows/downloads)

## <a name="part-0-get-the-code"></a>Часть 0. Получение кода
Это практическое занятие начинается в репозитории примера PhotoLab в [папке xaml-basics-starting-points/style/](https://github.com/Microsoft/Windows-appsample-photo-lab/tree/master/xaml-basics-starting-points/style). После клонирования или скачивания репозитория проект можно изменить. Для этого откройте файл PhotoLab.sln в Visual Studio 2017.

В приложении PhotoLab есть две основные страницы:

**MainPage.xaml**— отображает представление фотоальбома, а также некоторые сведения о каждой файле изображения.
![MainPage](../basics/images/xaml-basics/mainpage.png)

**DetailPage.xaml**— отображает одну выбранную фотографию. Всплывающее меню редактирования позволяет изменить, переименовать и сохранить фотографию.
![DetailPage](../basics/images/xaml-basics/detailpage.png)

## <a name="part-1-create-a-fancy-slider-control"></a>Часть 1. Создание необычного элемента управления ползунка  

Универсальная платформа Windows (UWP) предоставляет целый ряд способов настройки внешнего вида приложения. Доступно множество вариантов— от шрифтов и параметров оформления текста до цветов, градиентов и эффектов размытия. 

В первой части учебника мы оформим некоторые элементы управления для редактирования фотографий. 

<figure>
    <img src="../basics/images/xaml-basics/slider-start.png" />
    <figure>*Простой ползунок с оформлением по умолчанию.*</figure>
</figure>

Эти ползунки выполняют все положенные им функции, но выглядят они слишком просто. Исправим ситуацию. 

Ползунок экспозиции регулирует экспозицию изображения: передвиньте его влево, чтобы изображение стало темнее, и вправо, чтобы высветлить изображение. Сделаем ползунок интереснее, добавив к нему фон с постепенным переходом от черного к белому. Тогда ползунок будет лучше выглядеть, что уже хорошо, а также появится визуальная подсказка о возможностях этого ползунка.

### <a name="customize-a-slider-control"></a>Настройка элемента управления ползунка

<!-- TODO: Update folder -->
1. После скачивания репозитория откройте **PhotoLab.sln** в папке xaml-basics-starting-points/style/ и выберите архитектуру для своей платформы решения: x86 или x64 (не ARM). 

    Нажмите клавишу F5, чтобы скомпилировать и запустить приложение. На первом экране отображается галерея изображений. Щелкните любое изображение, чтобы перейти на его страницу сведений. Перейдя на страницу сведений, нажмите кнопку редактирования, чтобы просмотреть элементы управления редактированием, с которыми нам предстоит работать. Выйдите из приложения и вернитесь в Visual Studio.  

2. На панели обозревателя решений дважды щелкните файл **DetailPage.xaml**, чтобы открыть его. 

    ![Файл DetailPage.xaml в обозревателе решений Visual Studio 2017.](../basics/images/xaml-basics/style-detail-page-explorer.png)

3. Используйте элемент многоугольника, чтобы создать фоновую фигуру для ползунка экспозиции.

    В [пространстве имен Windows.XAML.Ui.Shapes](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Shapes) доступно на выбор семь фигур. Среди фигур есть эллипс, прямоугольник и элемент "Путь", с помощью которого можно создать любую фигуру— даже единорога! 
    
    <!-- TODO reduce size --> ![Единорог](../basics/images/xaml-basics/unicorn.png)
    
    > **Прочтите об этом.** В статье [Рисование фигур](https://docs.microsoft.com/en-us/windows/uwp/graphics/drawing-shapes) содержится вся необходимая информация о фигурах XAML. 
    
    Нам нужно создать мини-приложение в форме треугольника, чтобы оно напоминало элемент управления громкостью в стереопроигрывателе.
    
    ![Ползунок громкости](../basics/images/xaml-basics/style-volume-slider.png)
    
    В этом нам как раз поможет фигура многоугольника! Для определения многоугольника необходимо определить набор точек и залить его. Создадим многоугольник шириной примерно 200 пикселей и высотой 20 пикселей с градиентной заливкой.
    
    В файле DetailPage.xaml найдите код ползунка экспозиции и создайте элемент многоугольника прямо перед ним: 

    * Задайте свойству **Grid.Row** значение "2", чтобы разместить многоугольник в одной строке с ползунком экспозиции. 
    * Задайте свойству **Points** значение "0,20 200,20 200,0", чтобы определить фигуру треугольника.
    * Задайте свойству **Stretch** значение "Fill", а свойству **HorizontalAlignment**— "Stretch".
    * Задайте свойству **Height** значение "20", а свойству **VerticalAlignment**— "Center". 
    * Обеспечьте для **Polygon** линейную градиентную заливку.     
    * На ползунке экспозиции установите для свойства **Foreground** значение "Transparent", чтобы многоугольник было видно. 

    **До**
    ```xaml
    <Slider Header="Exposure"
        Grid.Row="2"
        Value="{x:Bind item.Exposure, Mode=TwoWay}"
        Minimum="-2"
        Maximum="2" />
    ```
    **После**
    ```xaml
    <Polygon Grid.Row="2" Stretch="Fill"
                Points="0,20 200,20 200,0" HorizontalAlignment="Stretch"  
                VerticalAlignment="Center" Height="20">
        <Polygon.Fill>
            <LinearGradientBrush StartPoint="0,0.5" EndPoint="1,0.5">
                <LinearGradientBrush.GradientStops>
                    <GradientStop Offset="0" Color="Black" />
                    <GradientStop Offset="1" Color="White" />
                </LinearGradientBrush.GradientStops>
            </LinearGradientBrush>
        </Polygon.Fill>
    </Polygon>
    <Slider Header="Exposure" 
        Grid.Row="2" 
        Foreground="Transparent"
        Value="{x:Bind item.Exposure, Mode=TwoWay}"
        Minimum="-2"
        Maximum="2" />
    ```

    Примечания.
    * Обратив внимание на окружающий код XAML, вы увидите, что эти элементы находятся в сетке. Мы разместили многоугольник в одной строке с ползунком экспозиции (Grid.Row="2"), чтобы они отображались в одном месте. Многоугольник расположен перед ползунком, чтобы ползунок отрисовывался поверх фигуры.
    * Мы настроили свойства Stretch="Fill" и HorizontalAlignment="Stretch" для многоугольника, чтобы треугольник менял размер для заполнения доступного пространства. Если ширина ползунка уменьшится или увеличится, размеры многоугольника изменятся соответствующим образом. 

4. Скомпилируйте и запустите приложение. Теперь ползунок должен выглядеть просто замечательно:

    ![Стильный ползунок экспозиции](../basics/images/xaml-basics/style-exposure-slider-done.png)

5. Теперь модернизируем следующий ползунок— ползунок температуры. Ползунок температуры изменяет цветовую температуру изображения: если передвинуть его влево, изображение приобретает более синий оттенок, а вправо— желтый.

    Для этой фоновой фигуры мы снова будем использовать многоугольник с теми же размерами, что и у предыдущего, но на сей раз заливкой послужит сине-желтый градиент, а не черно-белый. 

    **До**
    ```xaml
    <TextBlock Grid.Row="2"
                Grid.Column="1"
                 Margin="10,8,0,0" VerticalAlignment="Center" Padding="0"
                Text="{x:Bind item.Exposure.ToString('N', culture), Mode=OneWay}" />
                
    <Slider Header="Temperature"
            Grid.Row="3" Background="Transparent" Foreground="Transparent"
            Value="{x:Bind item.Temperature, Mode=TwoWay}"
            Minimum="-1"
            Maximum="1" />
    ```
    **После**
    ```xaml
    <TextBlock Grid.Row="2"
                Grid.Column="1"
                Margin="10,8,0,0" VerticalAlignment="Center" Padding="0"
                Text="{x:Bind item.Exposure.ToString('N', culture), Mode=OneWay}" />         
                
    <Polygon Grid.Row="3" Stretch="Fill"
                Points="0,20 200,20 200,0" HorizontalAlignment="Stretch"  
                VerticalAlignment="Center" Height="20">
        <Polygon.Fill>
            <LinearGradientBrush StartPoint="0,0.5" EndPoint="1,0.5">
                <LinearGradientBrush.GradientStops>
                    <GradientStop Offset="0" Color="Blue" />
                    <GradientStop Offset="1" Color="Yellow" />
                </LinearGradientBrush.GradientStops>
            </LinearGradientBrush>
        </Polygon.Fill>
    </Polygon>
    <Slider Header="Temperature"
            Grid.Row="3" Background="Transparent" Foreground="Transparent"
            Value="{x:Bind item.Temperature, Mode=TwoWay}"
            Minimum="-1"
            Maximum="1" />
    ```

6. Скомпилируйте и запустите приложение. Теперь у нас два стильный ползунка.

    ![Два стильных ползунка](../basics/images/xaml-basics/style-2sliders-done.png)

7. **Дополнительная задача**

    Добавьте фоновую фигуру для ползунка оттенка с градиентом от зеленого к красному. 

    ![Три стильных ползунка](../basics/images/xaml-basics/style-3sliders-done.png)


Поздравляем, вы завершили часть 1. Если вы столкнулись с проблемами или хотите увидеть окончательное решение, готовый код можно найти в папке **UWP Academy\XAML\Styling\Part1\Finish**.

 
    
## <a name="part-2-create-basic-styles"></a>Часть 2. Создание базовых стилей

Одно из преимуществ стилей XAML заключается в том, что их использование может кардинально сократить объем кода, который требуется написать, а также значительно упростить процесс обновления внешнего вида приложения.

Чтобы определить стиль, необходимо добавить элемент [Style](https://msdn.microsoft.com/library/windows/apps/br208849) в свойство [Resources](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.frameworkelement.Resources) элемента, содержащего элемент управления, который требуется оформить.  Если добавить стиль в свойство **Page.Resources**, стили будут доступны для всей страницы. Если добавить стиль в свойство **Application.Resources** в файле App.xaml, стиль будет доступен всему приложению.

Можно создавать именованные и общие стили. Именованный стиль должен быть явным образом применен к конкретным элементам управления, а общий стиль применяется к любому элементу управления, который соответствует указанному атрибуту **TargetType**. 

В этом примере первый стиль содержит **x:Key** и имеет тип целевого объекта **Button**. Данный ключ задается в свойстве **Style**, поэтому стиль является именованным и должен применяться явным образом. Второй стиль применяется ко второй кнопке автоматически, поскольку он имеет тип целевого объекта **Button**, а в стиле отсутствует атрибут **x:Key**.


```XAML
<Page.Resources>
    <Style x:Key="PurpleStyle" TargetType="Button">
        <Setter Property="FontFamily" Value="Lucida Sans Unicode"/>
        <Setter Property="FontStyle" Value="Italic"/>
        <Setter Property="FontSize" Value="14"/>
        <Setter Property="Foreground" Value="MediumOrchid"/>
    </Style>

    <Style TargetType="Button">
        <Setter Property="Foreground" Value="Orange"/>
    </Style>
</Page.Resources>

<Grid x:Name="LayoutRoot">
    <Button Content="Button" Style="{StaticResource PurpleStyle}"/>
    <Button Content="Button" />
</Grid>
```

Добавим стиль в наше приложение. В файле DetailsPage.xaml обратите внимание на тестовые блоки, расположенные рядом с ползунками экспозиции, температуры и оттенка. Каждый из этих текстовых блоков отображает значение ползунка. Вот текстовый блок ползунка экспозиции. Обратите внимание, что свойствам **Margin**, **VerticalAlignment** и **Padding** уже заданы значения.

```XAML
<TextBlock Grid.Row="2"
            Grid.Column="1"
            Margin="10,8,0,0" VerticalAlignment="Center" Padding="0"
            Text="{x:Bind item.Exposure.ToString('N', culture), Mode=OneWay}" />
```
Обратите внимание на другие текстовые блоки— эти свойства имеют в них те же значения. Похоже, это отличный вариант для оформления...

### <a name="create-a-value-text-block-style"></a>Создание стиля текстового блока со значением

<!-- TODO: add second starting point -->
1. Откройте файл DetailsPage.xaml.

2. Найдите элемент управления **Grid** с именем **EditControlsGrid**. Он содержит наши ползунки и текстовые поля. Обратите внимание, что сетка уже определяет стиль для ползунков. 

    ```XAML
    <Grid x:Name="EditControlsGrid"
            HorizontalAlignment="Stretch"
            Margin="24,48,24,24">
        <Grid.Resources>
            <Style TargetType="Slider">
                <Setter Property="Margin"
                        Value="0,0,0,0" />
                <Setter Property="Padding"
                        Value="0" />
                <Setter Property="MinWidth"
                        Value="100" />
                <Setter Property="StepFrequency"
                        Value="0.1" />
                <Setter Property="TickFrequency"
                        Value="0.1" />
            </Style>
        </Grid.Resources>    
    ```
3. Создайте стиль для **TextBlock**, который задает свойству **Margin** этого элемента значение "10,8,0,0", свойству **VerticalAlignment**— "Center", а свойству **Padding**— "0".

    **До**
    ```XAML
        <Grid.Resources>
            <Style TargetType="Slider">
                <Setter Property="Margin"
                        Value="0,0,0,0" />
                <Setter Property="Padding"
                        Value="0" />
                <Setter Property="MinWidth"
                        Value="100" />
                <Setter Property="StepFrequency"
                        Value="0.1" />
                <Setter Property="TickFrequency"
                        Value="0.1" />
            </Style>                           
        </Grid.Resources>
    ```

    **После**
    ```XAML
        <Grid.Resources>
            <Style TargetType="Slider">
                <Setter Property="Margin"
                        Value="0,0,0,0" />
                <Setter Property="Padding"
                        Value="0" />
                <Setter Property="MinWidth"
                        Value="100" />
                <Setter Property="StepFrequency"
                        Value="0.1" />
                <Setter Property="TickFrequency"
                        Value="0.1" />
            </Style>
            <Style TargetType="TextBlock">
                <Setter Property="Margin"
                        Value="10,8,0,0" />
                <Setter Property="VerticalAlignment"
                        Value="Center" />
                <Setter Property="Padding"
                        Value="0" />
            </Style>                            
        </Grid.Resources>
    ```    

4. Создадим именованный стиль, чтобы указать, к каким элементам управления **TextBlock** он применяется. Установите для свойства **x:Key** этого стиля значение "ValueTextBox". 

    **До**
    ```XAML
            <Style TargetType="TextBlock">
                <Setter Property="Margin"
                        Value="10,8,0,0" />
                <Setter Property="VerticalAlignment"
                        Value="Center" />
                <Setter Property="Padding"
                        Value="0" />
            </Style>                            
    ```    

    **После**
    ```XAML
            <Style TargetType="TextBlock"
                   x:Key="ValueTextBox">
                <Setter Property="Margin"
                        Value="10,8,0,0" />
                <Setter Property="VerticalAlignment"
                        Value="Center" />
                <Setter Property="Padding"
                        Value="0" />
            </Style>                            
    ```    

5. Удалите свойства **Margin**, **VerticalAlignment** и **Padding** каждого элемента **TextBlock**, а его свойству **Style** задайте значение "{StaticResource ValueTextBox}".

    **До**
    ```XAML
     <TextBlock Grid.Row="2"
                Grid.Column="1"
                Margin="10,8,0,0" VerticalAlignment="Center" Padding="0"
                Text="{x:Bind item.Exposure.ToString('N', culture), Mode=OneWay}" />   
    ```

    **После**
    ```XAML
     <TextBlock Grid.Row="2"
                Grid.Column="1"
                Style="{StaticResource ValueTextBox}"
                Text="{x:Bind item.Exposure.ToString('N', culture), Mode=OneWay}" />   
    ```    

    Сделайте это для всех шести элементов управления TextBlock, связанных с ползунками.

6. Скомпилируйте и запустите приложение. Теперь оно выглядит... так же. Но вы должны чувствовать невероятное удовлетворение от создания рационального и удобного в обслуживании кода.

<!-- TODO add new start/end points -->
Поздравляем, вы завершили часть 2.


## <a name="part-3-use-a-control-template-to-make-a-fancy-slider"></a>Часть 3. Использование шаблона элемента управления для создания стильного ползунка

Помните, как в части 1 мы добавили фигуру под ползунок, чтобы он выглядел лучше?

Мы справились с задачей, но есть более эффективный способ получить тот же результат— можно создать шаблон элемента управления. 

<!-- TODO add new starting points -->
1. На панели обозревателя решений дважды щелкните файл **DetailPage.xaml**.

2. Далее мы воспользуемся шаблоном элемента управления для ползунка в качестве отправной точки. Добавьте этот код XAML в элемент **Page.Resources**. (Элемент **Page.Resources** находится ближе к началу страницы.)

    ```XAML
    <ControlTemplate x:Key="FancySliderControlTemplate" TargetType="Slider">
        <Grid Margin="{TemplateBinding Padding}">
            <Grid.Resources>
                <Style TargetType="Thumb" x:Key="SliderThumbStyle">
                    <Setter Property="BorderThickness" Value="0" />
                    <Setter Property="Background" Value="{ThemeResource SliderThumbBackground}" />
                    <Setter Property="Template">
                        <Setter.Value>
                            <ControlTemplate TargetType="Thumb">
                                <Border Background="{TemplateBinding Background}"
                                            BorderBrush="{TemplateBinding BorderBrush}"
                                            BorderThickness="{TemplateBinding BorderThickness}"
                                            CornerRadius="4" />
                            </ControlTemplate>
                        </Setter.Value>
                    </Setter>
                </Style>
            </Grid.Resources>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Pressed">
                        <Storyboard>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HorizontalTrackRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackFillPressed}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="VerticalTrackRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackFillPressed}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HorizontalThumb" Storyboard.TargetProperty="Background">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderThumbBackgroundPressed}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="VerticalThumb" Storyboard.TargetProperty="Background">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderThumbBackgroundPressed}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="SliderContainer" Storyboard.TargetProperty="Background">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderContainerBackgroundPressed}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HorizontalDecreaseRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackValueFillPressed}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="VerticalDecreaseRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackValueFillPressed}" />
                            </ObjectAnimationUsingKeyFrames>
                        </Storyboard>
                    </VisualState>
                    <VisualState x:Name="Disabled">
                        <Storyboard>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HeaderContentPresenter" Storyboard.TargetProperty="Foreground">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderHeaderForegroundDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HorizontalDecreaseRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackValueFillDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HorizontalTrackRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackFillDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="VerticalDecreaseRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackValueFillDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="VerticalTrackRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackFillDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HorizontalThumb" Storyboard.TargetProperty="Background">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderThumbBackgroundDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="VerticalThumb" Storyboard.TargetProperty="Background">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderThumbBackgroundDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="TopTickBar" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTickBarFillDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="BottomTickBar" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTickBarFillDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="LeftTickBar" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTickBarFillDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RightTickBar" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTickBarFillDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="SliderContainer" Storyboard.TargetProperty="Background">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderContainerBackgroundDisabled}" />
                            </ObjectAnimationUsingKeyFrames>
                        </Storyboard>
                    </VisualState>
                    <VisualState x:Name="PointerOver">
                        <Storyboard>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HorizontalTrackRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackFillPointerOver}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="VerticalTrackRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackFillPointerOver}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HorizontalThumb" Storyboard.TargetProperty="Background">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderThumbBackgroundPointerOver}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="VerticalThumb" Storyboard.TargetProperty="Background">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderThumbBackgroundPointerOver}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="SliderContainer" Storyboard.TargetProperty="Background">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderContainerBackgroundPointerOver}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HorizontalDecreaseRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackValueFillPointerOver}" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="VerticalDecreaseRect" Storyboard.TargetProperty="Fill">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource SliderTrackValueFillPointerOver}" />
                            </ObjectAnimationUsingKeyFrames>
                        </Storyboard>
                    </VisualState>
                </VisualStateGroup>
                <VisualStateGroup x:Name="FocusEngagementStates">
                    <VisualState x:Name="FocusDisengaged" />
                    <VisualState x:Name="FocusEngagedHorizontal">
                        <Storyboard>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="SliderContainer" Storyboard.TargetProperty="(Control.IsTemplateFocusTarget)">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="False" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="HorizontalThumb" Storyboard.TargetProperty="(Control.IsTemplateFocusTarget)">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="True" />
                            </ObjectAnimationUsingKeyFrames>
                        </Storyboard>
                    </VisualState>
                    <VisualState x:Name="FocusEngagedVertical">
                        <Storyboard>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="SliderContainer" Storyboard.TargetProperty="(Control.IsTemplateFocusTarget)">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="False" />
                            </ObjectAnimationUsingKeyFrames>
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="VerticalThumb" Storyboard.TargetProperty="(Control.IsTemplateFocusTarget)">
                                <DiscreteObjectKeyFrame KeyTime="0" Value="True" />
                            </ObjectAnimationUsingKeyFrames>
                        </Storyboard>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
            <ContentPresenter x:Name="HeaderContentPresenter"
                        x:DeferLoadStrategy="Lazy"
                        Visibility="Collapsed"
                        Foreground="{ThemeResource SliderHeaderForeground}"
                        Margin="{ThemeResource SliderHeaderThemeMargin}"
                        Content="{TemplateBinding Header}"
                        ContentTemplate="{TemplateBinding HeaderTemplate}"
                        FontWeight="{ThemeResource SliderHeaderThemeFontWeight}"
                        TextWrapping="Wrap" />
            <Grid x:Name="SliderContainer"
                        Background="{ThemeResource SliderContainerBackground}"
                        Grid.Row="1"
                        Control.IsTemplateFocusTarget="True">
                <Grid x:Name="HorizontalTemplate" MinHeight="44">
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="18" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="18" />
                    </Grid.RowDefinitions>
                    <Rectangle x:Name="HorizontalTrackRect"
                                Fill="{TemplateBinding Background}"
                                Height="{ThemeResource SliderTrackThemeHeight}"
                                Grid.Row="1"
                                Grid.ColumnSpan="3" />
                    <Rectangle x:Name="HorizontalDecreaseRect" Fill="{TemplateBinding Foreground}" Grid.Row="1" />
                    <TickBar x:Name="TopTickBar"
                                Visibility="Collapsed"
                                Fill="{ThemeResource SliderTickBarFill}"
                                Height="{ThemeResource SliderOutsideTickBarThemeHeight}"
                                VerticalAlignment="Bottom"
                                Margin="0,0,0,4"
                                Grid.ColumnSpan="3" />
                    <TickBar x:Name="HorizontalInlineTickBar"
                                Visibility="Collapsed"
                                Fill="{ThemeResource SliderInlineTickBarFill}"
                                Height="{ThemeResource SliderTrackThemeHeight}"
                                Grid.Row="1"
                                Grid.ColumnSpan="3" />
                    <TickBar x:Name="BottomTickBar"
                                Visibility="Collapsed"
                                Fill="{ThemeResource SliderTickBarFill}"
                                Height="{ThemeResource SliderOutsideTickBarThemeHeight}"
                                VerticalAlignment="Top"
                                Margin="0,4,0,0"
                                Grid.Row="2"
                                Grid.ColumnSpan="3" />
                    <Thumb x:Name="HorizontalThumb"
                                Style="{StaticResource SliderThumbStyle}"
                                DataContext="{TemplateBinding Value}"
                                Height="24"
                                Width="8"
                                Grid.Row="0"
                                Grid.RowSpan="3"
                                Grid.Column="1"
                                FocusVisualMargin="-14,-6,-14,-6"
                                AutomationProperties.AccessibilityView="Raw" />
                </Grid>
                <Grid x:Name="VerticalTemplate" MinWidth="44" Visibility="Collapsed">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="*" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="18" />
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="18" />
                    </Grid.ColumnDefinitions>
                    <Rectangle x:Name="VerticalTrackRect"
                                Fill="{TemplateBinding Background}"
                                Width="{ThemeResource SliderTrackThemeHeight}"
                                Grid.Column="1"
                                Grid.RowSpan="3" />
                    <Rectangle x:Name="VerticalDecreaseRect"
                                Fill="{TemplateBinding Foreground}"
                                Grid.Column="1"
                                Grid.Row="2" />
                    <TickBar x:Name="LeftTickBar"
                                Visibility="Collapsed"
                                Fill="{ThemeResource SliderTickBarFill}"
                                Width="{ThemeResource SliderOutsideTickBarThemeHeight}"
                                HorizontalAlignment="Right"
                                Margin="0,0,4,0"
                                Grid.RowSpan="3" />
                    <TickBar x:Name="VerticalInlineTickBar"
                                Visibility="Collapsed"
                                Fill="{ThemeResource SliderInlineTickBarFill}"
                                Width="{ThemeResource SliderTrackThemeHeight}"
                                Grid.Column="1"
                                Grid.RowSpan="3" />
                    <TickBar x:Name="RightTickBar"
                                Visibility="Collapsed"
                                Fill="{ThemeResource SliderTickBarFill}"
                                Width="{ThemeResource SliderOutsideTickBarThemeHeight}"
                                HorizontalAlignment="Left"
                                Margin="4,0,0,0"
                                Grid.Column="2"
                                Grid.RowSpan="3" />
                    <Thumb x:Name="VerticalThumb"
                                Style="{StaticResource SliderThumbStyle}"
                                DataContext="{TemplateBinding Value}"
                                Width="24"
                                Height="8"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="3"
                                FocusVisualMargin="-6,-14,-6,-14"
                                AutomationProperties.AccessibilityView="Raw" />
                </Grid>
            </Grid>
        </Grid>
    </ControlTemplate>
    ```

    Этот код XAML занимает немало места! Шаблоны элементов управления— полезная функция, но они могут быть довольно сложными, поэтому обычно следует начинать с шаблона по умолчанию. 
    
3. В только что добавленном шаблоне **ControlTemplate** найдите элемент управления сетки с именем **HorizontalTemplate**. Эта сетка определяет ту часть шаблона, которую мы будем изменять.

    ```XAML
    <Grid x:Name="HorizontalTemplate" MinHeight="44">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="18" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="18" />
        </Grid.RowDefinitions>
    ```

5.  Создайте такой же многоугольник, что и для ползунка экспозиции в части 1. Добавьте этот многоугольник после закрывающего тега **Grid.RowDefinitions**. Установите для **Grid.Row** значение "0", для **Grid.RowSpan**— "3", а для **Grid.ColumnSpan**— "3". 

    **До**
    ```XAML
    <Grid x:Name="HorizontalTemplate" MinHeight="44">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="18" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="18" />
        </Grid.RowDefinitions>        
    ```

    **После**
    ```XAML
    <Grid x:Name="HorizontalTemplate" MinHeight="44">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="18" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="18" />
        </Grid.RowDefinitions>
        <Polygon Grid.Row="0" Grid.RowSpan="3"  Grid.ColumnSpan="3" Stretch="Fill"
                    Points="0,20 200,20 200,0" HorizontalAlignment="Stretch"  
                    VerticalAlignment="Center" Height="20" >
            <Polygon.Fill>
                <LinearGradientBrush StartPoint="0,0.5" EndPoint="1,0.5">
                    <LinearGradientBrush.GradientStops>
                        <GradientStop Offset="0" Color="Black" />
                        <GradientStop Offset="1" Color="White" />
                    </LinearGradientBrush.GradientStops>
                </LinearGradientBrush>
            </Polygon.Fill>
        </Polygon>           
    ```

6. Удалите параметр **Polygon.Fill**. Установите для **Fill** значение"{TemplateBinding Background}". Это необходимо, чтобы при установке значения для свойства **Background** ползунка также устанавливалось значение для свойства **Fill** многоугольника. 

    **До**
    ```XAML
        <Polygon Grid.Row="0" Grid.RowSpan="3"  Grid.ColumnSpan="3" Stretch="Fill"
                    Points="0,20 200,20 200,0" HorizontalAlignment="Stretch"  
                    VerticalAlignment="Center" Height="20" >
            <Polygon.Fill>
                <LinearGradientBrush StartPoint="0,0.5" EndPoint="1,0.5">
                    <LinearGradientBrush.GradientStops>
                        <GradientStop Offset="0" Color="Black" />
                        <GradientStop Offset="1" Color="White" />
                    </LinearGradientBrush.GradientStops>
                </LinearGradientBrush>
            </Polygon.Fill>
        </Polygon>           
    ```
    
    **После**
    ```XAML
        <Polygon Grid.Row="0" Grid.RowSpan="3"  Grid.ColumnSpan="3" Stretch="Fill"
                    Points="0,20 200,20 200,0" HorizontalAlignment="Stretch"  
                    VerticalAlignment="Center" Height="20" 
                    Fill="{TemplateBinding Background}">
        </Polygon>           
    ```    

7. Сразу после добавленного многоугольника находится прямоугольник с именем **HorizontalTrackRect**. Удалите параметр **Fill** этого прямоугольника, чтобы он стал невидимым и не загораживал фигуру многоугольника. (Полностью удалять прямоугольник не нужно, так как шаблон элемента управления использует его для визуальных эффектов взаимодействия, таких как наведение.)

    **До**
    ```XAML
        <Rectangle x:Name="HorizontalTrackRect"
                    Fill="{TemplateBinding Background}"
                    Height="{ThemeResource SliderTrackThemeHeight}"
                    Grid.Row="1"
                    Grid.ColumnSpan="3" />          
    ```
    
    **После**
    ```XAML
        <Rectangle x:Name="HorizontalTrackRect"
                    Height="{ThemeResource SliderTrackThemeHeight}"
                    Grid.Row="1"
                    Grid.ColumnSpan="3" />
    ```

    Шаблон готов. Теперь применим его к ползункам. 
    
8. Обновим ползунок экспозиции.

    * Установите для свойства **Template** ползунка значение "{StaticResource FancySliderControlTemplate}".
    * Удалите параметр Background="Transparent" ползунка. 
    * В качестве фона ползунка установите линейный градиент с переходом от черного к белому.
    * Удалите фоновый многоугольник, созданный в части 1.
        
    **До**
    ```XAML
    <Polygon Grid.Row="2" Stretch="Fill"
                Points="0,20 200,20 200,0" HorizontalAlignment="Stretch"  
                VerticalAlignment="Center" Height="20">
        <Polygon.Fill>
            <LinearGradientBrush StartPoint="0,0.5" EndPoint="1,0.5">
                <LinearGradientBrush.GradientStops>
                    <GradientStop Offset="0" Color="Black" />
                    <GradientStop Offset="1" Color="White" />
                </LinearGradientBrush.GradientStops>
            </LinearGradientBrush>
        </Polygon.Fill>
    </Polygon>
    <Slider Header="Exposure" 
            Grid.Row="2" Background="Transparent" Foreground="Transparent"
            Value="{x:Bind item.Exposure, Mode=TwoWay}"
            Minimum="-2"
            Maximum="2"
            Template="{StaticResource FancySliderControlTemplate}"/>    
    ```
    
    **После**
    ```XAML
    <Slider Header="Exposure" 
            Grid.Row="2"  Foreground="Transparent"
            Value="{x:Bind item.Exposure, Mode=TwoWay}"
            Minimum="-2"
            Maximum="2"
            Template="{StaticResource FancySliderControlTemplate}">
        <Slider.Background>
            <LinearGradientBrush StartPoint="0,0.5" EndPoint="1,0.5">
                <LinearGradientBrush.GradientStops>
                    <GradientStop Offset="0" Color="Black" />
                    <GradientStop Offset="1" Color="White" />
                </LinearGradientBrush.GradientStops>
            </LinearGradientBrush>
        </Slider.Background>
    </Slider>
    ```        
9. Внесите те же изменения для ползунка температуры.

    **До**
    ```XAML
    <Polygon Grid.Row="3" Stretch="Fill"
                Points="0,20 200,20 200,0" HorizontalAlignment="Stretch"  
                VerticalAlignment="Center" Height="20">
        <Polygon.Fill>
            <LinearGradientBrush StartPoint="0,0.5" EndPoint="1,0.5">
                <LinearGradientBrush.GradientStops>
                    <GradientStop Offset="0" Color="Blue" />
                    <GradientStop Offset="1" Color="Yellow" />
                </LinearGradientBrush.GradientStops>
            </LinearGradientBrush>
        </Polygon.Fill>
    </Polygon>
    <Slider Header="Temperature"
            Grid.Row="3" Background="Transparent" Foreground="Transparent"
            Value="{x:Bind item.Temperature, Mode=TwoWay}"
            Minimum="-1"
            Maximum="1" />
    ```
    
    **После**
    ```XAML
    <Slider Header="Temperature"
            Grid.Row="3" Foreground="Transparent"
            Value="{x:Bind item.Temperature, Mode=TwoWay}"
            Minimum="-1"
            Maximum="1"
            Template="{StaticResource FancySliderControlTemplate}">
        <Slider.Background>
            <LinearGradientBrush StartPoint="0,0.5" EndPoint="1,0.5">
                <LinearGradientBrush.GradientStops>
                    <GradientStop Offset="0" Color="Blue" />
                    <GradientStop Offset="1" Color="Yellow" />
                </LinearGradientBrush.GradientStops>
            </LinearGradientBrush>
        </Slider.Background>
    </Slider>
    ```    

10. Внесите те же изменения для ползунка оттенка.

    **До**
    ```XAML
    <Polygon Grid.Row="4" Stretch="Fill"
                Points="0,20 200,20 200,0" HorizontalAlignment="Stretch"  
                VerticalAlignment="Center" Height="20">
        <Polygon.Fill>
            <LinearGradientBrush StartPoint="0,0.5" EndPoint="1,0.5">
                <LinearGradientBrush.GradientStops>
                    <GradientStop Offset="0" Color="Red" />
                    <GradientStop Offset="1" Color="Green" />
                </LinearGradientBrush.GradientStops>
            </LinearGradientBrush>
        </Polygon.Fill>
    </Polygon>
    <Slider Header="Tint"
            Grid.Row="4" Background="Transparent" Foreground="Transparent"
            Value="{x:Bind item.Tint, Mode=TwoWay}"
            Minimum="-1"
            Maximum="1" />
    ```
    
    **После**
    ```XAML
    <Slider Header="Tint"
            Grid.Row="4" Foreground="Transparent"
            Value="{x:Bind item.Tint, Mode=TwoWay}"
            Minimum="-1"
            Maximum="1"
            Template="{StaticResource FancySliderControlTemplate}">
        <Slider.Background>
            <LinearGradientBrush StartPoint="0,0.5" EndPoint="1,0.5">
                <LinearGradientBrush.GradientStops>
                    <GradientStop Offset="0" Color="Red" />
                    <GradientStop Offset="1" Color="Green" />
                </LinearGradientBrush.GradientStops>
            </LinearGradientBrush>
        </Slider.Background>
    </Slider>
    ```        

11. Скомпилируйте и запустите приложение. 

    ![Лучшие ползунки в мире](../basics/images/xaml-basics/style-sliders-templates.png)
    
    Как видно, внесенные изменения улучшили расположение многоугольника: теперь его нижняя часть выровнена относительно нижней части бегунка ползунка.
    
<!-- TODO correct folder -->
Поздравляем, вы завершили изучение учебника. Если вы столкнулись с проблемами или хотите ознакомиться с окончательным решением, готовый пример можно найти в [репозитории примеров приложений UWP](https://github.com/Microsoft/Windows-universal-samples).