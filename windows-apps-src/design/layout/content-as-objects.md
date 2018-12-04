---
description: ''
title: Содержимое в виде объектов
template: detail.hbs
ms.localizationpriority: medium
ms.openlocfilehash: 37ba5093f2d7cfe268be40413b889801daf00967
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8483989"
---
# <a name="content-as-objects"></a>Содержимое в виде объектов

 

Вы можете управлять глубиной или z-порядком элементов для создания визуальной иерархии, которая помогает упростить использование вашего приложения.  

> Примечание. Это предварительная версия статьи для новой функции в Windows 10 RS2. Названия компонентов, терминология и функциональные возможности могут меняться. 

## <a name="why-visual-hierarchy-is-important"></a>Почему важна визуальная иерархия

Пользователи постоянно сталкиваются с многочисленными попытками привлечь их внимание. Каждый элемент на экране пытается привлечь взгляд, каждая строка текста предназначена для чтения, а кнопка — для нажатия. По мере того, как визуальная среда становится все более беспорядочной и хаотичной, требуется все боле усилий, чтобы проанализировать сцену и понять, что происходит.  

Вот почему так важно тщательно выбирать элементы пользовательского интерфейса и создавать макеты, которые устанавливают четкую визуальную иерархию между элементами пользовательского интерфейса. <!-- Every element is competing for the user's attention, and every time you add an element, you add a mental tax to the user. -->

Четкая визуальная иерархия сообщает пользователю, какие элементы наиболее важны, и создает отношения между элементами. Благодаря хорошей визуальной иерархии пользователи с первого взгляда понимают макет страницы и могут сосредоточиться на задачу, им необходимо выполнить. 

<p></p>


<div class="side-by-side">
<div class="side-by-side-content">
  <div class="side-by-side-content-left">
  <p>Как же создать четкую визуальную иерархию? В более ранних версиях Windows 10 для создания визуальной иерархии можно было использовать пустое пространство, позиционирование и оформление текста. </p>
  </div>
  <div class="side-by-side-content-right">
    ![Плоский макет](images/content-as-objects/flat-layout.png)
    
  </div>
</div>
</div>

В Windows 10 RS2 мы буквально добавили еще одно измерение — глубину. 

![Глубина в макете](images/content-as-objects/depth-in-layout2.png)


## <a name="use-depth-to-establish-a-hierarchy"></a>Использование глубины для создания иерархии 

<p></p>

<div class="side-by-side">
<div class="side-by-side-content">
  <div class="side-by-side-content-left">
     <p>Глубину (z-порядок) можно использовать вместе с другими инструментами разработки (пустым пространством, позиционированием и шрифтовым оформление) для создания иерархии. Расположите наиболее важные элементы на самом близком уровне и используйте следующие уровни для отображения менее важных элементов интерфейса. 

    The relative importance of an element can change throughout an experience, so you can bring elements forward as they become more important and backward as they become less important. 
    </p>
  </div>
  <div class="side-by-side-content-right">
    ![Глубина в макете](images/content-as-objects/elements-forward-backward.png) 
    
  </div>
</div>
</div>

## <a name="how-does-it-work"></a>Как это работает?
> TODO: краткое описание способов управления z-порядком элементов. Следует ли жестко кодировать z-порядок или существует система семантического ранжирования? Как элементы перемещаться с одного уровня на другой? Что система делает автоматически, а о чем следует позаботиться проектировщикам и разработчикам? 

## <a name="the-four-layers-of-a-typical-app-layers"></a>Четыре уровня стандартного приложения

<p>Обычное приложение имеет четыре уровня.</p>
<p></p>

<div class="side-by-side">
<div class="side-by-side-content">
  <div class="side-by-side-content-left">
  **За фоном** <br/>
Этот уровень находится за приложением.  При перемещении элементов на этот уровень рекомендуется делать их неинтерактивными. Элементы на этом уровне имеют самый медленный параллакс и обрезаются до размера окна приложения. TODO: масштабируется ли этот уровень? 

<p>Примером фоновых элементов могут служить изображения, расположенные за содержимым. TODO: пример, TODO: пример.</p>
  </div>
  <div class="side-by-side-content-right">
    ![Уровень приложения "за фоном"](images/content-as-objects/elements-forward-backward.png)
    
  </div>
</div>
</div>

<p></p>

<div class="side-by-side">
<div class="side-by-side-content">
  <div class="side-by-side-content-left">
  **Пассивный уровень** <br/>
Это базовый уровень приложения, на котором элементы пользовательского интерфейса располагаются по умолчанию.  Элементы перемещаются по этому уровню в режиме реального времени (без параллакса), обрезаются по размеру окна приложения и отображаются в масштабе 100%. 

<p>Пример элементов: фон приложения, текст, дополнительные элементы пользовательского интерфейса, такие как элементы навигации по приложению.</p>
  </div>
  <div class="side-by-side-content-right">
    ![Пассивный уровень приложения](images/content-as-objects/elements-forward-backward.png)
    
  </div>
</div>
</div>

<p></p>

<div class="side-by-side">
<div class="side-by-side-content">
  <div class="side-by-side-content-left">
  **Призывы к действию** <br/>
Этот уровень предназначен для интерактивных элементов, которые имеют приоритет по сравнению с элементами пассивного уровня. Элементы на этом уровне имеют средний параллакс и обрезаются до размера окна приложения. TODO: масштабируются ли элементы этого уровня и имеют ли они тень?

<p>Пример элементов: списки, сетки, основные команды (TODO: например...).</p> 
  </div>
  <div class="side-by-side-content-right">
    ![Уровень приложения "призывы к действию"](images/content-as-objects/elements-forward-backward.png)
    
  </div>
</div>
</div>

<p></p>
<div class="side-by-side">
<div class="side-by-side-content">
  <div class="side-by-side-content-left">
  **Главный уровень** <br/>
Этот уровень предназначен для элементов с максимальным приоритетом из тех, что находятся в данный момент на экране.  Элементы на этом уровне могут выходить за границы окна приложения, могут масштабироваться и автоматически получают тени.

<p>Примеры элементов: фотографии, выбранное элементы.</p>  
  </div>
  <div class="side-by-side-content-right">
    ![Главный уровень приложения](images/content-as-objects/elements-forward-backward.png)
    
  </div>
</div>
</div>



<!--
Depth is meaningful; it establishes visual and interactive hierarchy for users to efficiently complete tasks. Depth orients users in our system. 
-->

## <a name="example-tbd"></a>Пример: TBD
> TODO: показать, как адаптировать стандартный шаблон пользовательского интерфейса для использования z-упорядочивания. Необходимо показать иллюстрации и код. 

## <a name="download-the-code-samples"></a>Загрузка примеров кода
>TODO: ссылки на примеры, демонстрирующие эту функцию. 


## <a name="related-articles"></a>Статьи по теме
* [Основные сведения о содержимом](../basics/content-basics.md)
