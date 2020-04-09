---
Description: TwoPaneView — это элемент управления макетом, позволяющий управлять отображением приложений с 2 различными областями содержимого.
title: Представление с двумя панелями
template: detail.hbs
ms.date: 01/22/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 19b473ea964ee9c6cb82098e142f066258bcf30b
ms.sourcegitcommit: af4050f69168c15b0afaaa8eea66a5ee38b88fed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80081444"
---
# <a name="two-pane-view"></a>Представление с двумя панелями

[TwoPaneView](/uwp/api/microsoft.ui.xaml.controls.twopaneview) — это элемент управления макетом, позволяющий управлять отображением приложений с 2 различными областями содержимого, например с основным и подробным представлением.

> [!IMPORTANT]
> В этой статье описаны функциональные возможности предварительной версии, а также инструкции по ее использованию, которые могут быть существенно изменены, прежде чем версия станет общедоступной. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.

Несмотря на то что элемент управления TwoPaneView работает на всех устройствах с Windows, его предназначение — позволить вам в полной мере автоматически использовать преимущества двухэкранных устройств без специального кодирования. На двухэкранном устройстве представление с двумя панелями обеспечивает четкое разделение пользовательского интерфейса, при котором ваше содержимое размещается с обеих сторон промежутка между экранами.

