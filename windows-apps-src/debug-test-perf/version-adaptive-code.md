---
author: jwmsft
title: "Адаптивный к версии код"
description: "Узнайте, как воспользоваться новыми API, при этом сохраняя совместимость с предыдущими версиями"
translationtype: Human Translation
ms.sourcegitcommit: 3f81d80cef0fef6d24cad1b42ce9726b03857b5a
ms.openlocfilehash: db6b9c83d36ac876661197dce81e5724e44bb640

---

# Адаптивный к версии код: используйте новые API, сохраняя совместимость с предыдущими версиями

В каждом выпуске Windows 10 SDK добавляются новые возможности, которыми вы наверняка захотите воспользоваться. Однако не все ваши пользователи обновляют свои устройства до последней версии Windows 10 одновременно, при этом вам нужно, чтобы ваше приложение работала на максимальном количестве устройств. В этой статье мы покажем вам, как спроектировать приложение так, чтобы оно работало в предыдущих версиях Windows 10, но также могло использовать новые возможности, если оно запущено на устройстве с последним обновлением.

Чтобы приложение поддерживало максимальное количество устройств с Windows 10, необходимо выполнить два действия. Во-первых, укажите в качестве цели для проекта Visual Studio новейшие API. Это повлияет на то, что происходит, когда вы компилируете приложение. Во-вторых, выполните проверки времени выполнения, чтобы можно было вызывать только API-интерфейсы, доступные на устройстве, где запущено приложение.

> [!NOTE] 
> В этой статье используются примеры из пакета Windows Insider Preview SDK для Windows 10 версии 1607 (юбилейное обновление). Пакет Preview SDK — это предварительная версия, которая не может использоваться в рабочей среде. Установите SDK только на тестовом компьютере. Пакет Preview SDK содержит исправления ошибок и находящиеся на стадии разработки изменения контактной зоны API. Если вы работаете над приложением, которое необходимо отправить в Магазин, не устанавливайте предварительную версию.

## Настройка проекта Visual Studio

Первый шаг для поддержки нескольких версий Windows 10 — указание *целевой* и *минимальной* поддерживаемых версий ОС и SDK в проекте Visual Studio.
- *Целевая версия*: версия SDK, для которой Visual Studio компилирует код приложения и запускает все инструменты. Все API и ресурсы в этой версии SDK доступны в коде приложения во время компиляции.
- *Минимальная версия*: версия SDK, которая поддерживает самую раннюю версию ОС, в которой может работать приложение (и для которой оно будет развернуто Магазином), а также версия, для которой Visual Studio компилирует код разметки приложения. 

Во время выполнения приложение запускается для версии ОС, в которой оно развернуто, поэтому приложение будет вызывать исключения при использовании ресурсов или API-интерфейсов, которые недоступны в этой версии. Мы покажем, как использовать проверки времени выполнения для вызова правильных API, далее в этой статье.

Целевая и минимальная версии указывают границы диапазона версий ОС и SDK. Однако если вы тестируете приложение в минимальной версии, оно будет работать во всех версиях между минимальной и целевой.

> [!TIP]
> Visual Studio не предупреждает вас о совместимости API. Именно вы должны убедиться, что ваше приложение работает, как ожидается, на всех версиях в диапазоне, включая минимальную и целевую версии.

При создании проекта в Visual Studio 2015 с обновление 2 или более поздней версии вам будет предложено установить целевую и минимальную версии, которые поддерживает ваше приложение. По умолчанию целевая версия — это самая старшая установленная версия SDK, а минимальная версия — это наименьшая установленная версия SDK. В качестве целевой и минимальной версии можно выбрать только те версии SDK, которые установлены на компьютере. 

![Выбор целевого пакета SDK в Visual Studio](images/vs-target-sdk-1.png)

Обычно мы рекомендуем оставить значения по умолчанию. Однако если у вас установлена предварительная версия SDK и вы пишете рабочий код, вам придется изменить целевую версию на последнюю официальную версию SDK. 

Чтобы изменить минимальную и целевую версию для проекта, который уже создан в Visual Studio, перейдите в меню "Проект -> Свойства -> вкладка «Приложения» > Нацеливание".

![Изменение целевой версии SDK в Visual Studio](images/vs-target-sdk-2.png) 

