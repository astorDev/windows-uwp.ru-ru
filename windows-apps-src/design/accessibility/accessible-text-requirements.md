---
Description: В этой статье приводятся рекомендации относительно специальных возможностей отображения текста в приложении, которые помогают обеспечить необходимый уровень контрастности между цветами текста и фона.
ms.assetid: BA689C76-FE68-4B5B-9E8D-1E7697F737E6
title: Требования к специальным возможностям отображения текста
label: Accessible text requirements
template: detail.hbs
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 8b566b1960435a856b82a7be605ef5e1f7ba86e2
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74257794"
---
# <a name="accessible-text-requirements"></a>Требования к специальным возможностям отображения текста  




В этой статье приводятся рекомендации относительно специальных возможностей отображения текста в приложении, которые помогают обеспечить необходимый уровень контрастности между цветами текста и фона. Кроме того, в статье описываются роли модели автоматизации пользовательского интерфейса Майкрософт, которые могут быть присущи элементам текста в приложениях универсальной платформы Windows (UWP) на языках программирования C++, C# и Visual Basic, а также рекомендации относительно использования текста в графике.

<span id="contrast_rations"/>
<span id="CONTRAST_RATIONS"/>

## <a name="contrast-ratios"></a>Коэффициенты контрастности  
Хотя у пользователей всегда есть возможность переключиться в режим высокой контрастности, с точки зрения дизайна текста в вашем приложении её стоит рассматривать как крайний шаг. Имеет смысл удостовериться, что текст вашего приложения соответствует рекомендациям по уровню контрастности между текстом и фоном. Оценка уровня контрастности основана на детерминированных методах, которые не учитывают оттенок цвета. Например, если у вас имеется красный текст на зеленом фоне, то этот текст может оказаться нечитабельным для людей, страдающих дальтонизмом. Проверка и исправление коэффициента контрастности поможет избежать подобных проблем.

