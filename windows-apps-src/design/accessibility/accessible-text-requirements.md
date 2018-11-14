---
author: Xansky
Description: This topic describes best practices for accessibility of text in an app, by assuring that colors and backgrounds satisfy the necessary contrast ratio.
ms.assetid: BA689C76-FE68-4B5B-9E8D-1E7697F737E6
title: Требования к специальным возможностям отображения текста
label: Accessible text requirements
template: detail.hbs
ms.author: mhopkins
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 39979ed3fa2fcd85cbf1f1b73d7c37b2dce38f20
ms.sourcegitcommit: f2c9a050a9137a473f28b613968d5782866142c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2018
ms.locfileid: "6280974"
---
# <a name="accessible-text-requirements"></a>Требования к специальным возможностям отображения текста  




В этой статье приводятся рекомендации относительно специальных возможностей отображения текста в приложении, которые помогают обеспечить необходимый уровень контрастности между цветами текста и фона. Кроме того, в статье описываются роли модели автоматизации пользовательского интерфейса Майкрософт, которые могут быть присущи элементам текста в приложениях универсальной платформы Windows (UWP) на языках программирования C++, C# и Visual Basic, а также рекомендации относительно использования текста в графике.

<span id="contrast_rations"/>
<span id="CONTRAST_RATIONS"/>

## <a name="contrast-ratios"></a>Коэффициенты контрастности  
Хотя у пользователей всегда есть возможность переключиться в режим высокой контрастности, с точки зрения дизайна текста в вашем приложении её стоит рассматривать как крайний шаг. Имеет смысл удостовериться, что текст вашего приложения соответствует рекомендациям по уровню контрастности между текстом и фоном. Оценка уровня контрастности основана на детерминированных методах, которые не учитывают оттенок цвета. Например, если у вас имеется красный текст на зеленом фоне, то этот текст может оказаться нечитабельным для людей, страдающих дальтонизмом. Проверка и исправление коэффициента контрастности поможет избежать подобных проблем.

