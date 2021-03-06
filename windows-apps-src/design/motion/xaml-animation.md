---
ms.assetid: 0C8DEE75-FB7B-4E59-81E3-55F8D65CD982
title: Обзор анимаций
description: Используйте анимации из библиотеки среды выполнения Windows, чтобы ваше приложение соответствовало внешнему виду Windows.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d6fa8740fba35ae6a50d91846bab79a4858de076
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970489"
---
# <a name="animations-in-xaml"></a>Анимации в XAML

Анимация может улучшить приложение, добавляя перемещение и интерактивность. Воспользовавшись анимациями из библиотеки среды выполнения Windows, вы можете легко привести внешний вид своего приложения в соответствие с внешним видом Windows. В этой статье представлена сводка таких анимаций и даны примеры типичных сценариев использования каждой из них.

> [!TIP]
> Элементы управления среды выполнения Windows для XAML содержат анимации некоторых типов в виде встроенных событий, которые берутся из библиотеки анимации. Применяя эти элементы управления в приложениях, вы получите внешний вид и удобство анимаций без необходимости программировать их самостоятельно.

Анимации из библиотеки анимации среды выполнения Windows дают следующие преимущества.

-   движения, которые соответствуют требованиям [руководства по анимациям](https://docs.microsoft.com/windows/uwp/style/motion);
-   Быстрые, плавные переходы между состояниями пользовательского интерфейса, которые информируют, но не отвлекают пользователя.
-   Визуальное поведение, указывающее пользователю на переходы в пределах приложения

Например, когда пользователь добавляет элемент в список, вместо немедленного отображения нового элемента в списке происходит анимированное перемещение этого элемента на свое место. Остальные элементы списка в течение небольшого промежутка времени анимированно перемещаются на новые позиции, освобождая место для добавленного элемента. Поведение при переходе делает взаимодействие элементов управления более очевидным для пользователя.

В Windows 10 версии 1607 представлен новый API [**ConnectedAnimationService**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice), который позволяет анимировать элемент при навигации между представлениями. Использование данного API отличается от других API библиотеки анимации. Использование **ConnectedAnimationService** рассматривается на [странице справки](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice).

Библиотека анимации не предоставляет анимаций для всех возможных сценариев. В некоторых случаях вам может понадобиться создать собственную анимацию на XAML. Подробнее: [Раскадрованные анимации](storyboarded-animations.md).

Кроме того, для некоторых сложных сценариев, таких как анимация элемента на основе положения прокрутки ScrollViewer разработчикам может потребоваться использовать взаимодействие визуального уровня для реализации пользовательских анимаций. Дополнительные сведения см. в разделе [Визуальный уровень](https://docs.microsoft.com/windows/uwp/composition/visual-layer).

## <a name="types-of-animations"></a>Типы анимаций

Система анимации в среде выполнения Windows и библиотека анимации служат более масштабной цели — реализации анимации в элементах управления и других компонентах пользовательского интерфейса. Существует несколько различных типов анимации.

-   *Переходы темы* применяются автоматически при изменении определенных условий в пользовательском интерфейсе. В них задействуются элементы управления или элементы типов пользовательского интерфейса на XAML, заранее определенных в среде выполнения Windows. Они называются *переходами темы*, так как анимации поддерживают внешний вид и удобство Windows, а также определяют поведение всех приложений в конкретных сценариях пользовательского интерфейса при переходе от одного режима взаимодействия к другому. Переходы темы входят в библиотеку анимации.
-   *Анимации темы* — это анимации одного или нескольких свойств, относящихся к готовым типам пользовательского интерфейса на XAML из среды выполнения Windows. Анимации тем отличаются от переходов темы, так как первые ориентированы на один конкретный элемент и существуют в конкретных визуальных состояниях элемента управления, а переходы темы назначаются свойствам элемента управления, существующим вне визуальных состояний и влияющим на переходы между этими состояниями. Многие элементы управления среды выполнения Windows на XAML содержат анимации тем в раскадровках, входящих в состав шаблона данного элемента управления, причем анимации запускаются визуальными состояниями. Если вы не вносите изменений в шаблоны, то такие встроенные анимации тем доступны для элементов управления пользовательского интерфейса. Однако в случае замены шаблонов происходит удаление и встроенных анимаций тем элементов управления. Чтобы вернуть их, необходимо определить раскадровку, которая включает анимации тем в составе набора визуальных состояний элемента управления. Можно также запускать анимации тем из раскадровок, не относящихся к визуальным состояниям, используя для этого метод [**Begin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.begin), однако такой способ применяется редко. Все анимации темы входят в библиотеку анимации.
-   *Визуальные переходы* применяются, когда элемент управления переходит из одного заданного визуального состояния в другое. Эти анимации создаются пользователем и обычно связываются с настраиваемым шаблоном, который создается для элемента управления, и определениями визуальных состояний в этом шаблоне. Анимация выполняется только во время между состояниями. Обычно это малое время, не более нескольких секунд. Подробнее см. в [подразделе "VisualTransition" раздела "Раскадрованные анимации для визуальных состояний"](https://docs.microsoft.com/previous-versions/windows/apps/jj819808(v=win.10)).
-   *Раскадрованные анимации* постепенно анимируют значение свойства зависимости в среде выполнения Windows. Раскадровки можно определять как часть визуального перехода или запускать во время выполнения приложения. Подробнее: [Раскадрованные анимации](storyboarded-animations.md). Подробнее о свойствах зависимостей и их размещении см в разделе [Общие сведения о свойствах зависимостей](https://docs.microsoft.com/windows/uwp/xaml-platform/dependency-properties-overview).
-   *Связанные анимации*, предоставляемые в новом API [**ConnectedAnimationService**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice), позволяют разработчикам создавать эффект анимации элемента при навигации между представлениями. Этот API доступен в Windows 10 начиная с версии 1607. Дополнительные сведения см. в разделе [**ConnectedAnimationService**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice).

## <a name="animations-available-in-the-library"></a>Анимации, доступные в библиотеке

В библиотеке анимации доступны следующие анимации. Щелкните название анимации, чтобы узнать подробности об основных сценариях их использования, способах их определения, а также просмотреть пример анимации.

-   [Переход страницы](#page-transition): анимирует переходы между страницами во [**фрейме**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame).
-   [Переход содержимого и входа](#content-transition-and-entrance-transition): анимирует отображение или прекращение отображения одного элемента содержимого или их набора.
-   [Появление или исчезновение и плавный переход между элементами](#fade-in-out-and-crossfade): отображает переходные элементы или элементы управления либо обновляет область содержимого.
-   [Касание/отрыв указателя](#pointer-up-down): обеспечивает визуальную реакцию на касание или щелчок плитки.
-   [Перемещение](#reposition): перемещает элемент на новое место.
-   [Показ и скрытие всплывающих окон](#show-hide-popup): отображает или скрывает контекстно-зависимые элементы пользовательского интерфейса поверх представления.
-   [Показ или скрытие элементов пользовательского интерфейса по краям](#show-hide-edge-ui): перемещение элементов пользовательского интерфейса по краям экрана (например, панели) в отображаемую область и из нее.
-   [Изменения элементов списка](#list-item-changes): добавление и удаление элемента из списка либо переупорядочивание элементов.
-   [Перетаскивание](#drag-drop): визуальное отображение операции перетаскивания.

### <a name="page-transition"></a>Переход страницы

Используйте переходы страницы для анимации навигации внутри приложения. Поскольку практически во всех приложениях используется какая-либо навигация, анимации перехода страницы являются наиболее распространенным типом анимации темы, используемым приложениями. Подробнее об API-интерфейсах перехода страницы: [**NavigationThemeTransition**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.navigationthemetransition).



### <a name="content-transition-and-entrance-transition"></a>Переход между содержимым и переход во время первой загрузки

Используйте анимации перехода между содержимым ([**ContentThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.ContentThemeTransition)) при перемещении элемента или набора элементов содержимого в текущее представление или из него. Например, анимации перехода между содержимым отображают содержимое, которое не было готово к отображению при первом показе страницы или при изменении содержимого раздела страницы.

[**EntranceThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.EntranceThemeTransition) представляет собой движение, которое может быть применено к содержимому при первой загрузке страницы или большой части пользовательского интерфейса. Таким образом, реакция на первое представление содержимого и на его изменение может быть разной. Класс [**EntranceThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.EntranceThemeTransition) аналогичен классу [**NavigationThemeTransition**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.navigationthemetransition) вместе с параметрами по умолчанию, однако может быть использован вне класса [**Frame**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame).
 
 
<span id="fade-in-out-and-crossfade"/>

### <a name="fade-inout-and-crossfade"></a>Появление или исчезновение и плавный переход между элементами

Используйте анимации появления или исчезания для показа или скрытия промежуточных элементов пользовательского интерфейса либо элементов управления. В XAML они представлены как [**FadeInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation) и [**FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation). Одним из примеров этой анимации может послужить панель приложения, в которой благодаря действиям пользователя могут появляться новые элементы управления. Другим примером является промежуточная полоса прокрутки или индикатор сдвига, исчезающий, если пользователь не вводит данные в течение определенного периода времени. Приложениям также следует использовать анимацию появления при переходе от заполнителя к итоговому элементу во время динамической загрузки содержимого.

Используйте эту анимацию, чтобы сделать более плавным переход, когда состояние элемента изменяется, например при обновлении приложением текущего содержимого представления. В библиотеке анимации XAML нет специальной анимации с плавным переходом между элементами (нет эквивалента для [**crossFade**](https://docs.microsoft.com/previous-versions/windows/apps/br212661(v=win.10))), однако тот же результат можно получить с помощью [**FadeInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation) и [**FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation) при совмещении по времени.

<span id="pointer-up-down"/>

### <a name="pointer-updown"></a>Касание или отрыв указателя

Используйте анимации [**PointerUpThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointerUpThemeAnimation) и [**PointerDownThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointerDownThemeAnimation), чтобы показать пользователю реакцию на успешное касание или щелчок плитки. Например, когда пользователь щелкает плитку или касается ее, отображается анимация касания указателя. Когда пользователь отпускает плитку, отображается анимация отрыва указателя.

### <a name="reposition"></a>Изменение положения

Используйте анимацию перемещения ([**RepositionThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.RepositionThemeAnimation) или [**RepositionThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.RepositionThemeTransition)) для перемещения элементов в новое положение. Например, при перемещении заголовков в элементе управления элементами используется анимация изменения положения.

<span id="show-hide-popup"/>

### <a name="showhide-popup"></a>Показ или скрытие всплывающих окон

Используйте [**PopInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopInThemeAnimation) и [**PopOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopOutThemeAnimation) во время показа и скрытия [**Popup**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.Popup) или аналогичного элемента контекстного пользовательского интерфейса, который находится поверх текущего представления. [**PopupThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopupThemeTransition) — это переход темы, который удобно использовать для отображения визуальной реакции во время исчезновения всплывающего окна.

<span id="show-hide-edge-ui"/>

### <a name="showhide-edge-ui"></a>Показ или скрытие элементов пользовательского интерфейса границ

Используйте анимации [**EdgeUIThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.EdgeUIThemeTransition) для отображения и скрытия мелких элементов пользовательского интерфейса границ. Например, они понадобятся при отображении собственной панели приложения вверху или внизу экрана или поверхности интерфейса для вывода сообщений об ошибках и предупреждениях в верхней части экрана.

Используйте анимацию [**PaneThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PaneThemeTransition) для показа и скрытия области или панели. Это относится к крупным элементам пользовательского интерфейса границ, таким как пользовательская клавиатура или область задач.

### <a name="list-item-changes"></a>Изменения элементов списка

Используйте анимацию [**AddDeleteThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.AddDeleteThemeTransition), чтобы добавить анимированное поведение при внесении элемента в существующий список и удалении из него. При добавлении переход предварительно переместит существующие элементы в списке, чтобы освободить место для новых элементов, затем добавит новые элементы. При удалении переход удалит элементы из списка и при необходимости изменит положение остающихся элементов после удаления.

Также существует отдельный класс [**ReorderThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.ReorderThemeTransition), который применяется, если элемент изменяет положение в списке. Он анимируется не так, как удаление или добавление элемента в другое расположение.

Обратите внимание, что эти анимации входят в состав стандартных шаблонов [**ListView**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview) и [**GridView**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.gridview), поэтому если эти элементы управления уже используются, необходимость в ручном добавлении этих анимаций отсутствует.

<span id="drag-drop"/>

### <a name="dragdrop"></a>Перетаскивание

Используйте анимации перетаскивания ([**DragItemThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DragItemThemeAnimation), [**DragOverThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DragOverThemeAnimation)) и анимацию отпускания ([**DropTargetItemThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DropTargetItemThemeAnimation)), чтобы отобразить визуальную реакцию на перетаскивание элемента пользователем.

При активации анимации показывают пользователю, что список можно переупорядочить относительно перемещенного элемента. Пользователям может быть полезно знать, где элемент окажется в списке, если его сейчас отпустить. Анимации демонстрируют визуальную реакцию на перетаскивание и показывают, что элемент можно отпустить между двумя другими элементами списка, которые отодвинутся в стороны.

## <a name="using-animations-with-custom-controls"></a>Использование анимаций с пользовательскими элементами управления

Ниже приведена сводная таблица с рекомендациями по использованию анимаций для пользовательских версий указанных элементов управления среды выполнения Windows.

| Тип пользовательского интерфейса | Рекомендованная анимация |
|---------|-----------------------|
| . | [**FadeInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation) и [**FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation) |
| Всплывающий элемент | [**PopInThemeAnimation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.popinthemeanimation.popinthemeanimation) и [**PopOutThemeAnimation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.popoutthemeanimation.popoutthemeanimation) |
| Всплывающая подсказка | [**FadeInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation) и [**FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation) |
| Контекстное меню | [**PopInThemeAnimation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.popinthemeanimation.popinthemeanimation) и [**PopOutThemeAnimation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.popoutthemeanimation.popoutthemeanimation) |
| Панель команд | [**EdgeUIThemeTransition**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.edgeuithemetransition.edgeuithemetransition) |
| Область задач или панель, привязанная к границам экрана | [**PaneThemeTransition**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.panethemetransition.panethemetransition) |
| Содержимое любого контейнера пользовательского интерфейса | [**ContentThemeTransition**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.contentthemetransition.contentthemetransition) |
| Для элементов управления или если другая анимация не применяется | [**FadeInThemeAnimation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.fadeinthemeanimation.fadeinthemeanimation) и [**FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation) |

 

## <a name="transition-animation-examples"></a>Примеры анимаций перехода

Лучше всего, когда анимации в приложениях улучшают пользовательский интерфейс или повышают привлекательность приложения, но не надоедают пользователям. Один из способов достичь этого — использовать анимированные переходы в пользовательском интерфейсе, которые привлекают внимание пользователя, когда что-то возникает на экране, пропадает с него или изменяется. Например, кнопки на экране могут появляться и исчезать плавно, а не мгновенно. Мы создали несколько API, с помощью которых можно реализовывать согласованные рекомендуемые или типовые анимированные переходы. Пример ниже показывает, как анимировать кнопку так, чтобы она быстро появлялась на экране.

```xml
<Button Content="Transitioning Button">
     <Button.Transitions>
         <TransitionCollection> 
             <EntranceThemeTransition/>
         </TransitionCollection>
     </Button.Transitions>
 </Button>
 ```

В этом коде мы добавляем в коллекцию переходов кнопки объект [**EntranceThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.EntranceThemeTransition). Теперь при обработке кнопка будет не просто возникать на экране, а быстро выскальзывать на него. Вы можете задать ряд свойств объекта анимации, чтобы указать, с какой стороны он будет появляться и какое расстояние будет проходить, но на самом деле этот API задуман как простой инструмент для конкретной цели — привлечь внимание к появлению объекта.

Кроме того, можно указать темы анимаций перехода в ресурсах стиля вашего приложения, что позволит соблюсти единообразие в применении эффекта. Пример ниже аналогичен предыдущему, но реализован с использованием стиля ([**Style**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style)):

```xml
<UserControl.Resources>
     <Style x:Key="DefaultButtonStyle" TargetType="Button">
         <Setter Property="Transitions">
             <Setter.Value>
                 <TransitionCollection>
                     <EntranceThemeTransition/>
                 </TransitionCollection>
             </Setter.Value>
        </Setter>
    </Style>
</UserControl.Resources>
      
<StackPanel x:Name="LayoutRoot">
    <Button Style="{StaticResource DefaultButtonStyle}" Content="Transitioning Button"/>
</StackPanel>
```

В предыдущих примерах анимация перехода на базе темы применяется к отдельному элементу управления. Но переходы на базе тем выглядят еще привлекательнее, когда применяются к контейнеру объектов. В этом случае в переходе принимают участие все дочерние объекты контейнера. В следующем примере к сетке ([**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid)) из прямоугольников применяется переход [**EntranceThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.EntranceThemeTransition).

```xml
<!-- If you set an EntranceThemeTransition animation on a panel, the
     children of the panel will automatically offset when they animate
     into view to create a visually appealing entrance. -->        
<ItemsControl Grid.Row="1" x:Name="rectangleItems">
    <ItemsControl.ItemContainerTransitions>
        <TransitionCollection>
            <EntranceThemeTransition/>
        </TransitionCollection>
    </ItemsControl.ItemContainerTransitions>
    <ItemsControl.ItemsPanel>
        <ItemsPanelTemplate>
            <WrapGrid Height="400"/>
        </ItemsPanelTemplate>
    </ItemsControl.ItemsPanel>
            
    <!-- The sequence children appear depends on their order in 
         the panel's children, not necessarily on where they render
         on the screen. Be sure to arrange your child elements in
         the order you want them to transition into view. -->
    <ItemsControl.Items>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
    </ItemsControl.Items>
</ItemsControl>
```

Дочерние прямоугольники перехода [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) появляются на экране один за другим, а не все сразу, как было бы, если бы вы применили эту анимацию к каждому прямоугольнику по отдельности.

Вот как выглядит эта анимация:

![Анимация появления на экране дочернего прямоугольника](./images/animation-child-rectangles.gif)

Кроме того, дочерние объекты контейнера могут перетекать, когда один или несколько из них меняют свое положение. В следующем примере мы применяем объект [**RepositionThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.RepositionThemeTransition) к сетке прямоугольников. Когда вы убираете один из прямоугольников, все остальные перетекают на новые места.

```xml
<Button Content="Remove Rectangle" Click="RemoveButton_Click"/>
        
<ItemsControl Grid.Row="1" x:Name="rectangleItems">
    <ItemsControl.ItemContainerTransitions>
        <TransitionCollection>
                    
            <!-- Without this, there would be no animation when items 
                 are removed. -->
            <RepositionThemeTransition/>
        </TransitionCollection>
    </ItemsControl.ItemContainerTransitions>
    <ItemsControl.ItemsPanel>
        <ItemsPanelTemplate>
            <WrapGrid Height="400"/>
        </ItemsPanelTemplate>
    </ItemsControl.ItemsPanel>
            
    <!-- All these rectangles are just to demonstrate how the items
         in the grid re-flow into position when one of the child items
         are removed. -->
    <ItemsControl.Items>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
        <Rectangle Fill="Red" Width="100" Height="100" Margin="10"/>
    </ItemsControl.Items>
</ItemsControl>
```

```cs
private void RemoveButton_Click(object sender, RoutedEventArgs e)
{
    if (rectangleItems.Items.Count > 0)
    {    
        rectangleItems.Items.RemoveAt(0);
    }                         
}
```

```cpp
// .h
private:
void RemoveButton_Click(Platform::Object^ sender, Windows::UI::Xaml::RoutedEventArgs^ e);

//.cpp
void BlankPage::RemoveButton_Click(Platform::Object^ sender, Windows::UI::Xaml::RoutedEventArgs^ e)
{
    if (rectangleItems->Items->Size > 0)
    {    
        rectangleItems->Items->RemoveAt(0);
    }
}
```

К отдельному объекту или контейнеру объектов можно применить несколько анимаций перехода. Например, если вы хотите анимировать появление на экране набора прямоугольников, а также использовать анимацию, когда они меняют положение, можно применить объекты [**RepositionThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.RepositionThemeTransition) и [**EntranceThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.EntranceThemeTransition) следующим образом:

```xml
...
<ItemsControl.ItemContainerTransitions>
    <TransitionCollection>
        <EntranceThemeTransition/>                    
        <RepositionThemeTransition/>
    </TransitionCollection>
</ItemsControl.ItemContainerTransitions>
...      
```

Есть несколько эффектов перехода, с помощью которых можно анимировать добавление, удаление и изменение порядка объектов, а также другие действия с объектами. Имена всех таких API содержат текст "ThemeTransition".

| API | Описание |
|-----|-------------|
| [**NavigationThemeTransition**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.navigationthemetransition) | Обеспечивает анимацию персонализации Windows для перемещения по страницам во [**фрейме**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame). |
| [**AddDeleteThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.AddDeleteThemeTransition) | Обеспечивает анимированный переход в ситуациях, когда элементы управления добавляют или удаляют дочерние объекты либо содержимое. Обычно элемент управления представляет собой контейнер элементов. |
| [**ContentThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.ContentThemeTransition) | Обеспечивает анимированный переход в ситуациях, когда содержимое элемента управления изменяется. Может применяться в дополнение к [**AddDeleteThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.AddDeleteThemeTransition). |
| [**EdgeUIThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.EdgeUIThemeTransition) | Обеспечивает анимированный переход для (мелких) элементов пользовательского интерфейса по краям. |
| [**EntranceThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.EntranceThemeTransition) | Обеспечивает анимированный переход в ситуациях, когда элемент управления появляется впервые. |
| [**PaneThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PaneThemeTransition) | Обеспечивает анимированный переход для панели пользовательского интерфейса (крупных элементов пользовательского интерфейса по краям). |
| [**PopupThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopupThemeTransition) | Обеспечивает анимированный переход для всплывающих компонентов элементов управления (например, пользовательский интерфейс типа "подсказка" на объекте) при их появлении. |
| [**ReorderThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.ReorderThemeTransition) | Обеспечивает анимированный переход в ситуациях, когда в элементах управления, представленных списками, изменяется порядок следования элементов. Обычно это происходит в результате перетаскивания. Разные элементы управления и темы могут иметь различные характеристики для анимаций. |
| [**RepositionThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.RepositionThemeTransition) | Обеспечивает анимированный переход в ситуациях, когда изменяется положение элементов управления. |

 

## <a name="theme-animation-examples"></a>Примеры анимаций темы

Применять анимации перехода просто. Но, возможно, вам понадобятся более широкие возможности по настройке времени и порядка следования эффектов анимации. Темы позволяют точнее управлять анимацией, сохраняя единообразие внешнего вида и поведения. Вдобавок анимации темы требуют меньшего количества разметки, чем пользовательские анимации. Здесь мы анимируем исчезновение прямоугольника с помощью объекта [**FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation).

```xml
<StackPanel>    
    <StackPanel.Resources>
        <Storyboard x:Name="myStoryboard">
            <FadeOutThemeAnimation TargetName="myRectangle" />
        </Storyboard>
    </StackPanel.Resources>
    <Rectangle PointerPressed="Rectangle_Tapped" x:Name="myRectangle"  
              Fill="Blue" Width="200" Height="300"/>
</StackPanel>
```

```cs
// When the user taps the rectangle, the animation begins.
private void Rectangle_Tapped(object sender, PointerRoutedEventArgs e)
{
    myStoryboard.Begin();
}
```

```vb
' When the user taps the rectangle, the animation begins.
Private Sub Rectangle_Tapped(sender As Object, e As PointerRoutedEventArgs)
    myStoryboard.Begin()
End Sub
```

```cpp
//.h
void Rectangle_Tapped(Platform::Object^ sender, Windows::UI::Xaml::Input::PointerRoutedEventArgs^ e);

//.cpp
void BlankPage::Rectangle_Tapped(Object^ sender, PointerRoutedEventArgs^ e)
{
    myStoryboard->Begin();
}
```

В отличие от анимации перехода, анимация темы не имеет встроенного триггера, по которому она запускается. Следует использовать [**раскадровку**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) в качестве контейнера для анимации тем на этапе определения в XAML. Кроме того, можно изменять действия анимации по умолчанию. Например, вы можете замедлить исчезновение, увеличив значение времени [**Duration**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.duration) объекта [**FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation).

**Примечание**  . в целях отображения основных методов анимации мы используем код приложения для запуска анимации, вызывая методы [**раскадровки**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard). Управлять выполнением анимаций **раскадровки** можно с помощью методов [**Begin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.begin), [**Stop**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.stop), [**Pause**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.pause) и [**Resume** класса ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.resume) **Storyboard**. Однако это не типичный способ включения анимаций библиотеки в приложения. Обычно выполняется интеграция анимаций библиотеки в стили и шаблоны XAML, применяемые к элементам управления или элементам. Изучение шаблонов и визуальных состояний требует несколько больших усилий. Использование анимаций библиотеки в визуальных состояниях рассматривается в разделе [Раскадрованные анимации для визуальных состояний](https://docs.microsoft.com/previous-versions/windows/apps/jj819808(v=win.10)).

 

Вы также можете применять к элементам своего пользовательского интерфейса ряд других анимаций темы для создания анимационных эффектов. Имена всех таких API содержат текст "ThemeAnimation".

| API | Описание |
|-----|-------------|
| [**DragItemThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DragItemThemeAnimation) | Представляет анимацию с заданными настройками, которая применяется для перемещаемых элементов. |
| [**DragOverThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DragOverThemeAnimation) | Представляет анимацию с заданными настройками, которая применяется для элементов, расположенных ниже перемещаемого элемента. |
| [**DropTargetItemThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DropTargetItemThemeAnimation) | Анимация с заданными настройками, которая применяется к элементам, на которые можно перетаскивать объекты. |
| [**FadeInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation) | Анимация непрозрачности с заданными настройками, которая применяется к элементам управления при их первом появлении. |
| [**FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation) | Анимация непрозрачности с заданными настройками, которая применяется к элементам управления, когда они удаляются из пользовательского интерфейса или скрываются. |
| [**PointerDownThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointerDownThemeAnimation) | Анимация с заданными настройками для действия пользователя, связанного с касанием или выбором объекта или элемента. |
| [**PointerUpThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointerUpThemeAnimation) | Анимация с заданными настройками для действия пользователя, выполняемая после того, как пользователь коснется или выберет объект или элемент, а затем отпустит его. |
| [**PopInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopInThemeAnimation) | Анимация с заданными настройками, которая применяется к всплывающим компонентам элементов управления при их появлении. Она объединяет непрозрачность и преобразование. |
| [**PopOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopOutThemeAnimation) | Анимация с заданными настройками, которая применяется к всплывающим компонентам элементов управления, когда они закрываются или удаляются. Она объединяет непрозрачность и преобразование. |
| [**RepositionThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.RepositionThemeAnimation) | Анимация с заданными настройками, которая применяется к объекту при изменении его положения. |
| [**SplitCloseThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.SplitCloseThemeAnimation) | Анимация с заданными настройками, которая скрывает целевой пользовательский интерфейс с помощью анимации в виде открытия и закрытия [**ComboBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.combobox). |
| [**SplitOpenThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.SplitOpenThemeAnimation) | Анимация с заданными настройками, которая выводит целевой пользовательский интерфейс с помощью анимации в виде открытия и закрытия [**ComboBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.combobox). |
| [**DrillInThemeAnimation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.drillinthemeanimation) | Представляет собой анимацию с заданными настройками, которая выполняется при переходе вперед в логической иерархии, например от эталонной страницы к странице сведений. |
| [**DrillOutThemeAnimation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.drilloutthemeanimation) | Представляет собой анимацию с заданными настройками, которая выполняется при переходе назад в логической иерархии, например от страницы сведений к эталонной странице. |

 

## <a name="create-your-own-animations"></a>Создание собственных анимаций

Если для реализации вашего замысла недостаточно анимаций темы, вы можете создавать собственные анимации. Объекты анимируются при помощи анимации значения одного или нескольких их свойств. Например, вы можете анимировать ширину прямоугольника, угол в [**RotateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RotateTransform) или значение цвета кнопки. Мы называем такой тип пользовательской анимации раскадрованной анимацией — это позволяет отличать ее от анимации библиотеки, которую обеспечивает среда выполнения Windows в качестве анимации с заданными настройками. Для раскадровки используется анимация, которая способна изменять значения определенного типа (например, [**DoubleAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DoubleAnimation) для анимации **Double**). Для управления такой анимацией ее помещают в класс [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard).

Чтобы использовать свойство для анимации, оно должно быть *свойством зависимостей*. Дополнительные сведения о свойствах зависимостей см. в разделе [Общие сведения о свойствах зависимостей](https://docs.microsoft.com/windows/uwp/xaml-platform/dependency-properties-overview). Дополнительные сведения о создании пользовательских раскадрованных анимаций, в том числе об их нацеливании и управлении ими см. в разделе [Раскадрованные анимации](storyboarded-animations.md).

Самая большая область определения пользовательского интерфейса приложения в XAML, где вы определяете пользовательские раскадрованные анимации, — это определение визуальных состояний элементов управления в XAML. Это делается при создании нового класса элементов управления или при изменении шаблона существующего элемента управления, имеющего визуальные состояния в шаблоне. Подробнее об этом см. в разделе [Раскадрованные анимации для визуальных состояний](https://docs.microsoft.com/previous-versions/windows/apps/jj819808(v=win.10)).

 

 