> [!NOTE]
> _Двухэкранное устройство_ — это особый тип устройств с уникальными возможностями. Оно не эквивалентно компьютеру с несколькими мониторами. Дополнительные сведения о двухэкранных устройствах см. в [этой статье](/dual-screen/introduction). (Дополнительные сведения об оптимизации своего приложения для нескольких мониторов см. в [этой статье](/windows/uwp/design/layout/show-multiple-views).)

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Элемент управления **TwoPaneView** является частью библиотеки пользовательского интерфейса Windows, пакета NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в описании [библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **API-интерфейсы библиотеки пользовательского интерфейса Windows:** [Класс TwoPaneView](/uwp/api/microsoft.ui.xaml.controls.twopaneview)
>
> **API платформы:** [Класс TwoPaneView](/uwp/api/windows.ui.xaml.controls.twopaneview)

В этом документе мы будем использовать в XAML псевдоним **muxc** для всех API библиотеки пользовательского интерфейса Windows, которую мы добавили в проект. Мы добавили его для нашего элемента [Page](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page):

```xaml
xmlns:muxc="using:Microsoft.UI.Xaml.Controls"
```

В коде C# мы также будем использовать псевдоним **muxc** для всех API библиотеки пользовательского интерфейса Windows, которую мы добавили в проект. Мы добавили оператор **using** в верхней части файла:

```csharp
using muxc = Microsoft.UI.Xaml.Controls;
```

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Представление с двумя панелями следует использовать при наличии 2 отдельных областей содержимого и:

- если необходимо, чтобы содержимое автоматически перегруппировывалось и изменяло размер по ширине окна;
- если необходимо, чтобы вторичная область содержимого отображалась/скрывалась в зависимости от доступного пространства;
- если необходимо четко распределить содержимое между двумя экранами двухэкранного устройства.

## <a name="examples"></a>Примеры

На приведенных ниже изображениях показано приложение, работающее на одном экране или открытое на двух экранах. Представление с двумя панелями адаптирует пользовательский интерфейс приложения к различным конфигурациям экрана.

![Представление с двумя панелями приложения, открытого на одном экране](images/two-pane-view/tpv-single.png)

> _Приложение на одном экране._

![Представление с двумя панелями приложения, открытого на двух экранах в горизонтальном режиме](images/two-pane-view/tpv-dual-wide.png)

> _Приложение, открытое на двухэкранном устройстве в горизонтальном режиме._

![Представление с двумя панелями приложения, открытого на двух экранах в вертикальном режиме](images/two-pane-view/tpv-dual-tall.png)

> _Приложение, открытое на двухэкранном устройстве в вертикальном режиме._

## <a name="how-it-works"></a>Принцип работы

Представление с двумя панелями содержит две панели для размещения содержимого. Размер и расположение панелей регулируется в зависимости от пространства, доступного для окна. Возможные макеты панели определяются перечислением [TwoPaneViewMode](/uwp/api/microsoft.ui.xaml.controls.twopaneviewmode):

| Значение&nbsp;перечисления | Описание |
| - | - |
| `SinglePane` | Отображается только одна панель, как указано в свойстве [PanePriority](/uwp/api/microsoft.ui.xaml.controls.twopaneview.panepriority). |
| `Wide` | Панели отображаются рядом, или отображается одна панель, как указано в свойстве [WideModeConfiguration](/uwp/api/microsoft.ui.xaml.controls.twopaneview.widemodeconfiguration). |
| `Tall` | Панели отображаются сверху и снизу, либо отображается одна панель, как указано в свойстве [TallModeConfiguration](/uwp/api/microsoft.ui.xaml.controls.twopaneview.tallmodeconfiguration). |

Для настройки представления с двумя панелями задайте [PanePriority](/uwp/api/microsoft.ui.xaml.controls.twopaneview.panepriority), указав, какая панель будет отображаться при наличии пространства лишь для одной панели. Затем укажите, где должна отображаться панель `Pane1`: сверху или снизу для вертикальных окон, слева или справа для горизонтальных окон.

Представление с двумя панелями управляет размером и расположением панелей, но вам все равно необходимо настроить адаптацию содержимого внутри панели к изменениям размера и ориентации. Дополнительные сведения о создании адаптивного пользовательского интерфейса см. в статьях [Responsive layouts with XAML](/windows/uwp/design/layout/layouts-with-xaml) (Гибкие макеты в XAML) и [Layout panels](/windows/uwp/design/layout/layout-panels) (Панели макета).

[TwoPaneView](/uwp/api/microsoft.ui.xaml.controls.twopaneview) управляет отображением панелей в зависимости от состояния объединения приложения.

- На одном экране

    Если приложение открыто на одном экране, `TwoPaneView` корректирует размер и расположение своих панелей на основе заданных параметров свойств. Эти свойства более подробно описаны в следующем разделе. Единственное различие между устройствами заключается в том, что некоторые устройства, например настольные компьютеры, допускают изменение размера окон, а другие устройства — нет.

- На двух экранах

    `TwoPaneView` упрощает оптимизацию пользовательского интерфейса для двухэкранных устройств. Размер окна изменяется для использования всего доступного пространства на экранах. Если ваше приложение открыто на двух экранах устройства, каждый экран будет отображать содержимое одной из панелей, правильно распределенное с учетом промежутка. При использовании представления с двумя панелями поддержка разделения встроена. Вам лишь нужно настроить высоту/ширину, указав, на каком из экранов должна отображаться каждая панель. Представление с двумя панелями настроит остальные параметры.

## <a name="how-to-use-the-two-pane-view-control"></a>Использование элемента управления "Представление с двумя панелями"

[TwoPaneView](/uwp/api/microsoft.ui.xaml.controls.twopaneview) не обязательно должен быть корневым элементом макета вашей страницы. Вы будете часто использовать его внутри элемента управления [NavigationView](/uwp/api/microsoft.ui.xaml.controls.navigationview), который обеспечивает общую навигацию для вашего приложения. Элемент `TwoPaneView` адаптируется соответствующим образом, независимо от того, где он находится в дереве XAML. Однако мы рекомендуем не вкладывать `TwoPaneView` в другой элемент `TwoPaneView`. (Если же вы это сделаете, лишь внешний элемент `TwoPaneView` будет поддерживать разделение.)

### <a name="add-content-to-the-panes"></a>Добавление содержимого на панель

Каждая панель представления с двумя панелями может содержать один элемент XAML `UIElement`. Чтобы добавить содержимое, как правило, необходимо разместить панель макета XAML на каждой панели, а затем добавить другие элементы управления и содержимое на панель. Панели могут изменять размер и переключаться между горизонтальным и вертикальным режимами, поэтому необходимо настроить адаптацию содержимого каждой панели к таким изменениям. Дополнительные сведения о создании адаптивного пользовательского интерфейса см. в статьях [Responsive layouts with XAML](/windows/uwp/design/layout/layouts-with-xaml) (Гибкие макеты в XAML) и [Layout panels](/windows/uwp/design/layout/layout-panels) (Панели макета).

В этом примере создается простой пользовательский интерфейс приложения, отображающего изображения и информацию, который показан в _этом разделе_. Если приложение открыто на двух экранах, изображение и информация будут отображаться на отдельных экранах. При открытии на одном экране содержимое может отображаться на двух панелях или на одной, в зависимости от доступного пространства. (При наличии пространства лишь для одной панели необходимо переместить содержимое панели Pane2 на панель Pane1 и настроить для пользователя прокрутку, позволяющую просмотреть любое скрытое содержимое. Код для этого приведен далее в разделе _Реакция на изменения режима_.)

![Небольшое изображение примера приложения, открытого на двух экранах](images/two-pane-view/tpv-left-right.png)

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" MinHeight="40"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>

    <CommandBar DefaultLabelPosition="Right">
        <AppBarButton x:Name="Share" Icon="Share" Label="Share" Click="Share_Click"/>
        <AppBarButton x:Name="Print" Icon="Print" Label="Print" Click="Print_Click"/>
    </CommandBar>

    <muxc:TwoPaneView
        x:Name="MyTwoPaneView"
        Grid.Row="1"
        MinWideModeWidth="959"
        MinTallModeHeight="863"
        ModeChanged="TwoPaneView_ModeChanged">

        <muxc:TwoPaneView.Pane1>
            <Grid x:Name="Pane1Root">
                <ScrollViewer>
                    <StackPanel x:Name="Pane1StackPanel">
                        <Image Source="Assets\LandscapeImage8.jpg"
                               VerticalAlignment="Top" HorizontalAlignment="Center"
                               Margin="16,0"/>
                    </StackPanel>
                </ScrollViewer>
            </Grid>
        </muxc:TwoPaneView.Pane1>

        <muxc:TwoPaneView.Pane2>
            <Grid x:Name="Pane2Root">
                <ScrollViewer x:Name="DetailsContent">
                    <StackPanel Padding="16">
                        <TextBlock Text="Mountain.jpg" MaxLines="1"
                                       Style="{ThemeResource HeaderTextBlockStyle}"/>
                        <TextBlock Text="Date Taken:"
                                       Style="{ThemeResource SubheaderTextBlockStyle}"
                                       Margin="0,24,0,0"/>
                        <TextBlock Text="8/29/2019 9:55am"
                                       Style="{ThemeResource SubtitleTextBlockStyle}"/>
                        <TextBlock Text="Dimensions:"
                                       Style="{ThemeResource SubheaderTextBlockStyle}"
                                       Margin="0,24,0,0"/>
                        <TextBlock Text="800x536"
                                       Style="{ThemeResource SubtitleTextBlockStyle}"/>
                        <TextBlock Text="Resolution:"
                                       Style="{ThemeResource SubheaderTextBlockStyle}"
                                       Margin="0,24,0,0"/>
                        <TextBlock Text="96 dpi"
                                       Style="{ThemeResource SubtitleTextBlockStyle}"/>
                        <TextBlock Text="Description:"
                                       Style="{ThemeResource SubheaderTextBlockStyle}"
                                       Margin="0,24,0,0"/>
                        <TextBlock Text="Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Maecenas porttitor congue massa. Fusce posuere, magna sed pulvinar ultricies, purus lectus malesuada libero, sit amet commodo magna eros quis urna."
                                       Style="{ThemeResource SubtitleTextBlockStyle}"
                                       TextWrapping="Wrap"/>
                    </StackPanel>
                </ScrollViewer>
            </Grid>
        </muxc:TwoPaneView.Pane2>
    </muxc:TwoPaneView>

    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="TwoPaneViewStates">
            <VisualState x:Name="Normal"/>
            <VisualState x:Name="Wide">
                <VisualState.Setters>
                    <Setter Target="MyTwoPaneView.Pane1Length"
                            Value="2*"/>
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Grid>
```

### <a name="specify-which-pane-to-display"></a>Определение панели для отображения

Если представление с двумя панелями может отображать лишь одну панель, оно использует свойство [PanePriority](/uwp/api/microsoft.ui.xaml.controls.twopaneview.panepriority), чтобы определить нужную панель. По умолчанию для PanePriority установлено значение **Pane1**. Вы можете установить это свойство в XAML или в коде, как описано ниже.

```xaml
<muxc:TwoPaneView x:Name="MyTwoPaneView" PanePriority="Pane2">
```

```csharp
MyTwoPaneView.PanePriority = Microsoft.UI.Xaml.Controls.TwoPaneViewPriority.Pane2;
```

### <a name="pane-sizing"></a>Изменение размера панелей

При открытии на одном экране размер панели определяется свойствами [Pane1Length](/uwp/api/microsoft.ui.xaml.controls.twopaneview.pane1length) и [Pane2Length](/uwp/api/microsoft.ui.xaml.controls.twopaneview.pane2length). Они используют значения [GridLength](/uwp/api/windows.ui.xaml.gridlength), которые поддерживают _автоматическое_ определение размера и определение размера _по типу "звезды"_ (\*). Описание этих типов определения размера см. в разделе _Layout properties_ (Свойства макета) в статье [Responsive layouts with XAML](/windows/uwp/design/layout/layouts-with-xaml#layout-properties) (Гибкие макеты в XAML).

По умолчанию для `Pane1Length` установлено значение `Auto`, и размер панели масштабируется в соответствии с размером содержимого. Для `Pane2Length` установлена `*`, и эта панель использует все оставшееся пространство.

![Представление с двумя панелями, для которых заданы размеры по умолчанию](images/two-pane-view/tpv-size-default.png)

> _Панели с размером по умолчанию_

Значения по умолчанию удобно использовать для типичного макета основного/подробного представления, где список элементов отображается на панели `Pane1`, а множество подробных сведений — на панели `Pane2`. Однако, в зависимости от содержимого, вы можете разделить пространство по-разному. Здесь для `Pane1Length` установлено значение `2*`, поэтому для этой панели выделено вдвое больше пространства, чем для панели `Pane2`.

```xaml
<muxc:TwoPaneView x:Name="MyTwoPaneView" Pane1Length="2*">
```

![Представление с панелью pane 1, занимающей две трети экрана, и панелью pane 2, занимающей одну треть](images/two-pane-view/tpv-size-2.png)

> _Панели с размером 2* и *_

> [!NOTE]
> Как упоминалось ранее, если приложение открыто на двух экранах, эти свойства игнорируются и каждая панель заполняет один из экранов.

Если для панели задано автоматическое изменение размера, вы можете контролировать размер, задав высоту и ширину панели, в которой хранится содержимое. В этом случае вам может потребоваться обработать событие ModeChanged и установить ограничения высоты и ширины содержимого в соответствии с текущим режимом.

### <a name="display-in-wide-or-tall-mode"></a>Отображение в горизонтальном или вертикальном режиме

На одном экране [режим](/uwp/api/microsoft.ui.xaml.controls.twopaneview.mode) отображения представления с двумя панелями определяется свойствами [MinWideModeWidth](/uwp/api/microsoft.ui.xaml.controls.twopaneview.minwidemodewidth) и [MinTallModeHeight](/uwp/api/microsoft.ui.xaml.controls.twopaneview.mintallmodeheight). Для двух свойств задано значение по умолчанию 641px, такое же, как и для свойства [NavigationView.CompactThresholdWidth](/uwp/api/windows.ui.xaml.controls.navigationview.compactmodethresholdwidth).

В приведенной ниже таблице показано, как свойства высоты и ширины TwoPaneView определяют использование режима отображения.

| Условие TwoPaneView  | Режим |
|---------|---------|
| `Width` > `MinWideModeWidth` | Используется режим `Wide` |
| `Width` <= `MinWideModeWidth` и `Height` > `MinTallModeHeight` | Используется режим `Tall` |
| `Width` <= `MinWideModeWidth` и `Height` <= `MinTallModeHeight` | Используется режим `SinglePane` |


> [!NOTE]
> Как упоминалось ранее, если приложение открыто на двух экранах, эти свойства игнорируются и режим отображения определяется в зависимости от _состояния_ устройства.

#### <a name="wide-configuration-options"></a>Параметры конфигурации режима широкого окна

Представление с двумя панелями переходит в режим `Wide`, если один дисплей шире, чем свойство `MinWideModeWidth`. `MinWideModeWidth` контролирует переход представления с двумя панелями в горизонтальный режим. Значение по умолчанию — 641px, но вы можете изменить его на любое другое. В общем, вы должны установить это свойство равным минимальной ширине вашей панели.

Если представление с двумя панелями находится в горизонтальном режиме, свойство [WideModeConfiguration](/uwp/api/microsoft.ui.xaml.controls.twopaneview.widemodeconfiguration) определяет, что будет отображено:

| [Значение&nbsp;перечисления](/uwp/api/microsoft.ui.xaml.controls.twopaneviewwidemodeconfiguration) | Описание |
|---------|---------|
| `SinglePane` | Одна панель (как определено свойством `PanePriority`). Панель занимает весь размер `TwoPaneView` (т. е. размер по типу "звезда" в обоих направлениях). |
| `LeftRight` | `Pane1` слева, `Pane2` справа. Обе панели имеют размер по типу "звезда" по вертикали, ширина панели `Pane1` изменяется автоматически, а панели `Pane2` — равна размеру по типу "звезда". |
| `RightLeft` | `Pane1` справа, `Pane2` слева. Обе панели имеют размер по типу "звезда" по вертикали, ширина панели `Pane2` изменяется автоматически, а панели `Pane1` — равна размеру по типу "звезда". |

Значение по умолчанию — `LeftRight`.

| LeftRight | RightLeft |
| - | - |
| ![Представление с двумя панелями, настроенное слева направо](images/two-pane-view/tpv-left-right.png)  | ![Представление с двумя панелями, настроенное справа налево](images/two-pane-view/tpv-right-left.png)  |

> **СОВЕТ.** Если на устройстве используется язык с порядком чтения справа налево (RTL), представление с двумя панелями автоматически меняет порядок: `RightLeft` преобразуется в `LeftRight`, а `LeftRight` — в `RightLeft`.

#### <a name="tall-configuration-options"></a>Параметры конфигурации вертикального режима

Представление с двумя панелями переходит в режим `Tall`, если один дисплей уже значения `MinWideModeWidth` и выше значения `MinTallModeHeight`. Значение по умолчанию — 641px, но вы можете изменить его на любое другое. В общем, вы должны установить это свойство равным минимальной высоте вашей панели.

Если представление с двумя панелями находится в вертикальном режиме, свойство [TallModeConfiguration](/uwp/api/microsoft.ui.xaml.controls.twopaneview.tallmodeconfiguration) определяет, что будет отображено:

| [Значение&nbsp;перечисления](/uwp/api/microsoft.ui.xaml.controls.twopaneviewtallmodeconfiguration) | Описание |
|---------|---------|
| `SinglePane` | Одна панель (как определено свойством `PanePriority`). Панель занимает весь размер `TwoPaneView` (т. е. размер по типу "звезда" в обоих направлениях). |
| `TopBottom` | `Pane1` сверху, `Pane2` снизу. Обе панели имеют размер по типу "звезда" по горизонтали, высота панели `Pane1` изменяется автоматически, а панели `Pane2` — равна размеру по типу "звезда". |
| `BottomTop` | `Pane1` снизу, `Pane2` сверху. Обе панели имеют размер по типу "звезда" по горизонтали, высота панели `Pane2` изменяется автоматически, а панели `Pane1` — равна размеру по типу "звезда". |

Значение по умолчанию — `TopBottom`.

| TopBottom | BottomTop |
| - | - |
| ![Представление с двумя панелями, настроенное сверху](images/two-pane-view/tpv-top-bottom.png)  | ![Представление с двумя панелями, настроенное снизу](images/two-pane-view/tpv-bottom-top.png)  |

#### <a name="special-values-for-minwidemodewidth-and-mintallmodeheight"></a>Специальные значения для MinWideModeWidth и MinTallModeHeight

С помощью свойства `MinWideModeWidth` можно предотвратить переход представления с двумя панелями в горизонтальный режим — просто установите для `MinWideModeWidth` значение [Double.PositiveInfinity](/dotnet/api/system.double.positiveinfinity?view=dotnet-uwp-10.0).

Если для свойства `MinTallModeHeight` задано значение [Double.PositiveInfinity](/dotnet/api/system.double.positiveinfinity?view=dotnet-uwp-10.0), оно не позволит представлению с двумя панелями переходить в вертикальный режим.

Если для `MinTallModeHeight` задано значение 0, оно не позволит представлению с двумя панелями переходить в режим `SinglePane`.

#### <a name="responding-to-mode-changes"></a>Реакция на изменения режима

С помощью свойства [Mode](/uwp/api/microsoft.ui.xaml.controls.twopaneview.mode) только для чтения можно получить текущий режим отображения. Всякий раз, когда представление с двумя панелями меняет отображаемую панель или панели, событие [ModeChanged](/uwp/api/microsoft.ui.xaml.controls.twopaneview.modechanged) возникает до отображения обновленного содержимого. Вы можете обработать событие, чтобы оно реагировало на изменения режима отображения.

> [!TIP]
> Событие `ModeChanged` не происходит при начальной загрузке страницы, поэтому XAML по умолчанию должен представлять пользовательский интерфейс, который должен отображаться при первой загрузке.

Один из способов использования этого события — обновить пользовательский интерфейс приложения, позволив пользователям просматривать все содержимое в режиме `SinglePane`. Например, образец приложения содержит основную панель (изображение) и информационную панель.

![Небольшое изображение примера приложения, открытого в вертикальном режиме](images/two-pane-view/tpv-top-bottom.png)

> _Вертикальный режим_

Если для отображения одной панели достаточно пространства, переместите содержимое панели `Pane2` на панель `Pane1`, позволяя пользователю прокручивать страницу для просмотра всего содержимого. Это выглядит следующим образом:

![Изображение примера приложения на одном экране с прокруткой всего содержимого на одной панели](images/two-pane-view/tpv-single-pane.png)

> _Режим SinglePane_

Помните, что свойства `MinWideModeWidth` и `MinTallModeHeight` определяют изменение режима экрана, поэтому вы можете изменить условия перемещения содержимого между панелями, изменив значения этих свойств.

Ниже приведен код обработчика событий `ModeChanged`, который перемещает содержимое между `Pane1` и `Pane2`. Он также задает [VisualState](/uwp/api/windows.ui.xaml.visualstate), чтобы ограничить ширину изображения в горизонтальном режиме.

```csharp
private void TwoPaneView_ModeChanged(Microsoft.UI.Xaml.Controls.TwoPaneView sender, object args)
{
    // Remove details content from it's parent panel.
    ((Panel)DetailsContent.Parent).Children.Remove(DetailsContent);
    // Set Normal visual state.
    Windows.UI.Xaml.VisualStateManager.GoToState(this, "Normal", true);

    // Single pane
    if (sender.Mode == Microsoft.UI.Xaml.Controls.TwoPaneViewMode.SinglePane)
    {
        // Add the details content to Pane1.
        Pane1StackPanel.Children.Add(DetailsContent);
    }
    // Dual pane.
    else
    {
        // Put details content in Pane2.
        Pane2Root.Children.Add(DetailsContent);

        // If also in Wide mode, set Wide visual state
        // to constrain the width of the image to 2*.
        if (sender.Mode == Microsoft.UI.Xaml.Controls.TwoPaneViewMode.Wide)
        {
            Windows.UI.Xaml.VisualStateManager.GoToState(this, "Wide", true);
        }
    }
}
```

## <a name="dos-and-donts"></a>Рекомендации

- Всегда используйте представление с двумя панелями, позволяя приложению использовать преимущества двух экранов и больших экранов.
- Не помещайте представление с двумя панелями в другое такое же представление.

## <a name="related-articles"></a>Похожие статьи

- [Макет](../layout/index.md)
- [Create apps for dual-screen devices](/dual-screen) (Создание приложений для двухэкранных устройств)
- [Introduction to dual-screen devices](/dual-screen/introduction) (Общие сведения о двухэкранных устройствах)