Далее представлены номера сборки для каждого SDK для справки:
- Windows 10 версии 1506: версия SDK 10240
- Windows 10 версии 1511 (обновление за ноябрь): версия SDK 10586
- Windows 10 Insider Preview версии 1607 (юбилейное обновление): на момент написания статьи [последняя версия Insider Preview SDK — 14332](https://blogs.windows.com/buildingapps/2016/04/28/windows-10-anniversary-sdk-preview-build-14332-released/).

Вы можете скачать все выпущенные версии SDK из [архива Windows SDK и эмулятора](https://developer.microsoft.com/downloads/sdk-archive). Последнюю версию Windows Insider Preview SDK можно скачать в разделе для разработчиков сайта [Windows Insider](https://insider.windows.com/).

## Написание адаптивного кода

Написание адаптивного кода аналогично созданию [адаптивного пользовательского интерфейса](https://msdn.microsoft.com/windows/uwp/layout/layouts-with-xaml). Вы можете спроектировать базовый интерфейс для самого небольшого экрана, а затем перемещать и добавлять элементы, если вы обнаружите, что приложение работает на устройстве с большим экраном. Для создания адаптивного кода вы пишите базовый код для самой ранней версии ОС и добавляете дополнительные функции, если обнаруживаете, что приложение работает в более поздней версии ОС, где эти функции доступны.

### Проверки API во время выполнения

Используйте класс [Windows.Foundation.Metadata.ApiInformation](https://msdn.microsoft.com/library/windows/apps/windows.foundation.metadata.apiinformation.aspx) в своем коде в условном выражении, чтобы проверить наличие API-интерфейса, который вы хотите вызвать. Это условие оценивается при каждом запуске приложения, но оно будет **истинным** только на устройствах, где этот API-интерфейс присутствует и, следовательно, доступен. Это позволяет писать адаптивный к версии код, чтобы создавать приложения, использующие API, доступные только в определенных версиях ОС.

Рассмотрим конкретные примеры использования новых функций в Windows Insider Preview. Общий обзор использования класса **ApiInformation** см. в [руководстве по приложениям UWP](https://msdn.microsoft.com/windows/uwp/get-started/universal-application-platform-guide) и записи блога [Динамическое определение компонентов с контрактами API](https://blogs.windows.com/buildingapps/2015/09/15/dynamically-detecting-features-with-api-contracts-10-by-10/).

> [!TIP]
> Многочисленные проверки API во время выполнения могут повлиять на производительность приложения. В этих примерах мы покажем проверки встроенными. В рабочем коде необходимо выполнить проверку только один раз и кэшировать результат, а затем использовать его во всем приложении. 

### Неподдерживаемые сценарии

В большинстве случаев вы можете сохранить для приложения минимальную версию SDK 10240 и использовать проверки во время выполнения, чтобы включить новые API, если приложение работает в более поздней версии системы. Однако в ряде случаев необходимо увеличить минимальную версию приложения, чтобы использовать новые функции.

Это нужно сделать, если вы используете:
- новый API, которому требуется возможность, недоступна в более ранней версии. Вам необходимо увеличить минимальную версию до той, которая поддерживает эту возможность. Подробнее см. в разделе [Объявления характеристик приложения](../packaging/app-capability-declarations.md);
- любые новые ключи ресурсов, добавленные в generic.xaml и недоступные в предыдущей версии. Версия файла generic.xaml, используемого во время выполнения, определяется по версии ОС устройства. Невозможно использовать проверки API во время выполнения, чтобы определить наличие ресурсов XAML. Поэтому необходимо использовать только ключи ресурсов, которые доступны в минимальной версии, поддерживаемой приложением, иначе в нем возникнет сбой из-за исключения [XAMLParseException](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.markup.xamlparseexception.aspx) во время выполнения.

### Варианты адаптивного кода

Создать адаптивный код можно двумя способами. В большинстве случаев вы пишете разметку приложения для минимальной версии, а затем используете код приложения, чтобы добавить более новые функции, если ОС их поддерживает. Однако если вам нужно обновить свойство в визуальном состоянии, а между версиями ОС изменилось только свойство или значения перечисления, вы можете создать расширяемый триггер состояния, который активируется в зависимости от наличия API.

Здесь мы сравним эти варианты.

**Код приложения**

Когда следует использовать:
- рекомендуется для всех сценариев адаптивного кода, за исключением конкретных случаев, указанных ниже для расширяемых триггеров.

Преимущества:
- упрощает обработку отличий API в разметке.

Недостатки:
- Нет поддержки конструктора.

**Триггеры состояния**

Когда следует использовать:
- только если между версиями ОС изменилось свойство или перечисление, связанное с визуальным состоянием, для чего не требуется изменение логики.

Преимущества:
- позволяет создавать специальные визуальные состояния, которые активируются в зависимости от наличия API.
- Доступна частичная поддержка конструктора.

Недостатки:
- использование настраиваемых триггеров ограничено визуальным состояниям, что не сводится к сложным адаптивным макетам;
- Следует использовать методы установки, чтобы задавать измененные значения, поэтому возможны только простые изменения;
- пользовательские триггеры состояния достаточно подробны для настройки и применения.

## Примеры адаптивного кода

В этом разделе мы приведем несколько примеров адаптивного кода, использующего новые API Windows 10 версии 1607 (Windows Insider Preview).

### Пример 1: новое значение перечисления

В Windows 10 версии 1607 добавлено новое значение перечисления [InputScopeNameValue](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.input.inputscopenamevalue.aspx): **ChatWithoutEmoji**. Поведение этого типа вводимых данных совпадает с типом **Chat** (проверка правописания, автозаполнение, автоматическое применение заглавных букв), но он сопоставляет с сенсорной клавиатурой без кнопки смайлика. Это полезно, если вы создаете собственное средство выбора смайликов и хотите отключить встроенную кнопку смайлика на сенсорной клавиатуре. 

В этом примере показано, как проверить, доступно ли значение перечисления **ChatWithoutEmoji**, и задать свойство [InputScope](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textbox.inputscope.aspx) элемента **TextBox**, если это так. Если значение отсутствует в системе, где запущено приложение, для **InputScope** задается значение **Chat**. Показанный код можно разместить в конструкторе Page или обработчике событий Page.Loaded.

> [!TIP]
> При проверке API используйте статические строки, а не полагайтесь на возможности языка .NET, иначе приложение может попытаться обратиться к неопределенному типу, что приведет к сбою во время выполнения.

**C#**
```csharp
// Create a TextBox control for sending messages 
// and initialize an InputScope object.
TextBox messageBox = new TextBox();
messageBox.AcceptsReturn = true;
messageBox.TextWrapping = TextWrapping.Wrap;
InputScope scope = new InputScope();
InputScopeName scopeName = new InputScopeName();

// Check that the ChatWithEmoji value is present.
// (It's present starting with Windows 10, version 1607,
//  the Target version for the app. This check returns false on earlier versions.)         
if (ApiInformation.IsEnumNamedValuePresent("Windows.UI.Xaml.Input.InputScopeNameValue", "ChatWithoutEmoji"))
{
    // Set new ChatWithoutEmoji InputScope if present.
    scopeName.NameValue = InputScopeNameValue.ChatWithoutEmoji;
}
else
{
    // Fall back to Chat InputScope.
    scopeName.NameValue = InputScopeNameValue.Chat;
}

// Set InputScope on messaging TextBox.
scope.Names.Add(scopeName);
messageBox.InputScope = scope;

// For this example, set the TextBox text to show the selected InputScope.
messageBox.Text = messageBox.InputScope.Names[0].NameValue.ToString();

// Add the TextBox to the XAML visual tree (rootGrid is defined in XAML).
rootGrid.Children.Add(messageBox);
```

В предыдущем примере элемент создается TextBox, и все его свойства задаются в коде. Однако если у вас есть существующий код XAML и необходимо просто изменить свойство InputScope в системах, где новое значение поддерживается, это можно сделать, не изменяя XAML, как показано ниже. Вы задаете значение по умолчанию **Chat** в XAML, но переопределяете его в коде, если значение **ChatWithoutEmoji** присутствует.

**XAML**
```xaml
<TextBox x:Name="messageBox"
         AcceptsReturn="True" TextWrapping="Wrap"
         InputScope="Chat"
         Loaded="messageBox_Loaded"/>
```

**C#**
```csharp
private void messageBox_Loaded(object sender, RoutedEventArgs e)
{
    if (ApiInformation.IsEnumNamedValuePresent("Windows.UI.Xaml.Input.InputScopeNameValue", "ChatWithoutEmoji"))
    {
        // Check that the ChatWithEmoji value is present.
        // (It's present starting with Windows 10, version 1607,
        //  the Target version for the app. This code is skipped on earlier versions.)
        InputScope scope = new InputScope();
        InputScopeName scopeName = new InputScopeName();
        scopeName.NameValue = InputScopeNameValue.ChatWithoutEmoji;
        // Set InputScope on messaging TextBox.
        scope.Names.Add(scopeName);
        messageBox.InputScope = scope;
    }

    // For this example, set the TextBox text to show the selected InputScope.
    // This is outside of the API check, so it will happen on all OS versions.
    messageBox.Text = messageBox.InputScope.Names[0].NameValue.ToString();
}
```

Теперь рассмотрим, как параметры целевой и минимальной версии применяются к нашему примеру.

В этих примерах можно использовать значение перечисления Chat в XAML или в коде без проверки, поскольку оно присутствует в минимальной поддерживаемой версии ОС. 

При использовании значения ChatWithoutEmoji в XAML или в коде без проверки компиляция пройдет без ошибок, поскольку это значение есть в целевой версии ОС. Приложение также запустится без ошибок в системе с целевой версией ОС. Однако при запуске приложения на компьютере с минимальной версией ОС произойдет сбой во время выполнения, поскольку значение перечисления ChatWithoutEmoji отсутствует. Поэтому это значение следует использовать только в коде и создать для него оболочку в виде проверки API, чтобы вызывать его, только если значение поддерживается в текущей системе.

### Пример 2: новый элемент управления

В новой версии Windows обычно предоставляются новые элементы управления на поверхности API платформы UWP, которые реализуют новые возможности. Чтобы использовать новый элемент управления используйте метод [ApiInformation.IsTypePresent](https://msdn.microsoft.com/library/windows/apps/windows.foundation.metadata.apiinformation.istypepresent.aspx).

В Windows 10 версии 1607 появился новый элемент управления мультимедиа [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx). Он основан на классе [MediaPlayer](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplayer.aspx), поэтому предоставляет такие функции, как привязка к фоновому звуку, а также использует архитектурные улучшения стека функций мультимедиа.

Однако если приложение выполняется на устройстве с версией Windows 10 более ранней, чем 1607, необходимо использовать элемент [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaelement.aspx) вместо нового элемента управления **MediaPlayerElement**. Вы можете использовать метод [**ApiInformation.IsTypePresent**](https://msdn.microsoft.com/library/windows/apps/windows.foundation.metadata.apiinformation.istypepresent.aspx), чтобы проверить наличие элемента управления MediaPlayerElement во время выполнения, и загрузить подходящий элемент.

В этом примере показано, как создать приложение, которое использует новый элемент MediaPlayerElement или старый элемент MediaElement в зависимости от того, имеется доступен ли тип MediaPlayerElement. В коде используйте класс [UserControl](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.usercontrol.aspx), чтобы разбить элементы управления, их интерфейс и код на компоненты и переключаться между ними зависимости от версии ОС. Кроме того, можно использовать настраиваемый элемент управления, который предоставляет дополнительные функции, но это выходит за рамки данного простого примера.
 
**MediaPlayerUserControl** 

`MediaPlayerUserControl` инкапсулирует элемент **MediaPlayerElement** и несколько кнопок, которые используются для перехода по мультимедиа кадр за кадром. UserControl позволяет обрабатывать эти элементы управления как единый объект и упрощает переключение на MediaElement в старых системах. Этот пользовательский элемент управления следует использовать только в системах, где MediaPlayerElement поддерживается, поэтому в коде внутри этого элемента управления проверки ApiInformation не выполняются.

> [!NOTE]
> Для упрощения этого примера кнопки перехода по кадрам размещаются вне проигрывателя мультимедиа. Для улучшения взаимодействия с пользователем необходимо добавить в MediaTransportControls собственные кнопки. Подробнее см. в разделе [Пользовательские элементы управления транспортировкой](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/custom-transport-controls). 

**XAML**
```xaml
<UserControl
    x:Class="MediaApp.MediaPlayerUserControl"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:MediaApp"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    d:DesignHeight="300"
    d:DesignWidth="400">

    <Grid x:Name="MPE_grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>
        <StackPanel Orientation="Horizontal" 
                    HorizontalAlignment="Center" Grid.Row="1">
            <RepeatButton Click="StepBack_Click" Content="Step Back"/>
            <RepeatButton Click="StepForward_Click" Content="Step Forward"/>
        </StackPanel>
    </Grid>
</UserControl>
```

**C#**
```csharp
using System;
using Windows.Media.Core;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

namespace MediaApp
{
    public sealed partial class MediaPlayerUserControl : UserControl
    {
        public MediaPlayerUserControl()
        {
            this.InitializeComponent();
            
            // The markup code compiler runs against the Minimum OS version so MediaPlayerElement must be created in app code
            MPE = new MediaPlayerElement();
            Uri videoSource = new Uri("ms-appx:///Assets/UWPDesign.mp4");
            MPE.Source = MediaSource.CreateFromUri(videoSource);
            MPE.AreTransportControlsEnabled = true;
            MPE.MediaPlayer.AutoPlay = true;

            // Add MediaPlayerElement to the Grid
            MPE_grid.Children.Add(MPE);

        }

        private void StepForward_Click(object sender, RoutedEventArgs e)
        {
            // Step forward one frame, only available using MediaPlayerElement.
            MPE.MediaPlayer.StepForwardOneFrame();
        }

        private void StepBack_Click(object sender, RoutedEventArgs e)
        {
            // Step forward one frame, only available using MediaPlayerElement.
            MPE.MediaPlayer.StepForwardOneFrame();
        }
    }
}
```

**MediaElementUserControl**
 
`MediaElementUserControl` инкапсулирует элемент управления **MediaElement**.

**XAML**
```xaml
<UserControl
    x:Class="MediaApp.MediaElementUserControl"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:MediaApp"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    d:DesignHeight="300"
    d:DesignWidth="400">

    <Grid>
        <MediaElement AreTransportControlsEnabled="True" 
                      Source="Assets/UWPDesign.mp4"/>
    </Grid>
</UserControl>
```

> [!NOTE]
> Кодовая страница `MediaElementUserControl` содержит только сформированный код, поэтому она не показана.

**Инициализация элемента управления на основе IsTypePresent**

Во время выполнения вы вызываете **ApiInformation.IsTypePresent**, чтобы проверить наличие MediaPlayerElement. Если элемент есть, вы загружаете `MediaPlayerUserControl`, если нет — загружается `MediaElementUserControl`.

**C#**
```csharp
public MainPage()
{
    this.InitializeComponent();

    UserControl mediaControl;

    // Check for presence of type MediaPlayerElement.
    if (ApiInformation.IsTypePresent("Windows.UI.Xaml.Controls.MediaPlayerElement"))
    {
        mediaControl = new MediaPlayerUserControl();
    }
    else
    {
        mediaControl = new MediaElementUserControl();
    }

    // Add mediaControl to XAML visual tree (rootGrid is defined in XAML).
    rootGrid.Children.Add(mediaControl);
}
```

> [!IMPORTANT]
> Помните, что эта проверка только устанавливает для объекта `mediaControl` значение `MediaPlayerUserControl` или `MediaElementUserControl`. Эти условные проверки следует выполнять в любом месте кода, где требуется определить, нужно ли использовать API MediaPlayerElement или MediaElement. Проверку необходимо выполнить только один раз и кэшировать результат, а затем использовать его во всем приложении.

## Примеры триггера состояния

Расширяемые триггеры состояния позволяют использовать разметку и код совместно для активации изменения визуального состояния на основе условия, которое вы проверяете в коде — в нашем случае это наличие определенного API. Мы не рекомендуем использовать триггеры состояния для общих сценариев адаптивного кода, так как они потребляют много ресурсов и применяются только к визуальному состоянию. 

Триггеры состояния необходимо использовать для адаптивного кода, только если пользовательский интерфейс в разных версиях ОС претерпевает незначительные изменения, которые не повлияют на оставшийся пользовательский интерфейс — это может быть изменение свойства или значения перечисления элемента управления.

### Пример 1: новое свойство

Первый шаг настройки расширяемого триггера состояния — создание подкласса для класса [StateTriggerBase](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.statetriggerbase.aspx), чтобы создать пользовательский триггер, который будет активироваться в зависимости от присутствия API. В этом примере показан триггер, который активируется, если наличие свойства соответствует переменной `_isPresent`, заданной в XAML.

**C#**
```csharp
class IsPropertyPresentTrigger : StateTriggerBase
{
    public string TypeName { get; set; }
    public string PropertyName { get; set; }

    private Boolean _isPresent;
    private bool? _isPropertyPresent = null;

    public Boolean IsPresent
    {
        get { return _isPresent; }
        set
        {
            _isPresent = value;
            if (_isPropertyPresent == null)
            {
                // Call into ApiInformation method to determine if property is present.
                _isPropertyPresent =
                ApiInformation.IsPropertyPresent(TypeName, PropertyName);
            }

            // If the property presence matches _isPresent then the trigger will be activated;
            SetActive(_isPresent == _isPropertyPresent);
        }
    }
}
```

Следующий шаг — настройка триггера визуального состояния в XAML, чтобы получить два различных визуальных состояния в зависимости от присутствия API. 

В Windows 10 версии 1607 добавлено новое свойство для класса [FrameworkElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.aspx), [AllowFocusOnInteraction](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.allowfocusoninteraction.aspx), которое определяет, получает ли элемент управления фокус, когда пользователь взаимодействует с ним. Это удобно, если вам нужно удерживать фокус на текстовом поле для ввода данных (и продолжать показывать сенсорную клавиатуру), когда пользователь нажимает кнопку.

Триггер в этом примере проверяет, доступно ли свойство. Если свойство присутствует, для свойства **AllowFocusOnInteraction** кнопки задается значение **false**; если свойство отсутствует, то кнопка сохраняет первоначальное состояние. В пример включен элемент TextBox, чтобы упростить просмотр действия этого свойства при запуске кода.

**XAML**
```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel>
        <TextBox Width="300" Height="36"/>
        <!-- Button to set the new property on. -->
        <Button x:Name="testButton" Content="Test" Margin="12"/>
    </StackPanel>

    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="propertyPresentStateGroup">
            <VisualState>
                <VisualState.StateTriggers>
                    <!--Trigger will activate if the AllowFocusOnInteraction property is present-->
                    <local:IsPropertyPresentTrigger 
                        TypeName="Windows.UI.Xaml.FrameworkElement" 
                        PropertyName="AllowFocusOnInteraction" IsPresent="True"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Target="testButton.AllowFocusOnInteraction" 
                            Value="False"/>
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Grid>
```

### Пример 2: новое значение перечисления

В этом примере показано, как задать различные значения перечисления в зависимости от того, доступно ли то или иное значение. Здесь настраиваемый триггер состояния используется, чтобы достичь того же результата, что и предыдущем примере чата. В данном примере вы используете новый тип вводимых данных ChatWithoutEmoji, если устройство работает под управлением Windows 10 версии 1607, в противном случае используется тип вводимых данных **Chat**. Визуальные состояния, которые используют этот триггер, настраиваются в стиле *если-иначе*, где тип вводимых данных выбирается в зависимости от наличия нового значения перечисления.

**C#**
```csharp
class IsEnumPresentTrigger : StateTriggerBase
{
    public string EnumTypeName { get; set; }
    public string EnumValueName { get; set; }

    private Boolean _isPresent;
    private bool? _isEnumValuePresent = null;

    public Boolean IsPresent
    {
        get { return _isPresent; }
        set
        {
            _isPresent = value;

            if (_isEnumValuePresent == null)
            {
                // Call into ApiInformation method to determine if value is present.
                _isEnumValuePresent =
                ApiInformation.IsEnumNamedValuePresent(EnumTypeName, EnumValueName);
            }

            // If the property presence matches _isPresent then the trigger will be activated;
            SetActive(_isPresent == _isEnumValuePresent);
        }
    }
}
```

**XAML**
```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <TextBox x:Name="messageBox"
     AcceptsReturn="True" TextWrapping="Wrap"/>


    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="EnumPresentStates">
            <!--if-->
            <VisualState x:Name="isPresent">
                <VisualState.StateTriggers>
                    <local:IsEnumPresentTrigger 
                        EnumTypeName="Windows.UI.Xaml.Input.InputScopeNameValue" 
                        EnumValueName="ChatWithoutEmoji" IsPresent="True"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Target="messageBox.InputScope" Value="ChatWithoutEmoji"/>
                </VisualState.Setters>
            </VisualState>
            <!--else-->
            <VisualState x:Name="isNotPresent">
                <VisualState.StateTriggers>
                    <local:IsEnumPresentTrigger 
                        EnumTypeName="Windows.UI.Xaml.Input.InputScopeNameValue" 
                        EnumValueName="ChatWithoutEmoji" IsPresent="False"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Target="messageBox.InputScope" Value="Chat"/>
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Grid>
```
## Статьи по теме

- [Руководство по приложениям UWP](https://msdn.microsoft.com/windows/uwp/get-started/universal-application-platform-guide)
- [Динамическое обнаружение компонентов с контрактами API](https://blogs.windows.com/buildingapps/2015/09/15/dynamically-detecting-features-with-api-contracts-10-by-10/)



<!--HONumber=Jun16_HO4-->

