---
Description: Use the ParallaxView control to add depth and movement to your app.
title: Сведения об использовании элемента управления ParallaxView для добавления в приложение глубины и движения.
ms.assetid: ''
label: Parallax View
template: detail.hbs
ms.date: 08/9/2017
ms.topic: article
keywords: windows10, uwp
pm-contact: abarlow
design-contact: conrwi
dev-contact: stpete
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: d53bb0747ba5fb3469f9a395847eb856f72c9632
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "8193896"
---
# <a name="parallax"></a>Параллакс

Параллакс— это визуальный эффект, при котором элементы, расположенные ближе к зрителю, перемещаются быстрее элементов фона. Эффект параллакса создает ощущение глубины, перспективы и движения. В приложении UWP для создания эффекта параллакса можно использовать элемент управления ParallaxView.  

> **Важные API**: [класс ParallaxView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Parallaxview), [свойство VerticalShift](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Parallaxview.VerticalShift), [свойство HorizontalShift](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Parallaxview.HorizontalShift)

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ParallaxView">открыть приложение и увидеть ParallaxView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="parallax-and-the-fluent-design-system"></a>Параллакс и система проектирования Fluent Design

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Параллакс— это компонент системы проектирования Fluent Design, добавляющий движение, глубину и масштаб вашему приложению. Дополнительные сведения см. в разделе [Обзор системы проектирования Fluent для UWP](../fluent-design-system/index.md).

## <a name="how-it-works-in-a-user-interface"></a>Как это работает в пользовательском интерфейсе

В пользовательском интерфейсе можно создать эффекта параллакса, перемещая различные объекты с разной скоростью при горизонтальной или вертикальной прокрутке. <!-- Parallax is an important tool in adding depth to applications along with other techniques like transition animations, perspective tilt, and layering. --> Чтобы продемонстрировать это, давайте рассмотрим пример с двумя уровнями содержимого: списком и фоновым изображением.  Список размещается поверх фонового изображения, создавая ощущение, что список находится ближе к зрителю.  Для достижения эффекта параллакса нужно, чтобы объект, расположенный ближе к нам, двигался "быстрее" расположенного дальше.  Когда пользователь прокручивает интерфейс, список перемещается быстрее относительно фонового изображения, что создает иллюзию глубины.

 ![Пример эффекта параллакса со списком и фоновым изображением](images/_Parallax_v2.gif)

 
## <a name="using-the-parallaxview-control-to-create-a-parallax-effect"></a>Использование элемента управления ParallaxView для создания эффекта параллакса

Для создания эффекта параллакса используется элемент управления [ParallaxView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Parallaxview). Он привязывает положение прокрутки элемента переднего плана, например списка, к фоновому элементу, например изображению. При прокрутке элемента переднего плана он анимирует фоновый элемент, создавая эффект параллакса. 

Чтобы использовать элемент управления ParallaxView, выберите элемент Source, фоновый элемент и установите для свойств [VerticalShift](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Parallaxview.VerticalShift) (для вертикальной прокрутки) и/или [HorizontalShift](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Parallaxview.HorizontalShift) (для горизонтальной прокрутки) значения выше нуля. 
* Свойство Source принимает ссылку на элемент переднего плана. Чтобы добиться эффекта параллакса, объектом переднего плана должен быть объект [ScrollViewer](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer) или элемент, содержащий объект ScrollViewer, например [ListView](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.listview) или [RichTextBox](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.RichEditBox). 

* Чтобы задать фоновый элемент, добавьте его в качестве дочернего элемента управления ParallaxView. Фоновым элементом может быть любой объект [UIElement](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.uielement), например [изображение](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.Image) или панель, содержащая дополнительные элементы пользовательского интерфейса. 

Для создания эффекта параллакса объект ParallaxView должен располагаться за элементом переднего плана. Панели [Grid](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.grid) и [Canvas](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.canvas) дают возможность располагать элементы слоями один над другим, поэтому они подходят для использования с элементом управления ParallaxView.  

В этом примере показано, как создать эффект параллакса для списка:
 
```xaml
<Grid>
    <ParallaxView Source="{x:Bind ForegroundElement}" VerticalShift="50"> 
    
        <!-- Background element --> 
        <Image x:Name="BackgroundImage" Source="Assets/turntable.png"
               Stretch="UniformToFill"/>
    </ParallaxView>
    
    <!-- Foreground element -->
    <ListView x:Name="ForegroundElement">
       <x:String>Item 1</x:String> 
       <x:String>Item 2</x:String> 
       <x:String>Item 3</x:String> 
       <x:String>Item 4</x:String> 
       <x:String>Item 5</x:String>  
       <x:String>Item 6</x:String> 
       <x:String>Item 7</x:String> 
       <x:String>Item 8</x:String> 
       <x:String>Item 9</x:String> 
       <x:String>Item 10</x:String>     
       <x:String>Item 11</x:String> 
       <x:String>Item 13</x:String> 
       <x:String>Item 14</x:String> 
       <x:String>Item 15</x:String> 
       <x:String>Item 16</x:String>     
       <x:String>Item 17</x:String> 
       <x:String>Item 18</x:String> 
       <x:String>Item 19</x:String> 
       <x:String>Item 20</x:String> 
       <x:String>Item 21</x:String>        
    </ListView>
</Grid>
``` 

ParallaxView автоматически подстраивает размер изображения для операции параллакса, так что вы можете не беспокоиться, что при прокрутке изображение выйдет из поля зрения.

## <a name="customizing-the-parallax-effect"></a>Настройка эффекта параллакса 

Свойства VerticalShift и HorizontalShift позволяют контролировать степень эффекта параллакса.

* Свойство VerticalShift указывает, насколько фон должен сдвинуться по вертикали за всю операцию параллакса. Значение 0 означает, что фон не перемещается.
* Свойство HorizontalShift указывает, насколько фон должен сдвинуться по горизонтали за всю операцию параллакса. Значение 0 означает, что фон не перемещается.

Чем выше значение, тем сильнее эффект. 

Полный перечень способов настройки эффекта параллакса приведен в разделе "Класс ParallaxView". 

## <a name="dos-and-donts"></a>Рекомендации

- Используйте параллакс для списков с фоновым изображением.
- Можно использовать эффект параллакса в ListViewItems, если там содержится изображение.
- Не используйте его везде, злоупотребление может ослабить впечатление.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

- [Класс ParallaxView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Parallaxview) 
- [Система проектирования Fluent для UWP](../fluent-design-system/index.md)
- [Наука системы: система проектирования Fluent и глубина](https://medium.com/microsoft-design/science-in-the-system-fluent-design-and-depth-fb6d0f23a53f)
