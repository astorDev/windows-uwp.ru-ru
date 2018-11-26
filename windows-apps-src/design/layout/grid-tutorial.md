---
Description: This tutorial walks through how to create a basic application user interface. It explains and demonstrates the use of Grid and StackPanel, two of the most common XAML elements.
title: Использование Grid и StackPanel для создания простого погодного приложения.
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: Windows10, UWP
ms.assetid: 9794a04d-e67f-472c-8ba8-8ebe442f6ef2
ms.localizationpriority: medium
ms.openlocfilehash: 5b221220d417df5b70927984ac65eff93fae54a4
ms.sourcegitcommit: 681c70f964210ab49ac5d06357ae96505bb78741
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "7707038"
---
# <a name="tutorial-use-grid-and-stackpanel-to-create-a-simple-weather-app"></a>Учебник: использование Grid и StackPanel для создания простого погодного приложения

Используйте XAML для создания макетов простого погодного приложения с помощью элементов **Grid** и **StackPanel**. С помощью этих инструментов можно создать визуально привлекательные приложения, которые хорошо работают на любом устройстве с Windows10. Работа с этим учебным материалом занимает 10–20 минут.

> **Важные API**: [Класс Grid](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.grid), [Класс StackPanel](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.stackpanel)

## <a name="prerequisites"></a>Предварительные требования
- Windows 10 и Microsoft Visual Studio 2015 или более поздней версии. (Новые Visual Studio рекомендуется для текущей разработки и безопасности обновления) [Щелкните здесь, чтобы узнать, как подготовиться к работе с Visual Studio](../../get-started/get-set-up.md).
- Сведения о том, как создать базовое приложение "Hello World" с помощью XAML и C#. [Щелкните здесь, чтобы узнать, как создать приложение "Hello World"](https://msdn.microsoft.com/windows/uwp/get-started/create-a-hello-world-app-xaml-universal), если у вас его еще нет.

## <a name="step-1-create-a-blank-app"></a>Шаг 1. Создайте пустое приложение
1. В меню Visual Studio выберите **Файл** > **Новый проект**.
2. На левой панели диалогового окна **Новый проект** выберите **Visual C#** > **Windows** > **Universal** или **Visual C++** > **Windows** > **Universal**.
3. На центральной панели выберите **Пустое приложение**.
4. В поле **Имя** введите **WeatherPanel** и нажмите **ОК**.
5. Чтобы запустить программу, выберите **Отладка** > **Начать отладку** в меню или нажмите клавишу F5.

## <a name="step-2-define-a-grid"></a>Шаг 2. Определите элемент Grid
В XAML элемент **Grid** состоит из серии строк и столбцов. Указав строку и столбец элемента в пределах элемента **Grid**, вы сможете размещать и разделять другие элементы в пользовательском интерфейсе. Строки и столбцы определяются элементами **RowDefinition** и **ColumnDefinition**.

Чтобы начать создание макета, откройте **MainPage.xaml** с помощью **Обозревателя решений** и замените автоматически созданный элемент **Grid** этим кодом.

```xml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="3*"/>
        <ColumnDefinition Width="5*"/>
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="2*"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
</Grid>
```

Новый элемент **Grid** создает набор строк и столбцов, определяя таким образом макет интерфейса приложения. Параметр **Width** первого столбца имеет значение "3\*", а второго — "5\*". Таким образом, горизонтальное пространство между этими двумя столбцами разделяется в соотношении 3:5. Также и параметр **Height** двух строк имеет значение "2\*" и "\*" соответственно, поэтому элемент **Grid** назначает вдвое больше места первой строке, чем второй ("\*" — это то же самое, что и "1\*"). Эти соотношения сохраняются даже при изменении размера окна или смене устройства.

