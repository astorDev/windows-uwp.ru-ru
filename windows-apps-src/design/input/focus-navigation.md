---
title: Навигация по фокусу без мыши
Description: Узнайте, как использовать навигацию по фокусу для обеспечения полного и согласованного взаимодействия в приложениях Windows и пользовательских элементов управления для опытных пользователей, с ограниченными возможностями и другими требованиями к специальным возможностям, а также с 10-футовыми экранами телепередач и Xbox One.
label: ''
template: detail.hbs
keywords: клавиатура, игровое устройство управления, удаленное управление, навигация, направленная внутренняя навигация, область направления, стратегия навигации, ввод, взаимодействие с пользователем, специальные возможности, удобство использования
ms.date: 03/02/2018
ms.topic: article
pm-contact: miguelrb
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: ad76db55ffb2f6073e565201ec42bebb418289ed
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970139"
---
# <a name="focus-navigation-for-keyboard-gamepad-remote-control-and-accessibility-tools"></a>Перемещение фокуса с помощью клавиатуры, геймпада, пульта дистанционного управления и средств специальных возможностей

![Клавиатура, пульт управления и крестовина](images/dpad-remote/dpad-remote-keyboard.png)

Используйте навигацию по фокусу, чтобы предоставить исчерпывающие и согласованные возможности взаимодействия в приложениях Windows, а также пользовательские элементы управления для опытных пользователей, с ограниченными возможностями и другими требованиями к специальным возможностям, кроме 10-футовых экранов телепередач и Xbox One.

## <a name="overview"></a>Обзор

Навигация по фокусу относится к базовому механизму, который позволяет пользователям перемещаться и взаимодействовать с пользовательским интерфейсом приложения Windows, используя клавиатуру, игровой планшет или пульт дистанционного управления.

> [!NOTE]
> Устройства ввода обычно классифицируются как указывающие устройства, такие как сенсорный ввод, сенсорная панель, перо и мышь, и как неуказывающие устройства, такие как клавиатура, геймпад и пульт дистанционного управления.

В этом разделе описывается Оптимизация приложения Windows и создание пользовательских интерфейсов взаимодействия для пользователей, которые используют неуказывающие типы входных данных. 

Несмотря на то, что мы нацелены на ввод с клавиатуры для пользовательских элементов управления в приложениях Windows на ПК, хорошо спроектированная клавиатура также важна для программных клавиатур, таких как сенсорная клавиатура и экранная клавиатура (ОСК), поддерживающих специальные средства, такие как экранный диктор, и поддержка 10-футового интерфейса.

Инструкции по созданию пользовательских интерфейсов в приложениях Windows для указывающих устройств см. в разделе [ввод указателя на обработку](handle-pointer-input.md) .

Дополнительные общие сведения о создании приложений и возможностей для клавиатуры см. в разделе [Взаимодействие с помощью клавиатуры](keyboard-interactions.md).

## <a name="general-guidance"></a>Общее руководство

Только те элементы пользовательского интерфейса, для которых требуется взаимодействие с пользователем, должны поддерживать перемещение фокуса; элементы, для которых не требуется выполнение действия, например, статические изображения, не требуют поддержки фокуса клавиатуры. Средства чтения с экрана и подобные средства специальных возможностей по-прежнему объявляют эти статические элементы даже в том случае, если они не включены в перемещение фокуса. 

Важно помнить, что, в отличие от навигации с помощью указывающего устройства, например мыши или сенсорного управления, навигация с помощью клавиатуры является линейной. При реализации перемещения фокуса следует оценить, как пользователь будет взаимодействовать с приложением и какова будет логическая навигация. В большинстве случаев мы рекомендуем реализовывать настраиваемое поведение перемещения фокуса в соответствии с предпочтительным шаблоном чтения для языка и региональных параметров пользователя.

Некоторые другие рекомендации по реализации перемещения фокуса включают в себя:

- Элементы управления сгруппированы логически?
- Есть ли группы элементов управления, которые важнее других?
  - Если да, то имеют ли эти группы подгруппы?
- Требуется ли для макета реализация пользовательской направленной навигации (клавиши со стрелками) и формирование последовательности табуляции?

