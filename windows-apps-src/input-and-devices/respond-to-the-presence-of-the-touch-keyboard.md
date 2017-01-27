---
author: Karl-Bridge-Microsoft
Description: "Узнайте, как адаптировать пользовательский интерфейс приложения при отображении или скрытии сенсорной клавиатуры."
title: "Реакция на наличие сенсорной клавиатуры"
ms.assetid: 70C6130E-23A2-4F9D-88E7-7060062DA988
label: Respond to the presence of the touch keyboard
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: a3924fef520d7ba70873d6838f8e194e5fc96c62
ms.openlocfilehash: 7db7c360c1e6e3cadf6423d888240bb2f0f4651a

---

# <a name="respond-to-the-presence-of-the-touch-keyboard"></a>Реакция на наличие сенсорной клавиатуры
<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Узнайте, как адаптировать пользовательский интерфейс приложения при отображении или скрытии сенсорной клавиатуры.

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**AutomationPeer**](https://msdn.microsoft.com/library/windows/apps/br209185)</li>
<li>[**InputPane**](https://msdn.microsoft.com/library/windows/apps/br242255)</li>
</ul>
</div> 



![Сенсорная клавиатура в режиме раскладки по умолчанию](images/touchkeyboard-standard.png)

<sup>Сенсорная клавиатура в режиме раскладки по умолчанию</sup>

С помощью сенсорной клавиатуры можно вводить текст на устройствах, поддерживающих сенсорный ввод. В универсальной платформе Windows (UWP) элементы управления для ввода текста по умолчанию вызывают сенсорную клавиатуру, когда пользователь касается редактируемого поля ввода. Обычно сенсорная клавиатура остается видимой, когда пользователь переходит по элементам управления в форме, но это поведение может меняться в зависимости от имеющихся в форме элементов управления других типов.

Чтобы поддерживать необходимое поведение сенсорной клавиатуры в пользовательском элементе управления для ввода текста, не являющемся производным от стандартного элемента управления для ввода текста, следует использовать класс [**AutomationPeer**](https://msdn.microsoft.com/library/windows/apps/br209185). С помощью этого класса можно предоставить ваши элементы управления модели автоматизации пользовательского интерфейса Microsoft, а также реализовать правильные шаблоны элементов управления для этой модели. См. статьи [Специальные возможности клавиатуры](https://msdn.microsoft.com/library/windows/apps/mt244347) и [Настраиваемые одноранговые элементы автоматизации](https://msdn.microsoft.com/library/windows/apps/mt297667).

После добавления такой поддержки в пользовательский элемент управления приложение сможет правильно реагировать на наличие сенсорной клавиатуры.

**Предварительные требования:  **

В данной статье использованы материалы статьи [Взаимодействие с помощью клавиатуры](keyboard-interactions.md).

Для работы со статьей необходимо иметь базовое представление о стандартных взаимодействиях клавиатуры, обработке событий клавиатуры и данных, вводимых с клавиатуры, а также о модели автоматизации пользовательского интерфейса.

Если вы — начинающий разработчик приложений универсальной платформы для Windows (UWP), прочитайте указанные ниже статьи, чтобы ознакомиться с описанными здесь технологиями.

-   [Создание первого приложения](https://msdn.microsoft.com/library/windows/apps/bg124288)
-   Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

**Рекомендации по взаимодействию с пользователем.  **

Полезные советы по разработке удобных и привлекательных приложений, оптимизированных для ввода данных с клавиатуры, см. в разделе [Руководство по сенсорной клавиатуре](https://msdn.microsoft.com/library/windows/apps/hh972345).

## <a name="touch-keyboard-and-a-custom-ui"></a>Сенсорная клавиатура и настраиваемый пользовательский интерфейс


Ниже перечислены несколько основных рекомендаций для пользовательских элементов управления для ввода текста.

-   Сенсорная клавиатура должна отображаться в течение всего времени взаимодействия пользователя с формой.

-   Убедитесь, что у ваших пользовательских элементов управления имеется необходимый [**AutomationControlType**](https://msdn.microsoft.com/library/windows/apps/br209182) модели автоматизации пользовательского интерфейса. Это необходимо, чтобы клавиатура отображалась даже тогда, когда поле ввода текста теряет фокус в контексте ввода текста. Например, если в середине сценария текстового ввода открывается меню, а клавиатура должна по-прежнему отображаться, меню должно принадлежать к типу элемента управления меню **AutomationControlType**.

-   Не изменяйте свойства модели автоматизации пользовательского интерфейса для управления сенсорной клавиатурой. От точности настройки этих свойств зависят другие средства специальных возможностей.

-   Убедитесь, что пользователю всегда видно поле ввода, с которым он взаимодействует.

    Так как сенсорная клавиатура занимает большую часть экрана, UWP делает так, что поле ввода, находящееся в фокусе, всегда будет в поле зрения пользователя, когда тот переходит по элементам управления в форме, включая те элементы управления, которые в данный момент находятся вне поля зрения.

    При настройке пользовательского интерфейса обеспечьте аналогичную реакцию на появление сенсорной клавиатуры путем обработки событий [**Showing**](https://msdn.microsoft.com/library/windows/apps/br242262) и [**Hiding**](https://msdn.microsoft.com/library/windows/apps/br242260), предоставленных объектом [**InputPane**](https://msdn.microsoft.com/library/windows/apps/br242255).

    ![Форма с сенсорной клавиатурой и без нее](images/touch-keyboard-pan1.png)

    В ряде случаев некоторые элементы пользовательского интерфейса должны присутствовать на экране постоянно. При разработке пользовательского интерфейса следите за тем, чтобы элементы управления формы находились в области панорамирования, а важные элементы пользовательского интерфейса были статичными. Пример.

    ![Форма, содержащая области, которые всегда должны быть в поле зрения](images/touch-keyboard-pan2.png)

## <a name="handling-the-showing-and-hiding-events"></a>Обработка событий отображения и скрытия


В примере ниже показано, как прикрепить обработчики событий [**showing**](https://msdn.microsoft.com/library/windows/apps/br242262) и [**hiding**](https://msdn.microsoft.com/library/windows/apps/br242260) сенсорной клавиатуры.

```CSharp
public class MyApplication
{
    public MyApplication()
    {
        // Grab the input pane for the main application window and attach
        // touch keyboard event handlers.
        Windows.Foundation.Application.InputPane.GetForCurrentView().Showing  
            += new EventHandler(_OnInputPaneShowing);
        Windows.Foundation.Application.InputPane.GetForCurrentView().Hiding 
            += new EventHandler(_OnInputPaneHiding);
    }

    private void _OnInputPaneShowing(object sender, IInputPaneVisibilityEventArgs eventArgs)
    {
        // If the size of this window is going to be too small, the app uses 
        // the Showing event to begin some element removal animations.
        if (eventArgs.OccludedRect.Top < 400)
        {
            _StartElementRemovalAnimations();

            // Don&#39;t use framework scroll- or visibility-related 
            // animations that might conflict with the app&#39;s logic.
            eventArgs.EnsuredFocusedElementInView = true; 
        }
    }

    private void _OnInputPaneHiding(object sender, IInputPaneVisibilityEventArgs eventArgs)
    {
        if (_ResetToDefaultElements())
        {
            eventArgs.EnsuredFocusedElementInView = true; 
        }
    }

    private void _StartElementRemovalAnimations()
    {
        // This function starts the process of removing elements 
        // and starting the animation.
    }

    private void _ResetToDefaultElements()
    {
        // This function resets the window&#39;s elements to their default state.
    }
}
```

## <a name="related-articles"></a>Связанные разделы

* [Взаимодействие с помощью клавиатуры](keyboard-interactions.md)
* [Специальные возможности клавиатуры](https://msdn.microsoft.com/library/windows/apps/mt244347)
* [Настраиваемые одноранговые элементы автоматизации](https://msdn.microsoft.com/library/windows/apps/mt297667)


**Примеры архива**
* [Ввод: пример сенсорной клавиатуры](http://go.microsoft.com/fwlink/p/?linkid=246019)
* [Пример реакции на появление экранной клавиатуры](http://go.microsoft.com/fwlink/p/?linkid=231633)
* [Пример редактирования текста XAML](http://go.microsoft.com/fwlink/p/?LinkID=251417)
* [Пример реализации специальных возможностей на XAML](http://go.microsoft.com/fwlink/p/?linkid=238570)
 

 







<!--HONumber=Dec16_HO2-->