Узнать о других методах определения размера строк и столбцов можно узнать в разделе [Определение макетов с помощью XAML](https://msdn.microsoft.com/windows/uwp/layout/layouts-with-xaml#layout-properties).

Если запустить приложение на этом этапе, вы увидите только пустую страницу, так как ни в одной области элемента **Grid** нет содержимого. Чтобы отобразить элемент **Grid** можно придать ему цвет.

## <a name="step-3-color-the-grid"></a>Шаг 3. Придайте цвет элементу Grid
Чтобы придать цвет элементу **Grid**, добавим три элемента **Border**, каждый из которых имеет собственный цвет фона. Каждый из них также назначается строке и столбцу в родительском элементе **Grid** при помощи атрибутов **Grid.Row** и **Grid.Column**. По умолчанию эти атрибуты имеют значения 0, поэтому их не нужно назначать первому элементу **Border**. Добавьте следующий код в элемент **Grid** после определений строк и столбцов.

```xml
<Border Background="#2f5cb6"/>
<Border Grid.Column ="1" Background="#1f3d7a"/>
<Border Grid.Row="1" Grid.ColumnSpan="2" Background="#152951"/>
```

Обратите внимание, что для третьего элемента **Border** мы используем дополнительный атрибут, **Grid.ColumnSpan**, который позволяет элементу **Border** охватить оба столбца в нижней строке. Вы можете использовать **Grid.RowSpan** таким же образом. Вместе эти атрибуты позволяют растягивать элемент на любое количество строк и столбцов. Верхний левый угол охватываемой области — это всегда значения **Grid.Column** и **Grid.Row**, указанные в атрибутах элемента.

Если запустить приложение, результат будет примерно таким.

![Придание цвета элементу Grid](images/grid-weather-1.png)

## <a name="step-4-organize-content-by-using-stackpanel-elements"></a>Шаг 4. Упорядочивание содержимого с помощью элементов StackPanel
**StackPanel** — это второй элемент пользовательского интерфейса, который мы будем использовать для создания нашего погодного приложения. Элемент **StackPanel** является одной из основных составляющих многих базовых макетов приложений, позволяя распределять элементы вертикально или горизонтально.

В следующем коде мы создаем два элемента **StackPanel** и заполняем каждый из них тремя элементами **TextBlocks**. Добавьте эти элементы **StackPanel** в элемент **Grid** под элементами **Border** из шага 3. Тогда элементы **TextBlock** будут отображаться поверх цветного элемента **Grid**, который мы создали ранее.

```xml
<StackPanel Grid.Column="1" Margin="40,0,0,0" VerticalAlignment="Center">
    <TextBlock Foreground="White" FontSize="25" Text="Today - 64° F"/>
    <TextBlock Foreground="White" FontSize="25" Text="Partially Cloudy"/>
    <TextBlock Foreground="White" FontSize="25" Text="Precipitation: 25%"/>
</StackPanel>
<StackPanel Grid.Row="1" Grid.ColumnSpan="2" Orientation="Horizontal"
            HorizontalAlignment="Center" VerticalAlignment="Center">
    <TextBlock Foreground="White" FontSize="25" Text="High: 66°" Margin="0,0,20,0"/>
    <TextBlock Foreground="White" FontSize="25" Text="Low: 43°" Margin="0,0,20,0"/>
    <TextBlock Foreground="White" FontSize="25" Text="Feels like: 63°"/>
</StackPanel>
```

В первом элементе **Stackpanel** каждый элемент **TextBlock** расположен вертикально под следующим. Это стандартное поведение элемента StackPanel, поэтому нам не нужно устанавливать атрибут **Orientation**. Во втором элементе StackPanel нужно сделать так, чтобы дочерние элементы располагались горизонтально слева направо, поэтому мы установили атрибуту **Orientation** значение "Horizontal". Нам также нужно установить атрибуту **Grid.ColumnSpan** значение "2", чтобы текст выравнивался по центру над нижним элементом **Border**.

Если запустить приложение, результат будет примерно таким.

![Добавление элементов StackPanel](images/grid-weather-2.png)

## <a name="step-5-add-an-image-icon"></a>Шаг 5. Добавление значка изображения

Наконец, заполним пустой раздел элемента **Grid** изображением, символизирующим сегодняшнюю погоду— переменная облачность.

Скачайте изображение ниже и сохраните его в формате PNG с именем "partially-cloudy".

![Переменная облачность](images/partially-cloudy.PNG)

В **Обозревателе решений** щелкните правой кнопкой мыши папку **Ресурсы** и выберите **Добавить** -> **Существующий элемент...** В открывшемся обозревателе найдите файл partially-cloudy.png, выберите его и нажмите **Добавить**.

Затем в **MainPage.xaml** добавьте следующий элемент **Image** ниже элементов StackPanel из шага 4.

```xml
<Image Margin="20" Source="Assets/partially-cloudy.png"/>
```

Поскольку нам нужно разместить элемент Image в первой строке и столбце, нам не требуется устанавливать его атрибуты **Grid.Row** и **Grid.Column**— оставим их значения по умолчанию ("0").

Вот и все! Вы успешно создали макет для простого погодного приложения. Если запустить приложение с помощью клавиши **F5**, вы должны увидеть нечто подобное:

![Пример погодной панели](images/grid-weather-3.PNG)

При желании вы можете поэкспериментировать с макетом выше и изучить различные способы представления данных о погоде.

## <a name="related-articles"></a>Статьи по теме
Введение в проектирование макетов приложений UWP см. в разделе [Введение в проектирование приложений UWP](https://msdn.microsoft.com/windows/uwp/layout/design-and-ui-intro)

Сведения о создании гибких макетов, адаптирующихся к разным размерам экрана, см. в разделе [Определение макетов страниц с помощью XAML](https://msdn.microsoft.com/windows/uwp/layout/layouts-with-xaml)