В электронной книге [Разработка программного обеспечения с учетом специальных возможностей](https://www.microsoft.com/download/details.aspx?id=19262) есть глава под названием *Проектирование логической иерархии*, которая посвящена этой теме.

## <a name="2d-directional-navigation-for-keyboard"></a>Двухмерная направленная навигация для клавиатуры

Область навигации элемента управления или группы элементов управления двухмерной внутренней навигации называется "областью направления". При переносе фокуса на этот объект для перехода между дочерними элементами в области направления можно использовать клавиши со стрелками (влево, вправо, вверх и вниз).

![](images/keyboard/directional-area-small.png)
область*переходов 2D-внутренняя область навигации или область направления для группы управления*

Для управления двухмерной внутренней навигацией с помощью клавиш со стрелками на клавиатуре можно использовать свойство [XYFocusKeyboardNavigation](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement#Windows_UI_Xaml_UIElement_XYFocusKeyboardNavigation) (возможные значения [Auto](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.xyfocuskeyboardnavigationmode) (автоматически), [Enabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.xyfocuskeyboardnavigationmode) (включено) или [Disabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.xyfocuskeyboardnavigationmode) (отключено)).

> [!NOTE]
> Это свойство не влияет на последовательность табуляции. Чтобы избежать запутанного взаимодействия при навигации, для дочерних элементов области направления *не рекомендуется* явно задавать значения в порядке перехода по клавише табуляции в приложении. Дополнительные сведения о поведении перехода с помощью клавиши табуляции для элемента см. в разделах, посвященных свойствам [UIElement.TabFocusNavigation](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement#Windows_UI_Xaml_UIElement_TabFocusNavigation) и [TabIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control#Windows_UI_Xaml_Controls_Control_TabIndex).

### <a name="auto-default-behavior"></a>[Автоматически](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.xyfocuskeyboardnavigationmode) (поведение по умолчанию)

При задании свойству значения "Auto" (автоматически) поведение направленной навигации будет основываться на наследственности элемента или иерархии наследования. Если все родительские элементы находятся в режиме по умолчанию (для них задано значение **Auto**), поведение направленной навигации *не* поддерживается для клавиатуры.

### <a name="disabled"></a>[Отключено](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.xyfocuskeyboardnavigationmode)

Задайте свойству **XYFocusKeyboardNavigation** значение **Disabled** (отключено), чтобы заблокировать направленную навигацию к элементу управления и его дочерним элементам.

![Поведение отключенного](images/keyboard/xyfocuskeyboardnav-disabled.gif)
поведения*ксифокускэйбоарднавигатион* ксифокускэйбоарднавигатион Disabled

В этом примере в основной панели [StackPanel](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel) (ContainerPrimary) для свойства **XYFocusKeyboardNavigation** задано значение **Enabled** (включено). Все дочерние элементы наследуют этот параметр и к ним можно перейти с помощью клавиш со стрелками. Однако элементы B3 и B4 находятся во вспомогательной панели [StackPanel](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel) (ContainerSecondary), свойство **XYFocusKeyboardNavigation** которой имеет значение **Disabled** (отключено), что переопределяет основной контейнер и отключает навигацию с помощью клавиш со стрелками на себя, а также между его дочерними элементами.

```XAML
<Grid 
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" 
    TabFocusNavigation="Cycle">
    <Grid.RowDefinitions>
        <RowDefinition Height="40"/>
        <RowDefinition Height="75"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <TextBlock Name="KeyPressed"
                Grid.Row="0" 
                FontWeight="ExtraBold" 
                HorizontalTextAlignment="Center"
                TextWrapping="Wrap" 
                Padding="10" />
    <StackPanel Name="ContainerPrimary" 
                XYFocusKeyboardNavigation="Enabled" 
                KeyDown="ContainerPrimary_KeyDown" 
                Orientation="Horizontal" 
                BorderBrush="Green" 
                BorderThickness="2" 
                Grid.Row="1" 
                Padding="10" 
                MaxWidth="200">
        <Button Name="B1" 
                Content="B1" 
                GettingFocus="Btn_GettingFocus" />
        <Button Name="B2" 
                Content="B2" 
                GettingFocus="Btn_GettingFocus" />
        <StackPanel Name="ContainerSecondary" 
                    XYFocusKeyboardNavigation="Disabled" 
                    Orientation="Horizontal" 
                    BorderBrush="Red" 
                    BorderThickness="2">
            <Button Name="B3" 
                    Content="B3" 
                    GettingFocus="Btn_GettingFocus" />
            <Button Name="B4" 
                    Content="B4" 
                    GettingFocus="Btn_GettingFocus" />
        </StackPanel>
    </StackPanel>
</Grid>
```

### <a name="enabled"></a>[Enabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.xyfocuskeyboardnavigationmode)

Задайте свойству **XYFocusKeyboardNavigation** значение **Enabled** (включено) для включения поддержки двухмерной направленной навигации к элементу управления и к каждому из его дочерних объектов [UIElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement).

При задании этого значения навигация с помощью клавиш со стрелками будет ограничена элементами в области направления. Это не влияет на навигацию клавишей TAB, так как все элементы управления остаются доступны посредством их иерархии в последовательности табуляции.

![Поведение ксифокускэйбоарднавигатион с](images/keyboard/xyfocuskeyboardnav-enabled.gif)
включенным поведением*ксифокускэйбоарднавигатион*

В этом примере в основной панели [StackPanel](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel) (ContainerPrimary) для свойства **XYFocusKeyboardNavigation** задано значение **Enabled** (включено). Все дочерние элементы наследуют этот параметр и к ним можно перейти с помощью клавиш со стрелками. Элементы B3 и B4 находятся во вспомогательной панели [StackPanel](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel) (ContainerSecondary), в которой свойству **XYFocusKeyboardNavigation** значение не задано и которое затем будет наследовать значение параметра основного контейнера. Элемент B5 не находится внутри объявленной области направления и не поддерживает навигацию с помощью клавиш со стрелками, однако поддерживает стандартное поведение навигации с помощью клавиши TAB.

```xaml
<Grid
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}"
    TabFocusNavigation="Cycle">
    <Grid.RowDefinitions>
        <RowDefinition Height="40"/>
        <RowDefinition Height="100"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <TextBlock Name="KeyPressed"
               Grid.Row="0"
               FontWeight="ExtraBold"
               HorizontalTextAlignment="Center"
               TextWrapping="Wrap"
               Padding="10" />
    <StackPanel Grid.Row="1"
                Orientation="Horizontal"
                HorizontalAlignment="Center">
        <StackPanel Name="ContainerPrimary"
                    XYFocusKeyboardNavigation="Enabled"
                    KeyDown="ContainerPrimary_KeyDown"
                    Orientation="Horizontal"
                    BorderBrush="Green"
                    BorderThickness="2"
                    Padding="5" Margin="5">
            <Button Name="B1"
                    Content="B1"
                    GettingFocus="Btn_GettingFocus" Margin="5" />
            <Button Name="B2"
                    Content="B2"
                    GettingFocus="Btn_GettingFocus" />
            <StackPanel Name="ContainerSecondary"
                        Orientation="Horizontal"
                        BorderBrush="Red"
                        BorderThickness="2"
                        Margin="5">
                <Button Name="B3"
                        Content="B3"
                        GettingFocus="Btn_GettingFocus"
                        Margin="5" />
                <Button Name="B4"
                        Content="B4"
                        GettingFocus="Btn_GettingFocus"
                        Margin="5" />
            </StackPanel>
        </StackPanel>
        <Button Name="B5"
                Content="B5"
                GettingFocus="Btn_GettingFocus"
                Margin="5" />
    </StackPanel>
</Grid>
```

Может быть несколько уровней вложенных областей направления. Если во всех родительских элементах для параметра XYFocusKeyboardNavigation установлено значение Enabled (включено), границы области внутренней навигации игнорируются.

Вот пример двух вложенных областей направления в элементе, который явным образом не поддерживает двухмерную направленную навигацию. В этом случае направленная навигация между двумя вложенными областями не поддерживается.

![Включенные функции ксифокускэйбоарднавигатион и](images/keyboard/xyfocuskeyboardnav-enabled-nested1.gif)
вложенное поведение*ксифокускэйбоарднавигатион включены и вложенное поведение*

Ниже приведен более сложный пример трех вложенных областей направления, где:

-   Если фокус на элементе B1, можно перейти только к элементу B5 (и наоборот), так как граница области направления, для которой свойству XYFocusKeyboardNavigation задано значение Disabled (отключено), делает недостижимыми элементы B2, B3 и B4 с помощью клавиш со стрелками
-   Если фокус на элементе B2, можно перейти только к элементу B3 (и наоборот), так как граница области направления делает недостижимыми элементы B1, B4 и B5 с помощью клавиш со стрелками
-   Когда фокус на элементе B4, для перехода между элементами управления необходимо использовать клавишу TAB

![Сложное вложенное поведение XYFocusKeyboardNavigation со значением "Включено"](images/keyboard/xyfocuskeyboardnav-enabled-nested2.gif)

*Сложное вложенное поведение XYFocusKeyboardNavigation со значением "Включено"*

## <a name="tab-navigation"></a>Навигация с помощью клавиши TAB

Хотя клавиши со стрелками можно использовать для двухмерной навигации витин элемента управления или группы управления, клавишу TAB можно использовать для перехода между всеми элементами управления в приложении Windows. 

По умолчанию все интерактивные элементы управления поддерживают навигацию с помощью клавиши TAB (значением свойств [IsEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control#Windows_UI_Xaml_Controls_Control_IsEnabled) и [IsTabStop](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control#Windows_UI_Xaml_Controls_Control_IsTabStop) является **true**), при этом логическая последовательность табуляции извлекается и макета элемента управления приложения. Тем не менее порядок по умолчанию не обязательно совпадает с визуальным порядком. Фактическое расположение на экране может зависеть от родительского контейнера макета и определенных свойств, которые можно установить для дочерних элементов, чтобы изменить макет.

Избегайте пользовательских остановок перехода, из-за которых фокус хаотично перемещается по элементам приложения. Например, список элементов управления в пользовательском интерфейсе, подобном форме, должен иметь последовательность табуляции сверху вниз и слева направо (в зависимости от региона).

В этом разделе мы расскажем, как эту последовательность табуляции можно полностью настроить в соответствии с особенностями вашего приложения.

### <a name="set-the-tab-navigation-behavior"></a>Настройка навигации с использованием клавиши TAB

Свойство [TabFocusNavigation](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement#Windows_UI_Xaml_UIElement_TabFocusNavigation) элемента [UIElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) определяет навигацию с использованием клавиши табуляции для всего дерева объектов (или области направления).

> [!NOTE]
> Используйте это свойство вместо свойства [Control.TabNavigation](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control#Windows_UI_Xaml_Controls_Control_TabNavigation) для объектов, которые не используют [ControlTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.controltemplate) для определения их внешнего вида.

Как было сказано в предыдущем разделе, чтобы избежать запутанного взаимодействия при навигации, для дочерних элементов области направления *не рекомендуется* явно задавать значения в порядке перехода по клавише табуляции в приложении. Дополнительные сведения о поведении перехода с помощью клавиши табуляции для элемента см. в разделах, посвященных свойствам [UIElement.TabFocusNavigation](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement#Windows_UI_Xaml_UIElement_TabFocusNavigation) и [TabIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control#Windows_UI_Xaml_Controls_Control_TabIndex).   
> Для версий ОС выше Windows 10 Creators Update (сборка 10.0.15063) параметры табуляции ограничены объектами [ControlTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.controltemplate). Дополнительные сведения см. в разделе [Control.TabNavigation](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control#Windows_UI_Xaml_Controls_Control_TabNavigation).

Свойство [TabFocusNavigation](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement#Windows_UI_Xaml_UIElement_TabFocusNavigation) имеет значение типа [KeyboardNavigationMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardnavigationmode) и следующие возможные значения (обратите внимание, что эти примеры не являются пользовательскими группами элементов управления и для них не требуется поддержка внутренней навигации с помощью клавиш со стрелками):

- **Local** (по умолчанию)   
  Индексы перехода по TAB распознаются на локальном поддереве внутри контейнера. В этом примере последовательность табуляции следующая: B1, B2, B3, B4, B5, B6, B7, B1.

   ![Поведение навигации с помощью клавиши TAB в режиме "Local"](images/keyboard/tabnav-local.gif)

   *Поведение навигации с помощью клавиши TAB в режиме "Local"*

- **После**  
  Контейнер и все дочерние элементы один раз получают фокус. В этом примере последовательность табуляции следующая: B1, B2, B7, B1 (также показана внутренняя навигация с помощью клавиш со стрелками).

   ![Поведение навигации с помощью клавиши TAB в режиме "Once"](images/keyboard/tabnav-once.gif)

   *Поведение навигации с помощью клавиши TAB в режиме "Once"*

- **Жизненно**   
  Фокус циклически возвращается к начальному фокусируемому элементу внутри контейнера. В этом примере последовательность табуляции следующая: B1, B2, B3, B4, B5, B6, B2...

   ![Поведение навигации с помощью клавиши TAB в режиме "Cycle"](images/keyboard/tabnav-cycle.gif)

   *Поведение навигации с помощью клавиши TAB в режиме "Cycle"*

Вот пример кода для приведенных выше примеров (режимом TabFocusNavigation является "Cycle").

```XAML
<Grid 
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" 
    TabFocusNavigation="Cycle">
    <Grid.RowDefinitions>
        <RowDefinition Height="40"/>
        <RowDefinition Height="300"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <TextBlock Name="KeyPressed"
               Grid.Row="0" 
               FontWeight="ExtraBold" 
               HorizontalTextAlignment="Center"
               TextWrapping="Wrap" 
               Padding="10" />
    <StackPanel Name="ContainerPrimary"
                KeyDown="Container_KeyDown" 
                Orientation="Horizontal" 
                HorizontalAlignment="Center"
                BorderBrush="Green" 
                BorderThickness="2" 
                Grid.Row="1" 
                Padding="10" 
                MaxWidth="200">
        <Button Name="B1" 
                Content="B1" 
                GettingFocus="Btn_GettingFocus" 
                Margin="5"/>
        <StackPanel Name="ContainerSecondary" 
                    KeyDown="Container_KeyDown"
                    XYFocusKeyboardNavigation="Enabled" 
                    TabFocusNavigation ="Cycle"
                    Orientation="Vertical" 
                    VerticalAlignment="Center"
                    BorderBrush="Red" 
                    BorderThickness="2"
                    Padding="5" Margin="5">
            <Button Name="B2" 
                    Content="B2" 
                    GettingFocus="Btn_GettingFocus" 
                    Margin="5"/>
            <Button Name="B3" 
                    Content="B3" 
                    GettingFocus="Btn_GettingFocus" 
                    Margin="5"/>
            <Button Name="B4" 
                    Content="B4" 
                    GettingFocus="Btn_GettingFocus" 
                    Margin="5"/>
            <Button Name="B5" 
                    Content="B5" 
                    GettingFocus="Btn_GettingFocus" 
                    Margin="5"/>
            <Button Name="B6" 
                    Content="B6" 
                    GettingFocus="Btn_GettingFocus" 
                    Margin="5"/>
        </StackPanel>
        <Button Name="B7" 
                Content="B7" 
                GettingFocus="Btn_GettingFocus" 
                Margin="5"/>
    </StackPanel>
</Grid>
```

### <a name="tabindex"></a>[TabIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control#Windows_UI_Xaml_Controls_Control_TabIndex)

Используйте свойство [TabIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control#Windows_UI_Xaml_Controls_Control_TabIndex), чтобы задать порядок, в котором элементы будут получать фокус, когда пользователь будет переходить по элементам управления с помощью клавиши TAB. Элемент управления с более низким порядком перехода по клавише TAB получает фокус до того, как его получает элемент управления с более высоким порядком.

Когда для элемента управления не задано свойство [TabIndex](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control#Windows_UI_Xaml_Controls_Control_TabIndex), ему назначается значение порядка выше текущего наибольшего значения (и самый низкий приоритет) всех интерактивных элементов управления в визуальном дереве на основе области. 

Все дочерние элементы элемента управления считаются областью, и если у одного из них есть свои дочерние элементы, они считаются другой областью. При выборе первого элемента визуального дерева области устраняется неясность. 

Чтобы исключить элемент управления из последовательности табуляции, установите для свойства [IsTabStop](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control#Windows_UI_Xaml_Controls_Control_IsTabStop) значение **false**.

Переопределите последовательность табуляции по умолчанию, задав свойство [TabIndex](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control#Windows_UI_Xaml_Controls_Control_TabIndex).

> [!NOTE] 
> Свойство [TabIndex](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control#Windows_UI_Xaml_Controls_Control_TabIndex) работает одинаково как с [UIElement.TabFocusNavigation](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement#Windows_UI_Xaml_UIElement_TabFocusNavigation), так и с [Control.TabNavigation](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control#Windows_UI_Xaml_Controls_Control_TabNavigation).


Здесь мы покажем, как свойство [TabIndex](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control#Windows_UI_Xaml_Controls_Control_TabIndex) может влиять на перемещение фокуса по определенным элементам. 

![Навигация с помощью клавиши TAB в режиме "Local" и поведение TabIndex](images/keyboard/tabnav-tabindex.gif)

*Навигация с помощью клавиши TAB в режиме "Local" и поведение TabIndex*

В предыдущем примере есть две области: 
- B1,область направления (B2–B6) и B7
- область направления (B2–B6)

Когда элемент B3 (в области направления) получает фокус, область изменяется и навигация с помощью клавиши табуляции переходит в область направления, в которой определен лучший вариант для последующей установки фокуса. В данном случае за элементом B2 следуют элементы B4, B5 и B6. Затем область изменяется снова и фокус перемещается на элемент B1.

Вот код для этого примера.

```xaml
<Grid
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}"
    TabFocusNavigation="Cycle">
    <Grid.RowDefinitions>
        <RowDefinition Height="40"/>
        <RowDefinition Height="300"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <TextBlock Name="KeyPressed"
               Grid.Row="0"
               FontWeight="ExtraBold"
               HorizontalTextAlignment="Center"
               TextWrapping="Wrap"
               Padding="10" />
    <StackPanel Name="ContainerPrimary"
                KeyDown="Container_KeyDown"
                Orientation="Horizontal"
                HorizontalAlignment="Center"
                BorderBrush="Green"
                BorderThickness="2"
                Grid.Row="1"
                Padding="10"
                MaxWidth="200">
        <Button Name="B1"
                Content="B1"
                TabIndex="1"
                ToolTipService.ToolTip="TabIndex = 1"
                GettingFocus="Btn_GettingFocus"
                Margin="5"/>
        <StackPanel Name="ContainerSecondary"
                    KeyDown="Container_KeyDown"
                    TabFocusNavigation ="Local"
                    Orientation="Vertical"
                    VerticalAlignment="Center"
                    BorderBrush="Red"
                    BorderThickness="2"
                    Padding="5" Margin="5">
            <Button Name="B2"
                    Content="B2"
                    GettingFocus="Btn_GettingFocus"
                    Margin="5"/>
            <Button Name="B3"
                    Content="B3"
                    TabIndex="3"
                    ToolTipService.ToolTip="TabIndex = 3"
                    GettingFocus="Btn_GettingFocus"
                    Margin="5"/>
            <Button Name="B4"
                    Content="B4"
                    GettingFocus="Btn_GettingFocus"
                    Margin="5"/>
            <Button Name="B5"
                    Content="B5"
                    GettingFocus="Btn_GettingFocus"
                    Margin="5"/>
            <Button Name="B6"
                    Content="B6"
                    GettingFocus="Btn_GettingFocus"
                    Margin="5"/>
        </StackPanel>
        <Button Name="B7"
                Content="B7"
                TabIndex="2"
                ToolTipService.ToolTip="TabIndex = 2"
                GettingFocus="Btn_GettingFocus"
                Margin="5"/>
    </StackPanel>
</Grid>
```

## <a name="2d-directional-navigation-for-keyboard-gamepad-and-remote-control"></a>Двухмерная направленная навигация для клавиатуры, геймпада и пульта дистанционного управления

Входные типы, отличные от указателя, такие как клавиатура, игровой планшет, пульт дистанционного управления и специальные возможности, такие как экранный диктор Windows, совместно используют общий, базовый механизм для навигации и взаимодействия с пользовательским интерфейсом приложения Windows.

В этом разделе мы расскажем, как задать предпочтительную стратегию навигации и настроить перемещение фокуса внутри приложения с помощью набора свойств стратегии навигации, которые поддерживают все типы ввода без указателя на основе фокуса.

Дополнительные сведения о создании приложений и интерфейсах для Xbox/TV см. в статье [взаимодействие с клавиатурой](keyboard-interactions.md), [проектирование для Xbox и телепередач](../devices/designing-for-tv.md), а затем — [планшет и пульт дистанционного управления](gamepad-and-remote-interactions.md).

### <a name="navigation-strategies"></a>Стратегии навигации

> Стратегии навигации применимы к клавиатуре, геймпаду, пульту дистанционного управления и различным средствам специальных возможностей.

Следующие свойства стратегии навигации позволяют влиять на то, какой элемент управления будет получать фокус в зависимости от того, какая клавиша нажата: клавиша со стрелкой, крестовина и аналогичные клавиши. 

-   XYFocusUpNavigationStrategy
-   XYFocusDownNavigationStrategy
-   XYFocusLeftNavigationStrategy
-   XYFocusRightNavigationStrategy

Возможные значения этих свойств: [Auto](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.xyfocusnavigationstrategy) (по умолчанию), [NavigationDirectionDistance](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.xyfocusnavigationstrategy), [Projection](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.xyfocusnavigationstrategy) или [RectilinearDistance ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.xyfocusnavigationstrategy).

Если установлено значение **Auto**, поведение элемента основано на его родительских элементах. Если для всех элементов установлено значение **Auto**, используется **Projection**.

> [!NOTE]
> Другие факторы, например ранее сфокусированный элемент или приближение к оси направления навигации, могут повлиять на результат.

### <a name="projection"></a>Прогнозирование

Стратегия Projection перемещает фокус на первый элемент, встреченный при *проецировании* края текущего элемента, имеющего фокус, в направлении навигации.

В этом примере каждому направлению перемещения фокуса задано значение Projection. Обратите внимание, как фокус перемещается вниз от элемента B1 к элементу B4, минуя B3. Это происходит потому, что элемент B3 не находится в зоне проекции. Также обратите внимание, как кандидат для фокуса не определяется при перемещении слева от элемента B1. Это происходит потому, что положение элемента B2 относительно элемента B1 исключает элемент B3 в качестве кандидата. Если элемент B3 находился в одной строке с элементом B2, он будет подходящим кандидатом для навигации влево. B2 является подходящим кандидатом из-за его близкого расположения к оси направления навигации.

![Стратегия навигации проекции](images/keyboard/xyfocusnavigationstrategy-projection.gif)

*Стратегия навигации проекции*

### <a name="navigationdirectiondistance"></a>NavigationDirectionDistance

Стратегия NavigationDirectionDistance перемещает фокус на элемент, наиболее близкий к оси направления навигации.

Граница ограничивающего прямоугольника, соответствующая направлению навигации, *расширяется* и *проецируется* для указания целевых объектов-кандидатов. Первый обнаруженный элемент считается целевым. При наличии нескольких кандидатов целевым элементом становится самый ближний из них. Если кандидатов по-прежнему несколько, кандидатом становится самый верхний и самый левый элемент.

![Стратегия навигации NavigationDirectionDistance](images/keyboard/xyfocusnavigationstrategy-navigationdirectiondistance.gif)

*Стратегия навигации NavigationDirectionDistance*

### <a name="rectilineardistance"></a>RectilinearDistance

Стратегия RectilinearDistance перемещает фокус на ближайший элемент, который определяется по двухмерному расстоянию по прямой ([Расстояние городских кварталов](https://en.wikipedia.org/wiki/Taxicab_geometry)).

Сумма основного и дополнительного расстояния до каждого потенциального кандидата используется для определения наиболее подходящего кандидата. При одинаковом результате выбирается первый элемент слева, если запрашиваемым направлением является вверх или вниз, и первый элемент вверху, если запрашиваемым направлением является влево или вправо.

![Стратегия навигации RectilinearDistance](images/keyboard/xyfocusnavigationstrategy-rectilineardistance.gif)

*Стратегия навигации RectilinearDistance*

На этом изображении показано, как RectilinearDistance (B3) становится кандидатом для фокуса, когда фокус установлен на элементе B1 и запрашиваемым направлением является "вниз". Для этого используются следующие расчеты:
-   Расстояние (B1, B3, вниз) = 10 + 0 = 10
-   Расстояние (B1, B2, вниз) = 0 + 40 = 30
-   Расстояние (B1, D, вниз) = 30 + 0 = 30


## <a name="related-articles"></a>Похожие статьи
- [Программное перемещение фокуса](focus-navigation-programmatic.md)
- [Взаимодействие с клавиатурой](keyboard-interactions.md)
- [Специальные возможности клавиатуры](../accessibility/keyboard-accessibility.md) 