За основу приведенных здесь рекомендаций по контрастности текста взяты стандарты специальных возможностей в Интернете [G18: Ensuring that a contrast ratio of at least 4.5:1 exists between text (and images of text) and background behind the text](http://go.microsoft.com/fwlink/p/?linkid=221823). Данное руководство приведено в спецификации консорциума W3C *Методики для WCAG2.0*.

Чтобы видимый текст соответствовал требованиям специальных возможностей, коэффициент его контрастности должен составлять не менее 4,5:1 по отношению к фону. В число исключений входят логотипы и несущественный текст, например текст, являющийся частью неактивного компонента пользовательского интерфейса.

Исключается также декоративный текст и текст, не несущий информации. Например, если для создания фона используются случайные слова, которые можно переставить или заменить без изменения смысла, эти слова считаются декоративными и могут не соответствовать этому критерию.

Проверьте допустимость контрастности видимого текста с помощью средств измерения цветового контраста. Сведения о средствах измерения коэффициента контрастности см. в разделе [Методики WCAG 2.0 G18 (Раздел ресурсов)](http://www.w3.org/TR/WCAG20-TECHS/G18.html#G18-resources).

> [!NOTE]
> Некоторые средства, перечисленные в методиках для WCAG2.0 G18, нельзя интерактивно использовать с приложениями UWP. Вам может понадобиться вручную ввести значения цвета фона и переднего плана в средство или же сделать снимки экрана для пользовательского интерфейса вашего приложения и применить к ним средство измерения коэффициента контрастности.

<span id="Text_element_roles"/>
<span id="text_element_roles"/>
<span id="TEXT_ELEMENT_ROLES"/>

## <a name="text-element-roles"></a>Роли текстового элемента  
Приложение UWP может использовать следующие элементы по умолчанию (обычно они называются *текстовыми элементами* или *элементами управления Textedit*):

* [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/BR209652): роль— [**Text**](https://msdn.microsoft.com/library/windows/apps/BR209182)
* [**TextBox**](https://msdn.microsoft.com/library/windows/apps/BR209683): роль — [**Edit**](https://msdn.microsoft.com/library/windows/apps/BR209182)
* [**RichTextBlock**](https://msdn.microsoft.com/library/windows/apps/BR227565) (и класс переполнения [**RichTextBlockOverflow**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.richtextblockoverflow)): роль— [**Text**](https://msdn.microsoft.com/library/windows/apps/BR209182)
* [**RichEditBox**](https://msdn.microsoft.com/library/windows/apps/BR227548): роль — [**Edit**](https://msdn.microsoft.com/library/windows/apps/BR209182)

Когда элемент управления сообщает, что имеет роль [**Правка**](https://msdn.microsoft.com/library/windows/apps/BR209182), специальные возможности предполагают наличие у пользователей способов изменения этих значений. Поэтому если вы поместите статический текст в элемент [**TextBox**](https://msdn.microsoft.com/library/windows/apps/BR209683), вы неправильно сообщите пользователю специальных возможностей о роли (а значит, и о структуре) вашего приложения.

В текстовых моделях XAML существует два элемента, которые в основном используются для статического текста: [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/BR209652) и [**RichTextBlock**](https://msdn.microsoft.com/library/windows/apps/BR227565). Ни один из них не относится к подклассу [**Control**](https://msdn.microsoft.com/library/windows/apps/BR209390), и поэтому они не поддерживают перемещение фокуса с помощью клавиатуры и не могут отображаться в последовательности табуляции. Но это не означает, что специальные возможности не могут их считывать. Средства чтения с экрана обычно рассчитаны на поддержку нескольких режимов чтения содержимого в приложении, включая специальный режим чтения или шаблоны навигации, которые не работают с фокусом и последовательностью табуляции, например "виртуальный курсор". Поэтому не стоит размещать статический текст в контейнерах с поддержкой фокуса только затем, чтобы пользователь мог попадать в них с помощью последовательности табуляции. Пользователи специальных возможностей ожидают, что со всеми элементами в последовательности табуляции можно взаимодействовать, и если они столкнутся со статическим текстом, это скорее запутает их, чем поможет. Вы должны самостоятельно провести тестирование с помощью экранного диктора, чтобы оценить взаимодействие пользователя с приложением при использовании средства чтения с экрана для проверки статического текста приложения.

<span id="Auto-suggest_accessibility"/>
<span id="auto-suggest_accessibility"/>
<span id="AUTO-SUGGEST_ACCESSIBILITY"/>

## <a name="auto-suggest-accessibility"></a>Доступность автозаполнения  
Если пользователь вводит данные в поле и появляется список предложений, это называется автозаполнением. Такая функция используется для поля **Кому:** сообщения, поля поиска Кортаны в Windows, поля ввода URL-адреса в Microsoft Edge, поля ввода расположения в приложении "Погода" и т. д. Если вы используете XAML-элемент [**AutosuggestBox**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.autosuggestbox) или встроенные элементы управления HTML, эта функция уже реализована по умолчанию. Чтобы предоставить доступ к этой функции, необходимо связать поле ввода и список. Это описано в разделе [Реализация автозаполнения](#implementing_auto-suggest).

Экранный диктор был обновлен, чтобы использовать эту функцию со специальным режимом предложений. Если поле и список правильно связаны, пользователи:

* будут знать о наличии списка и его закрытии;
* будут знать о количестве доступных предложений;
* будут знать выбранный элемент, если имеется;
* смогут переместить фокус экранного диктора на список;
* смогут переходить по вариантам с помощью всех других режимов чтения.

![Список вариантов](images/autosuggest-list.png)<br/>
_Пример списка вариантов_

<span id="Implementing_auto-suggest"/>
<span id="implementing_auto-suggest"/>
<span id="IMPLEMENTING_AUTO-SUGGEST"/>

### <a name="implementing-auto-suggest"></a>Реализация автозаполнения  
Чтобы предоставить доступ к этой функции, необходимо связать поле ввода и список в дереве автоматизации пользовательского интерфейса (UIA). Это происходит с помощью свойства [UIA_ControllerForPropertyId](https://msdn.microsoft.com/windows/desktop/ee684017) для классических приложений или свойства [ControlledPeers](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.automationproperties.getcontrolledpeers) для приложений UWP.

На высоком уровне существует два типа автозаполнения.

**Выбор по умолчанию**  
Если в списке есть элемент, выбранный по умолчанию, экранный диктор ждет событие [**UIA_SelectionItem_ElementSelectedEventId**](https://msdn.microsoft.com/library/windows/desktop/ee671223) в классическом приложении или событие [**AutomationEvents.SelectionItemPatternOnElementSelected**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.peers.automationevents) в приложении UWP. Каждый раз, когда меняется выбранный элемент, пользователь вводит другую букву, варианты обновляются или пользователь переходит по списку, срабатывает событие **ElementSelected**.

![Список с выделением по умолчанию](images/autosuggest-default-selection.png)<br/>
_Пример с выделением по умолчанию_

**Без выделения по умолчанию**  
Если элемент по умолчанию не выбран, как в поле расположения приложения "Погода", экранный диктор ждет событие [**UIA_LayoutInvalidatedEventId**](https://msdn.microsoft.com/library/windows/desktop/ee671223 ) или [**LayoutInvalidated**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.peers.automationevents) для UWP, которое инициируется при каждом обновлении списка.

![Список без выделения по умолчанию](images/autosuggest-no-default-selection.png)<br/>
_Пример без выделения по умолчанию_

### <a name="xaml-implementation"></a>Реализация XAML  
Если вы используете XAML-элемент [**AutosuggestBox**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.autosuggestbox) по умолчанию, то все уже реализовано. Если вы реализуете собственную функцию автозаполнения с помощью [**TextBox**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textbox) и списка, необходимо настроить его как [**AutomationProperties.ControlledPeers**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.automationproperties.getcontrolledpeers) в элементе **TextBox**. Необходимо вызывать событие **AutomationPropertyChanged** свойства [**ControlledPeers**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.automationproperties.getcontrolledpeers) при каждом добавлении и удалении этого свойства, а также вызывать собственные события [**SelectionItemPatternOnElementSelected**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.peers.automationevents) или [**LayoutInvalidated**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.peers.automationevents) в зависимости от типа сценария, описанного ранее.

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
По возможности избегайте включения текста в графику. Например, любой текст, помещаемый в файл источника изображения, который воспроизводится в приложении в качестве элемента [**Image**](https://msdn.microsoft.com/library/windows/apps/BR242752), не является автоматически доступным (или доступным для чтения) для специальных возможностей. Если обязательно использовать текст в графике, убедитесь, что значение [**AutomationProperties.Name**](https://msdn.microsoft.com/library/windows/apps/Hh759770), которое вы предоставляете как эквивалент замещающему тексту, содержит нужный текст или краткую сводку о смысле текста. Те же принципы актуальны при создании текстовых символов из векторов как часть [**пути**](/uwp/api/Windows.UI.Xaml.Shapes.Path) или с использованием [**глифов**](https://msdn.microsoft.com/library/windows/apps/BR209921).

<span id="Text_font_size"/>
<span id="text_font_size"/>
<span id="TEXT_FONT_SIZE"/>

## <a name="text-font-size"></a>Размер шрифта  
У большинства читателей сложности при чтении текста в приложении возникают просто потому, что размер шрифта в тексте слишком мал. Эту проблему можно решить простым увеличением размера шрифта в элементах пользовательского интерфейса. Кроме того, в Windows существуют специальные возможности, с помощью которых пользователи могут изменять размеры представления приложений или размер отображения вообще.

* Один из параметров специальных возможностей позволяет пользователям изменять значение количества точек на дюйм на основном дисплее. Этот параметр доступен в разделе **Увеличение изображений на экране** страницы **Специальные возможности**, который перенаправляет вас в пользовательский интерфейс **панели управления** в раздел **Оформление и персонализация** / **Экран**. Набор доступных вариантов размера может быть разным, так как он зависит от возможностей устройства отображения.
* Экранная лупа позволяет увеличивать выбранную область пользовательского интерфейса, но ее трудно использовать для чтения текста.

<span id="Text_scale_factor"/>
<span id="text_scale_factor"/>
<span id="TEXT_SCALE_FACTOR"/>

## <a name="text-scale-factor"></a>Коэффициент масштабирования текста  
Разные текстовые элементы и элементы управления имеют свойство [**IsTextScaleFactorEnabled**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.istextscalefactorenabled). По умолчанию оно имеет значение **true**. Если значение свойства— **true**, настройка **Масштабирование текста** на телефоне (в разделе **Параметры &gt; Специальные возможности**) позволяет увеличить размер текста в этом элементе. Масштабирование применяется к тексту с малым значением **FontSize** в большей степени, чем к тексту с большим значением **FontSize**. Чтобы отключить автоматическое увеличение шрифта, присвойте свойству **IsTextScaleFactorEnabled** элемента значение **false**. Попробуйте эту разметку, измените параметр телефона **Размер текста** и посмотрите, как изменятся элементы **TextBlock**.

XAML
```xml
<TextBlock Text="In this case, IsTextScaleFactorEnabled has been left set to its default value of true."
    Style="{StaticResource BodyTextBlockStyle}"/>

<TextBlock Text="In this case, IsTextScaleFactorEnabled has been set to false."
    Style="{StaticResource BodyTextBlockStyle}" IsTextScaleFactorEnabled="False"/>
```  

Не рекомендуется постоянно отключать автоматическое увеличение, поскольку универсальное масштабирование текста пользовательского интерфейса во всех приложениях является важной специальной возможностью для пользователей и они будут ожидать, что оно будет работать и в вашем приложении.

Можно также воспользоваться событием [**TextScaleFactorChanged**](https://msdn.microsoft.com/library/windows/apps/Dn633867) и свойством [**TextScaleFactor**](https://msdn.microsoft.com/library/windows/apps/Dn633866), чтобы узнать об изменениях настройки **размер шрифта** на телефоне. Выполните следующие действия:

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

Значение параметра **TextScaleFactor** имеет тип double в диапазоне \[1,2\]. Самый маленький текст масштабируется по этому значению. С помощью этого значения можно, например, масштабировать графику в соответствии с текстом. Однако помните, что не весь текст масштабируется с одинаковым коэффициентом. В целом чем больше текст, тем меньше он подвергается масштабированию.

Данные типы имеют свойство **IsTextScaleFactorEnabled**:  
* [**ContentPresenter**](https://msdn.microsoft.com/library/windows/apps/BR209378)
* [**Control**](https://msdn.microsoft.com/library/windows/apps/BR209390) и производные классы
* [**FontIcon**](https://msdn.microsoft.com/library/windows/apps/Dn279514)
* [**RichTextBlock**](https://msdn.microsoft.com/library/windows/apps/BR227565)
* [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/BR209652)
* [**TextElement**](https://msdn.microsoft.com/library/windows/apps/BR209967) и производные классы

<span id="related_topics"/>

## <a name="related-topics"></a>Связанные темы  
* [Специальные возможности](accessibility.md)
* [Основные сведения о специальных возможностях](basic-accessibility-information.md)
* [Пример отображения текста XAML](http://go.microsoft.com/fwlink/p/?linkid=238579)
* [Образец редактирования текста XAML](http://go.microsoft.com/fwlink/p/?linkid=251417)
* [Пример реализации специальных возможностей на XAML](http://go.microsoft.com/fwlink/p/?linkid=238570) 