За основу приведенных здесь рекомендаций по контрастности текста взяты стандарты специальных возможностей в Интернете [G18: Ensuring that a contrast ratio of at least 4.5:1 exists between text (and images of text) and background behind the text](https://www.w3.org/TR/WCAG20-TECHS/G18.html). Данное руководство приведено в спецификации консорциума W3C *Методики для WCAG 2.0*.

Чтобы видимый текст соответствовал требованиям специальных возможностей, коэффициент его контрастности должен составлять не менее 4,5:1 по отношению к фону. В число исключений входят логотипы и несущественный текст, например текст, являющийся частью неактивного компонента пользовательского интерфейса.

Исключается также декоративный текст и текст, не несущий информации. Например, если для создания фона используются случайные слова, которые можно переставить или заменить без изменения смысла, эти слова считаются декоративными и могут не соответствовать этому критерию.

Проверьте допустимость контрастности видимого текста с помощью средств измерения цветового контраста. Сведения о средствах измерения коэффициента контрастности см. в разделе [Методики WCAG 2.0 G18 (Раздел ресурсов)](https://www.w3.org/TR/WCAG20-TECHS/G18.html#G18-resources).

> [!NOTE]
> Некоторые средства, перечисленные в методиках для WCAG 2.0 G18, нельзя интерактивно использовать с приложениями UWP. Вам может понадобиться вручную ввести значения цвета фона и переднего плана в средство или же сделать снимки экрана для пользовательского интерфейса вашего приложения и применить к ним средство измерения коэффициента контрастности.

<span id="Text_element_roles"/>
<span id="text_element_roles"/>
<span id="TEXT_ELEMENT_ROLES"/>

## <a name="text-element-roles"></a>Роли текстового элемента  
Приложение UWP может использовать следующие элементы по умолчанию (обычно они называются *текстовыми элементами* или *элементами управления Textedit*):

* [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock): role является [ **текстом**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType)
* [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox): роль [ **редактируется**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType)
* [**RichTextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock) (и класс Overflow [**ричтекстблокковерфлов**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richtextblockoverflow)): роль является [**текстом**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType)
* [**Ричедитбокс**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichEditBox): роль [ **редактируется**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType)

Когда элемент управления сообщает, что имеет роль [**Правка**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType), специальные возможности предполагают наличие у пользователей способов изменения этих значений. Поэтому если вы поместите статический текст в элемент [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox), вы неправильно сообщите пользователю специальных возможностей о роли (а значит, и о структуре) вашего приложения.

В текстовых моделях XAML существует два элемента, которые в основном используются для статического текста: [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) и [**RichTextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock). Ни один из них не относится к подклассу [**Control**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control), и поэтому они не поддерживают перемещение фокуса с помощью клавиатуры и не могут отображаться в последовательности табуляции. Но это не означает, что специальные возможности не могут их считывать. Средства чтения с экрана обычно рассчитаны на поддержку нескольких режимов чтения содержимого в приложении, включая специальный режим чтения или шаблоны навигации, которые не работают с фокусом и последовательностью табуляции, например "виртуальный курсор". Поэтому не стоит размещать статический текст в контейнерах с поддержкой фокуса только затем, чтобы пользователь мог попадать в них с помощью последовательности табуляции. Пользователи специальных возможностей ожидают, что со всеми элементами в последовательности табуляции можно взаимодействовать, и если они столкнутся со статическим текстом, это скорее запутает их, чем поможет. Вы должны самостоятельно провести тестирование с помощью экранного диктора, чтобы оценить взаимодействие пользователя с приложением при использовании средства чтения с экрана для проверки статического текста приложения.

<span id="Auto-suggest_accessibility"/>
<span id="auto-suggest_accessibility"/>
<span id="AUTO-SUGGEST_ACCESSIBILITY"/>

## <a name="auto-suggest-accessibility"></a>Доступность автозаполнения  
Если пользователь вводит данные в поле и появляется список предложений, это называется автозаполнением. Такая функция используется для поля **Кому:** сообщения, поля поиска Кортаны в Windows, поля ввода URL-адреса в Microsoft Edge, поля ввода расположения в приложении "Погода" и т. д. Если вы используете XAML-элемент [**AutosuggestBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox) или встроенные элементы управления HTML, эта функция уже реализована по умолчанию. Чтобы предоставить доступ к этой функции, необходимо связать поле ввода и список. Это описано в разделе [Реализация автозаполнения](#implementing_auto-suggest).

Экранный диктор был обновлен, чтобы использовать эту функцию со специальным режимом предложений. Если поле и список правильно связаны, пользователи:

* будут знать о наличии списка и его закрытии;
* будут знать о количестве доступных предложений;
* будут знать выбранный элемент, если имеется;
* смогут переместить фокус экранного диктора на список;
* смогут переходить по вариантам с помощью всех других режимов чтения.

![список предложений](images/autosuggest-list.png)<br/>
_Пример списка предложений_

<span id="Implementing_auto-suggest"/>
<span id="implementing_auto-suggest"/>
<span id="IMPLEMENTING_AUTO-SUGGEST"/>

### <a name="implementing-auto-suggest"></a>Реализация автозаполнения  
Чтобы предоставить доступ к этой функции, необходимо связать поле ввода и список в дереве автоматизации пользовательского интерфейса (UIA). Это происходит с помощью свойства [UIA_ControllerForPropertyId](https://msdn.microsoft.com/windows/desktop/ee684017) для классических приложений или свойства [ControlledPeers](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.automationproperties.getcontrolledpeers) для приложений UWP.

На высоком уровне существует два типа автозаполнения.

**Выбор по умолчанию**  
Если в списке есть элемент, выбранный по умолчанию, экранный диктор ждет событие [**UIA_SelectionItem_ElementSelectedEventId**](https://docs.microsoft.com/windows/desktop/WinAuto/uiauto-event-ids) в классическом приложении или событие [**AutomationEvents.SelectionItemPatternOnElementSelected**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.peers.automationevents) в приложении UWP. Каждый раз, когда меняется выбранный элемент, пользователь вводит другую букву, варианты обновляются или пользователь переходит по списку, срабатывает событие **ElementSelected**.

![списка с выбором по умолчанию](images/autosuggest-default-selection.png)<br/>
_Пример выбора по умолчанию_

**Нет выбора по умолчанию**  
Если элемент по умолчанию не выбран, как в поле расположения приложения "Погода", экранный диктор ждет событие [**UIA_LayoutInvalidatedEventId**](https://docs.microsoft.com/windows/desktop/WinAuto/uiauto-event-ids) или [**LayoutInvalidated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.peers.automationevents) для UWP, которое инициируется при каждом обновлении списка.

![списка без выбора по умолчанию](images/autosuggest-no-default-selection.png)<br/>
_Пример, в котором нет выбора по умолчанию_

### <a name="xaml-implementation"></a>Реализация XAML  
Если вы используете XAML-элемент [**AutosuggestBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox) по умолчанию, то все уже реализовано. Если вы реализуете собственную функцию автозаполнения с помощью [**TextBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox) и списка, необходимо настроить его как [**AutomationProperties.ControlledPeers**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.automationproperties.getcontrolledpeers) в элементе **TextBox**. Необходимо вызывать событие **AutomationPropertyChanged** свойства [**ControlledPeers**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.automationproperties.getcontrolledpeers) при каждом добавлении и удалении этого свойства, а также вызывать собственные события [**SelectionItemPatternOnElementSelected**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.peers.automationevents) или [**LayoutInvalidated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.peers.automationevents) в зависимости от типа сценария, описанного ранее.

### <a name="html-implementation"></a>Реализация HTML  
Если вы применяете встроенные элементы управления в HTML, то модель автоматизации пользовательского интерфейса уже реализована. Ниже приведен пример автоматической реализации.

``` HTML
<label>Sites <input id="input1" type="text" list="datalist1" /></label>
<datalist id="datalist1">
        <option value="http://www.google.com/" label="Google"></option>
        <option value="http://www.reddit.com/" label="Reddit"></option>
</datalist>
```

 Если вы создаете собственные элементы управления, вам придется настроить свои управления ARIA, которые описаны в стандартах W3C.

<span id="Text_in_graphics"/>
<span id="text_in_graphics"/>
<span id="TEXT_IN_GRAPHICS"/>

## <a name="text-in-graphics"></a>Текст в графике

По возможности избегайте включения текста в графику. Например, любой текст, помещаемый в файл источника изображения, который воспроизводится в приложении в качестве элемента [**Image**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image), не является автоматически доступным (или доступным для чтения) для специальных возможностей. Если обязательно использовать текст в графике, убедитесь, что значение [**AutomationProperties.Name**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.name), которое вы предоставляете как эквивалент замещающему тексту, содержит нужный текст или краткую сводку о смысле текста. Те же принципы актуальны при создании текстовых символов из векторов как часть [**пути**](/uwp/api/Windows.UI.Xaml.Shapes.Path) или с использованием [**глифов**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Glyphs).

<span id="Text_font_size"/>
<span id="text_font_size"/>
<span id="TEXT_FONT_SIZE"/>

## <a name="text-font-size-and-scale"></a>Размер и масштаб шрифта текста

Пользователи могут испытывать трудности при чтении текста в приложении, если шрифты используются слишком маленьким шрифтом, поэтому убедитесь, что любой текст в приложении имеет приемлемый размер.

После того как очевидно, Windows включает различные средства и параметры специальных возможностей, которые пользователи могут воспользоваться преимуществами и настроить в соответствии с собственными потребностями и предпочтениями для чтения текста. К ним можно отнести следующие.

* Средство экранной лупы, увеличивающее выделенную область пользовательского интерфейса. Следует убедиться, что макет текста в приложении не затрудняет использование экранной лупы для чтения.
* Глобальные параметры масштабирования и разрешения в **параметрах — > System-> отобразить — > масштаб и макет**. В точности доступные параметры размера могут зависеть от возможностей устройства вывода.
* Параметры размера текста в **параметрах — > простоту в доступе >** . Измените значение параметра увеличить **текст** , чтобы указать только размер текста в вспомогательных элементах управления во всех приложениях и экранах (все элементы управления "текст UWP" поддерживают возможности масштабирования текста без каких-либо настроек или шаблонов). 
> [!NOTE]
> Параметр **сделать все больше** позволяет пользователю указать предпочтительный размер текста и приложений в целом на основном экране.

Разные текстовые элементы и элементы управления имеют свойство [**IsTextScaleFactorEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.istextscalefactorenabled). По умолчанию оно имеет значение **true**. Если **значение равно true**, размер текста в этом элементе можно масштабировать. Масштабирование влияет на текст, имеющий малый размер **шрифта (FontSize** ) в большую степень, чем влияет на текст, имеющий большой размер **шрифта**. Можно отключить автоматическое изменение размера, задав свойству **истекстскалефакторенаблед** элемента **значение false**. 

Дополнительные сведения см. в разделе [масштабирование текста](https://docs.microsoft.com/windows/uwp/design/input/text-scaling) .

Добавьте следующую разметку в приложение и запустите ее. Настройте параметр **размера текста** и посмотрите, что происходит с каждым **TextBlock**.

XAML
```xml
<TextBlock Text="In this case, IsTextScaleFactorEnabled has been left set to its default value of true."
    Style="{StaticResource BodyTextBlockStyle}"/>

<TextBlock Text="In this case, IsTextScaleFactorEnabled has been set to false."
    Style="{StaticResource BodyTextBlockStyle}" IsTextScaleFactorEnabled="False"/>
```  

Не рекомендуется отключать масштабирование текста, так как универсальное масштабирование текста в пользовательском интерфейсе для всех приложений является важным специальным интерфейсом для пользователей.

Можно также воспользоваться событием [**TextScaleFactorChanged**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.uisettings.textscalefactorchanged) и свойством [**TextScaleFactor**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.uisettings.textscalefactor), чтобы узнать об изменениях настройки **размер шрифта** на телефоне. Выполните следующие действия:

C#
```csharp
{
    ...
    var uiSettings = new Windows.UI.ViewManagement.UISettings();
    uiSettings.TextScaleFactorChanged += UISettings_TextScaleFactorChanged;
    ...
}

private async void UISettings_TextScaleFactorChanged(Windows.UI.ViewManagement.UISettings sender, object args)
{
    var messageDialog = new Windows.UI.Popups.MessageDialog(string.Format("It's now {0}", sender.TextScaleFactor), "The text scale factor has changed");
    await messageDialog.ShowAsync();
}
```

Значение **текстскалефактор** является типом Double в диапазоне \[1, 2,25\]. Самый маленький текст масштабируется по этому значению. С помощью этого значения можно, например, масштабировать графику в соответствии с текстом. Однако помните, что не весь текст масштабируется с одинаковым коэффициентом. В целом чем больше текст, тем меньше он подвергается масштабированию.

Данные типы имеют свойство **IsTextScaleFactorEnabled**:  
* [**ContentPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentPresenter)
* [**Элементы управления**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control) и производные классы
* [**фонтикон**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.FontIcon)
* [**RichTextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock)
* [**Элемента**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock)
* [**TextElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.TextElement) и производные классы

<span id="related_topics"/>

## <a name="related-topics"></a>См. также  

* [Масштабирование текста](https://docs.microsoft.com/windows/uwp/design/input/text-scaling)
* [Специальные возможности](accessibility.md)
* [Основные сведения о специальных возможностях](basic-accessibility-information.md)
* [Пример вывода текста XAML](https://code.msdn.microsoft.com/windowsapps/XAML-text-display-sample-2593ba0a)
* [Пример редактирования текста XAML](https://code.msdn.microsoft.com/windowsapps/XAML-text-editing-sample-fb0493ad)
* [Пример специальных возможностей XAML](https://code.msdn.microsoft.com/windowsapps/XAML-accessibility-sample-d63e820d) 
