---
author: Karl-Bridge-Microsoft
ms.assetid: ''
title: Поддержка рукописного ввода в приложении UWP
description: Пошаговое руководство по добавлению поддержки рукописного ввода в приложение UWP.
keywords: рукописный ввод, учебник
ms.author: kbridge
ms.date: 01/25/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 62c62aacd894163ef2c65b9ddfe6d8299733a2e5
ms.sourcegitcommit: 3257416aebb5a7b1515e107866806f8bd57845a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "7166555"
---
# <a name="tutorial-support-ink-in-your-uwp-app"></a>Учебник: поддержка рукописного ввода в приложении UWP

![Ручка Surface](images/ink/ink-hero-small.png)  
*Ручка Surface* (доступна для приобретения в [Магазине Microsoft](https://aka.ms/purchasesurfacepen)).

В данной статье приводится пошаговое руководство по созданию простого приложения универсальной платформы Windows (UWP), которое поддерживает письмо и рисование с помощью Windows Ink. Мы используем фрагменты кода из примера приложения, которое можно скачать с GitHub (см. [Пример кода](#sample-code)), чтобы продемонстрировать различные возможности и связанные API-интерфейсы Windows Ink (см. раздел [Компоненты платформы Windows Ink](#components-of-the-windows-ink-platform)), описанные на каждом этапе.

Мы обращаем внимание на следующее:
* Добавление базовой поддержки рукописного ввода
* Добавление панели инструментов рукописного ввода
* Поддержка распознавания рукописного ввода
* Поддержка распознавания базовых фигур
* Загрузка и сохранение рукописного фрагмента

Дополнительные сведения о реализации этих функций см. в разделе [Взаимодействие с пером и Windows Ink в приложениях UWP](https://docs.microsoft.com/windows/uwp/input/pen-and-stylus-interactions).

## <a name="introduction"></a>Введение

С помощью Windows Ink можно обеспечить своих клиентов цифровым эквивалентом почти любого варианта работы с ручкой и бумагой от быстрых, рукописных примечаний и заметок до демонстрационных версий доски и от архитектурных и инженерных чертежей до персональных шедевров.

## <a name="prerequisites"></a>Что вам понадобится

* Компьютер (или виртуальная машина) под управлением текущей версии Windows10
* [Visual Studio 2017 и пакет SDK RS2](https://developer.microsoft.com/windows/downloads)
* [Windows10 SDK (10.0.15063.0)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* В зависимости от конфигурации, может потребоваться установить пакет NuGet [Microsoft.NETCore.UniversalWindowsPlatform](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/6.1.9) и включить **режим разработчика** в системных параметрах (параметры "->" обновление и безопасность -> для разработчиков "->" Использование функций разработчика).
* Если вы еще не знакомы с разработкой приложений универсальной платформы Windows (UWP) с помощью Visual Studio, изучите следующие разделы, прежде чем использовать этот учебник.  
    * [Подготовка](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)
    * [Создание приложения "Hello, world" (XAML)](https://docs.microsoft.com/windows/uwp/get-started/create-a-hello-world-app-xaml-universal)
* **[НЕОБЯЗАТЕЛЬНО]** Цифровое перо и компьютер с монитором, который поддерживает ввод от этого цифрового пера.

> [!NOTE] 
> Хотя Windows Ink может поддерживать рисование с помощью мыши и сенсорного ввода (мы покажем, как это сделать на этапе 3 этого учебника) для оптимальной работы Windows Ink, рекомендуется иметь в наличии цифровое перо и компьютер с монитором, который поддерживает ввод от этого цифрового пера.

## <a name="sample-code"></a>Образец кода
В этом руководстве мы используем пример приложения для рукописного ввода для демонстрации описанных основных понятий и функций.

Скачайте пример Visual Studio и исходный код с [GitHub](https://github.com/) в [примере windows-appsample-get-started-ink](https://aka.ms/appsample-ink):

1. Выберите зеленую кнопку **Клонировать или скачать**  
![Клонирование репозитория](images/ink/ink-clone.png)
2. Если у вас есть учетная запись GitHub, вы можете клонировать репозиторий на локальный компьютер, выбрав **Открыть в Visual Studio** 
3. Если у вас нет учетной записи GitHub или вам просто нужна локальная копия проекта, выберите **Скачать ZIP-файл** (чтобы скачивать последние обновления, необходимо будет регулярно следить за ними)

> [!IMPORTANT]
> Большая часть кода в примере закомментирована. На каждом этапе будет предложено раскомментировать различные части кода. В Visual Studio просто выделите строки кода и нажмите клавишу CTRL-K, а затем CTRL-U.

## <a name="components-of-the-windows-ink-platform"></a>Компоненты платформы Windows Ink

Эти объекты предоставляют большую часть рукописного ввода для приложений UWP.

| Компонент | Описание |
| --- | --- |
| [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas) | Элемент управления платформы XAMLUI, который по умолчанию получает и отображает все входные данные от пера как росчерк пера или росчерк стирания. |
| [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011) | Объект кода программной части, создаваемый вместе с элементом управления [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) (предоставляется свойством [**InkCanvas.InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.InkPresenter)). Этот объект обеспечивает все возможности рукописного ввода по умолчанию, предоставляемые элементом [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas), вместе с полным набором API для дополнительной настройки и персонализации. |
| [**InkToolbar**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbar.aspx) | Элемент управления платформы XAMLUI, содержащий и расширяемую коллекцию кнопок, которые активируют функции рукописного ввода в связанные [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas). |
| [**IInkD2DRenderer**](https://msdn.microsoft.com/library/mt147263)<br/>Здесь не описаны эти функции. Дополнительные сведения см. в разделе [Сложный пример рукописного ввода](http://go.microsoft.com/fwlink/p/?LinkID=620314). | Позволяет преобразовать росчерки пера в специальный контекст устройства Direct2D универсального приложения для Windows вместо элемента управления по умолчанию [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535). |

## <a name="step-1-run-the-sample"></a>Шаг1. Запуск примера

После загрузки примера приложения RadialController убедитесь, что оно запускается.
1. Откройте скачанный проект в Visual Studio.
2. В раскрывающемся списке **Платформы решений** выберите вариант без ARM.
3. Нажмите клавишу F5, чтобы скомпилировать, развернуть и запустить.  

   > [!NOTE]
   > Кроме того, можно последовательно выбрать пункты меню **Отладка** > **Начать отладку** или нажать кнопку запуска **локального компьютера**, показанную здесь.
   > ![Кнопка "Создать проект" в Visual Studio](images/ink/ink-vsrun-small.png)

Откроется окно приложения и после появления экрана-заставки на несколько секунд вы увидите этот начальный экран.

![Пустое приложение](images/ink/ink-app-step1-empty-small.png)

Итак, теперь у нас есть базовое приложение UWP, которое будет использоваться в остальных пунктах настоящего руководства. На следующих этапах мы добавим наши функции рукописного ввода.

## <a name="step-2-use-inkcanvas-to-support-basic-inking"></a>Шаг 2. Использование InkCanvas для поддержки базового рукописного ввода

Возможно, вы уже заметили, что приложение в своей исходной форме не позволяет ничего рисовать с помощью пера (хотя вы можете использовать перо как стандартный указатель для взаимодействия с приложением). 

Давайте исправим этот небольшой недостаток на этом этапе.

Чтобы добавить базовую функцию рукописного ввода, просто поместите элемент управления платформы UWP [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) на соответствующую страницу в приложении.

> [!NOTE]
> InkCanvas по умолчанию имеет значения свойств [**Height**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.Height) и [**Width**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.Width) равными нулю, если он не является дочерним элементом, который автоматически задает размеры своих дочерних элементов. 

### <a name="in-the-sample"></a>В примере:
1. Откройте файл MainPage.xaml.cs.
2. Найдите код, отмеченный заголовком этого этапа ("// Шаг 2. Использование InkCanvas для поддержки базового рукописного ввода").
3. Раскомментируйте следующие строки. (Эти ссылки необходимы для функции, используемой на последующих этапах).  

``` csharp
    using Windows.UI.Input.Inking;
    using Windows.UI.Input.Inking.Analysis;
    using Windows.UI.Xaml.Shapes;
    using Windows.Storage.Streams;
```

4. Откройте файл MainPage.xaml.
5. Найдите код с заголовком этого шага ("\<!-- Шаг 2. Базовый рукописный ввод с использованием InkCanvas-->").
6. Раскомментируйте следующую строку.  

``` xaml
    <InkCanvas x:Name="inkCanvas" />
```

Вот и все! 

Теперь повторно запустите приложение. Дальше нарисуйте, запишите ваше имя или (если вы держите зеркало или у вас очень хорошая память) нарисуйте автопортрет.

![Базовый рукописный ввод](images/ink/ink-app-step1-name-small.png)

## <a name="step-3-support-inking-with-touch-and-mouse"></a>Шаг 3. Поддержка рукописного ввода с помощью мыши и сенсорного ввода

Вы заметите, что по умолчанию рукописный ввод поддерживает только ввод с помощью пера. При попытке написать или нарисовать что-то с помощью пальца, мыши или сенсорной панели, вы будете разочарованы.

Чтобы устранить эту неприятность, необходимо добавить вторую строку кода. Сейчас он находится в коде программной части для XAML-файла, в котором объявляется ваш [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas). 

На этом этапе мы представляем объект [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter), который обеспечивает управление вводом, обработку и отрисовку рукописного ввода (стандартного и модифицированного) на более детальном уровне на вашем [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas).

> [!NOTE]
> Стандартный рукописный ввод (кончик пера или кнопка/кончик ластика) не изменены с помощью таких дополнительных возможностей, как кнопка пера, правая кнопка мыши или подобные механизмы. 

Чтобы включить рукописный ввод с помощью мыши и сенсорного ввода, задайте свойство [**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.InputDeviceTypes) [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter) для сочетания значений [**CoreInputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.core.coreinputdevicetypes), которые вам необходимы.

### <a name="in-the-sample"></a>В примере:
1. Откройте файл MainPage.xaml.cs.
2. Найдите код с заголовком этого шага ("// Шаг 3. Поддержка рукописного ввода с помощью мыши и сенсорной панели").
3. Раскомментируйте следующие строки.  

``` csharp
    inkCanvas.InkPresenter.InputDeviceTypes =
        Windows.UI.Core.CoreInputDeviceTypes.Mouse | 
        Windows.UI.Core.CoreInputDeviceTypes.Touch | 
        Windows.UI.Core.CoreInputDeviceTypes.Pen;
```

Снова запустите приложение и вы обнаружите, что все ваши мечты о рисовании пальцем на экране компьютера сбылись!

> [!NOTE]
> При определении типов устройств ввода необходимо указать поддержку для каждого конкретного типа ввода (включая перо), так как установка этого свойства переопределяет значение параметра [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas) по умолчанию.

## <a name="step-4-add-an-ink-toolbar"></a>Шаг 4. Добавление панели инструментов рукописного ввода

[**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) — это элемент управления платформы UWP, который предоставляет настраиваемый и расширяемый набор кнопок для активации возможностей, связанных с рукописным вводом. 

По умолчанию [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) содержит базовый набор кнопок, которые позволяют пользователям быстро выбрать перо, карандаш, маркер или ластик. Любой из этих элементов можно использовать вместе с трафаретом (линейкой или транспортиром). Каждая кнопка (перо, карандаш и маркер) также предоставляет всплывающий элемент для выбора цвета и размера росчерка рукописного ввода.

Чтобы добавить [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) по умолчанию в приложение рукописного ввода, просто поместите его на одной странице с вашим [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas) и свяжите эти два элемента управления.

### <a name="in-the-sample"></a>В примере:
1. Откройте файл MainPage.xaml.
2. Найдите код с заголовком этого шага ("\<!-- Шаг 4. Добавление панели инструментов рукописного ввода -->").
3. Раскомментируйте следующие строки.  

``` xaml
    <InkToolbar x:Name="inkToolbar" 
                        VerticalAlignment="Top" 
                        Margin="10,0,10,0"
                        TargetInkCanvas="{x:Bind inkCanvas}">
    </InkToolbar>
```

> [!NOTE]
> Для сохранения пользовательского интерфейса и кода максимально простыми и лаконичными насколько это возможно мы используем базовый макет сетки и объявляем [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) после [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas) в строке сетки. Если вы объявите его перед [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas), сначала будет обработан [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) под холстом и недоступен для пользователя.  

Теперь запустите приложение еще раз, чтобы увидеть [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar), и попробуйте использовать некоторые из этих инструментов.

![InkToolbar из альбома в Ink Workspace](images/ink/ink-inktoolbar-default-small.png)

### <a name="challenge-add-a-custom-button"></a>Задача: добавление пользовательской кнопки
<table class="wdg-noborder">
<tr>
<td>

![InkToolbar из альбома в Ink Workspace](images/challenge-icon.png)

</td>
<td>

Вот пример настраиваемого элемента **[InkToolbar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar)** (из альбома в Windows Ink Workspace).

![InkToolbar из альбома в Ink Workspace](images/ink/ink-inktoolbar-sketchpad-small.png)

Дополнительные сведения о настройке [InkToolbar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) см. в разделе [Добавление InkToolbar в приложение универсальной платформы Windows (UWP) для рукописного ввода](ink-toolbar.md).

</td>
</tr>
</table>

## <a name="step-5-support-handwriting-recognition"></a>Шаг 5. Поддержка распознавания рукописного ввода

Теперь, когда вы можете писать и рисовать в приложении, давайте попытаемся сделать что-то полезное с этими рисунками.

На этом этапе мы используем функции распознавания рукописного ввода Windows Ink для расшифровки того, что вы написали.

> [!NOTE]
> Распознавание рукописного ввода может быть улучшено с помощью параметров **Перо и Windows Ink**:
> 1. Откройте меню "Пуск" и выберите **Параметры**.
> 2. На экране параметров выберите **Устройства** > **Перо и Windows Ink**.
> ![InkToolbar из альбома в Ink Workspace](images/ink/ink-settings-small.png)
> 3. Выберите **Изучить почерк**, чтобы открыть диалоговое окно **Персонализация рукописного текста**.
> ![InkToolbar из альбома в Ink Workspace](images/ink/ink-settings-handwritingpersonalization-small.png)

### <a name="in-the-sample"></a>В примере:
1. Откройте файл MainPage.xaml.
2. Найдите код с заголовком этого шага ("\<!-- Шаг 5. Поддержка распознавания рукописного ввода -->").
3. Раскомментируйте следующие строки.  

``` xaml
    <Button x:Name="recognizeText" 
            Content="Recognize text"  
            Grid.Row="0" Grid.Column="0"
            Margin="10,10,10,10"
            Click="recognizeText_ClickAsync"/>
    <TextBlock x:Name="recognitionResult" 
                Text="Recognition results: "
                VerticalAlignment="Center" 
                Grid.Row="0" Grid.Column="1"
                Margin="50,0,0,0" />
```

4. Откройте файл MainPage.xaml.cs.
5. Найдите код с заголовком этого шага (" Шаг 5. Поддержка распознавания рукописного ввода").
6. Раскомментируйте следующие строки.  

- Это глобальные переменные, которые необходимы для этого шага.

``` csharp
    InkAnalyzer analyzerText = new InkAnalyzer();
    IReadOnlyList<InkStroke> strokesText = null;
    InkAnalysisResult resultText = null;
    IReadOnlyList<IInkAnalysisNode> words = null;
```

- Это обработчик для кнопки **Распознавание текста**, где мы выполняем обработку распознавания.

``` csharp
    private async void recognizeText_ClickAsync(object sender, RoutedEventArgs e)
    {
        strokesText = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();
        // Ensure an ink stroke is present.
        if (strokesText.Count > 0)
        {
            analyzerText.AddDataForStrokes(strokesText);

            resultText = await analyzerText.AnalyzeAsync();

            if (resultText.Status == InkAnalysisStatus.Updated)
            {
                words = analyzerText.AnalysisRoot.FindNodes(InkAnalysisNodeKind.InkWord);
                foreach (var word in words)
                {
                    InkAnalysisInkWord concreteWord = (InkAnalysisInkWord)word;
                    foreach (string s in concreteWord.TextAlternates)
                    {
                        recognitionResult.Text += s;
                    }
                }
            }
            analyzerText.ClearDataForAllStrokes();
        }
    }
```

7. Снова запустите приложение и напишите что-то, а затем нажмите кнопку **Распознавание текста**
8. Результаты распознавания отображаются рядом с кнопкой

### <a name="challenge-1-international-recognition"></a>Задача 1. Международное распознавание
<table class="wdg-noborder">
<tr>
<td>

![InkToolbar из альбома в Ink Workspace](images/challenge-icon.png)

</td>
<td>

Windows Ink поддерживает распознавание текста для многих языков, поддерживаемых Windows. Каждый языковой пакет включает модуль распознавания рукописного ввода, который можно установить с языковым пакетом.

Выберите необходимый язык путем запроса установленных модулей распознавания рукописного ввода.

Дополнительные сведения о международном распознавании рукописного входа см. в разделе [Распознавание росчерков пера Windows Ink как текста](convert-ink-to-text.md).

</td>
</tr>
</table>

### <a name="challenge-2-dynamic-recognition"></a>Задача 2. Динамическое распознавание
<table class="wdg-noborder">
<tr>
<td>

![InkToolbar из альбома в Ink Workspace](images/challenge-icon.png)

</td>
<td>

В этом руководстве требуется, чтобы кнопка была нажата для запуска распознавания. Вы также можете выполнить динамическое распознавание, используя основную функцию синхронизации.

Дополнительные сведения о динамическом распознавании см. в разделе [Распознавание росчерков пера Windows Ink как текста](convert-ink-to-text.md).

</td>
</tr>
</table>

## <a name="step-6-recognize-shapes"></a>Шаг 6. Распознавание фигур

Хорошо! Теперь можно преобразовать рукописные заметки во что-нибудь более разборчивое. Но как насчет этих дрожащих, кофеиносодержащих записок с утреннего собрания "Flowcharters Anonymous"?

С помощью анализа рукописного текста ваше приложение также может распознавать ряд основных фигур, в том числе:

- Круг
- Ромб
- Рисунок
- Эллипс
- Равносторонний треугольник
- Шестиугольник
- Равнобедренный треугольник
- Параллелограмм
- Пятиугольник
- Четырехугольник
- Прямоугольник
- Прямоугольный треугольник
- Квадрат
- Трапеция
- Треугольник

На этом этапе мы используем функции распознавания фигур Windows Ink для очистки ваших записок.

В этом примере мы не пытаемся перерисовать росчерки пера (хотя это возможно). Вместо этого мы добавим стандартный холст под InkCanvas, на котором мы нарисуем эквивалентные объекты эллипсов или многоугольников, полученных из исходного рукописного ввода. Затем мы удалим соответствующие росчерки пера.

### <a name="in-the-sample"></a>В примере:
1. Откройте файл MainPage.xaml
2. Найдите код с заголовком этого шага ("\<!-- Шаг 6. Распознавание фигур -->")
3. Раскомментируйте эту строку.  

``` xaml
   <Canvas x:Name="canvas" />

   And these lines.

    <Button Grid.Row="1" x:Name="recognizeShape" Click="recognizeShape_ClickAsync"
        Content="Recognize shape" 
        Margin="10,10,10,10" />
```

4. Откройте файл MainPage.xaml.cs
5. Найдите код с заголовком этого шага ("// Шаг 6. Распознавание фигур")
6. Раскомментируйте эти строки:  

``` csharp
    private async void recognizeShape_ClickAsync(object sender, RoutedEventArgs e)
    {
        ...
    }

    private void DrawEllipse(InkAnalysisInkDrawing shape)
    {
        ...
    }

    private void DrawPolygon(InkAnalysisInkDrawing shape)
    {
        ...
    }
```

7. Запустите приложение, нарисуйте несколько фигур и нажмите кнопку **Распознавание форм**

Вот пример элементарной блок-схемы из цифрового шаблона.

![Блок-схема исходного рукописного ввода](images/ink/ink-app-step6-shapereco1-small.png)

Вот эта же блок-схема после распознавания фигур.

![Блок-схема исходного рукописного ввода](images/ink/ink-app-step6-shapereco2-small.png)


## <a name="step-7-save-and-load-ink"></a>Шаг 7. Сохранение и загрузка рукописного ввода

Итак, вы создали эскиз и вам нравится то, что вы видите, но вы считаете, что можете изменить несколько элементов позже? Можно сохранить ваши росчерки пера в файл в формате Ink Serialized Format (ISF) и загрузить для редактирования в момент, когда вас настигнет вдохновение. 

Файл ISF— это основное изображение GIF, которое включает дополнительные метаданные, описывающие свойства и поведение росчерков пера. Приложения, в которых отключена поддержка рукописного ввода, могут игнорировать дополнительные метаданные и по-прежнему загружать основное GIF-изображение (включая прозрачный фон альфа-канала).

На этом этапе мы подключим кнопки **Сохранить** и **Загрузить**, расположенные рядом с панелью рукописного ввода.

### <a name="in-the-sample"></a>В примере:
1. Откройте файл MainPage.xaml.
2. Найдите код с заголовком этого шага ("\<!-- Шаг 7. Загрузка и сохранение рукописного фрагмента -->").
3. Раскомментируйте следующие строки. 

``` xaml
    <Button x:Name="buttonSave" 
            Content="Save" 
            Click="buttonSave_ClickAsync"
            Width="100"
            Margin="5,0,0,0"/>
    <Button x:Name="buttonLoad" 
            Content="Load"  
            Click="buttonLoad_ClickAsync"
            Width="100"
            Margin="5,0,0,0"/>
```

4. Откройте файл MainPage.xaml.cs.
5. Найдите код с заголовком этого шага ("// Шаг 7. Загрузка и сохранение рукописного фрагмента").
6. Раскомментируйте следующие строки.  

``` csharp
    private async void buttonSave_ClickAsync(object sender, RoutedEventArgs e)
    {
        ...
    }

    private async void buttonLoad_ClickAsync(object sender, RoutedEventArgs e)
    {
        ...
    }
```

7. Запустите приложение и нарисуйте что-нибудь.
8. Нажмите кнопку **Сохранить** и сохраните документ.
9. Очистите рукописный ввод или перезапустите приложение.
10. Нажмите кнопку **Загрузить** и откройте файл с рукописным фрагментом, который вы только что сохранили.

### <a name="challenge-use-the-clipboard-to-copy-and-paste-ink-strokes"></a>Задача. Использование буфера обмена для копирования и вставки росчерков пера 
<table class="wdg-noborder">
<tr>
<td>

![InkToolbar из альбома в Ink Workspace](images/challenge-icon.png)

</td>

<td>

Рукописный ввод Windows также поддерживает копирование и вставку росчерков пера в буфер обмена и из него.

Дополнительные сведения об использовании буфера обмена с рукописным вводом см. в разделе [Хранение и извлечение данных движения пера Windows Ink](save-and-load-ink.md).

</td>
</tr>
</table>

## <a name="summary"></a>Краткий обзор

Поздравляем, вы завершили изучение учебника **Ввод: поддержка рукописного ввода в приложении UWP**! Мы показали вам базовый код, необходимый для поддержки рукописного ввода в приложениях UWP, и способы предоставления некоторых усовершенствованных возможностей для пользователя, поддерживаемых платформой Windows Ink.

## <a name="related-articles"></a>Статьи по теме

* [Взаимодействие с пером и Windows Ink в приложениях UWP](pen-and-stylus-interactions.md)

### <a name="samples"></a>Примеры

* [Пример анализа рукописного ввода (базовый) (C#)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-analysis-basic.zip)
* [Пример распознавания рукописного ввода (C#)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-handwriting-reco.zip)
* [Сохранение и загрузка росчерков пера из файла Ink Serialized Format (ISF)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store.zip)
* [Сохранение и загрузка росчерков пера из буфера обмена](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store-clipboard.zip)
* [Пример расположения и ориентации панели инструментов рукописного ввода (базовый)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-toolbar-handedness.zip)
* [Пример расположения и ориентации панели инструментов рукописного ввода (динамичный)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-toolbar-handedness-dynamic.zip)
* [Простой пример рукописного ввода (C#/C++)](http://go.microsoft.com/fwlink/p/?LinkID=620312)
* [Сложный пример рукописного ввода (C++)](http://go.microsoft.com/fwlink/p/?LinkID=620314)
* [Пример рукописного ввода (JavaScript)](http://go.microsoft.com/fwlink/p/?LinkID=620308)
* [Руководство по началу работы: поддержка рукописного ввода в приложении UWP](https://aka.ms/appsample-ink)
* [Пример раскраски](https://aka.ms/cpubsample-coloringbook)
* [Пример семейных заметок](https://aka.ms/cpubsample-familynotessample)
