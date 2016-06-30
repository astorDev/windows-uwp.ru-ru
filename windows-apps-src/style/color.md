---
author: mijacobs
Description: "Цвета позволяют интуитивно ориентироваться среди различных информационных уровней приложения и служат важнейшим средством для усиления модели взаимодействия."
title: "Цвет"
ms.assetid: 3ba7176f-ac47-498c-80ed-4448edade8ad
label: Color
template: detail.hbs
extraBodyClass: style-color
translationtype: Human Translation
ms.sourcegitcommit: a4e9a90edd2aae9d2fd5d7bead948422d43dad59
ms.openlocfilehash: 878470a7cbf44862c47a1428a1d25d332db32fdc

---

# Цвет

Цвета позволяют интуитивно ориентироваться среди различных информационных уровней приложения и служат важнейшим средством для усиления модели взаимодействия.

В Windows цвет также отражает личные предпочтения. Пользователи могут выбрать цвет и светлую либо темную тему, чтобы она проявлялась при каждом их взаимодействии с устройством.

## Цвет элементов

Пользователь может выбрать один цвет, который относится к элементам, в меню *Параметры > Персонализация > Цвета*. Они могут выбирать из 48 специально подобранных наборов цветов, однако на Xbox выбор ограничен палитрой из 21 безопасного для ТВ цвета.

<!-- Alternate version for the dev center. Need to add hex values. -->
![Цвета элементов по умолчанию](images/accentcolorswatch.png) Цвета элементов по умолчанию

![Цвета элементов Xbox](images/accentcolorswatch_xbox.png) Цвета элементов Xbox



Когда пользователи выбирают цвет элементов, он появляется в составе системной темы. Затрагиваются следующие области: меню "Пуск", панель задач, хром окон, определенные состояния взаимодействия и гиперссылки в [общих элементах управления](https://dev.windows.com/design/controls-patterns). Цвет элементов может еще шире использоваться в каждом приложении за счет оформления, фонов и взаимодействий или переопределяться ради сохранения фирменной символики приложения.

## Цвет на цвете

После выбора цвета элементов создаются его светлые и темные оттенки на основании значений яркости цвета согласно модели HSB. Приложения могут использовать варианты затенения для создания визуальной иерархии и предоставления указания на взаимодействие.

По умолчанию для гиперссылок используется выбранный пользователем цвет элементов. Если фон страницы имеет схожий цвет, можно назначить более светлый (или более темный) оттенок гиперссылкам для повышения контрастности.

<figure class="figure-img" >
    <img src="images/shades.png" alt="A single accent color with its 6 shades"  />
        <figcaption><p>Различные светлые и темные оттенки цвета элементов по умолчанию.</p>
</figcaption>
</figure>

<figure class="figure-img" >
    <img src="images/action_center_redline_zoom.png" alt="Redlines for Colored Action Center"  />
        <figcaption><p>Пример работы логики цвета в спецификации на дизайн.</p>
</figcaption>
</figure>

<aside class="aside-dev">
    <div class="aside-dev-title">
    </div>
    <div class="aside-dev-content">
В XAML основной цвет элементов рассматривается как [ресурс темы](https://msdn.microsoft.com/library/windows/apps/Mt187274.aspx) под названием `SystemAccentColor`. Оттенки доступны в виде `SystemAccentColorLight3`, `SystemAccentColorLight2`, `SystemAccentColorLight1`, `SystemAccentColorDark1`, `SystemAccentColorDark2` и `SystemAccentColorDark3`. Также возможен программный доступ с помощью [UISettings.GetColorValue](https://msdn.microsoft.com/library/windows/apps/windows.ui.viewmanagement.uisettings.getcolorvalue.aspx) и перечисления [UIColorType](https://msdn.microsoft.com/library/windows/apps/windows.ui.viewmanagement.uicolortype.aspx).
    </div>
</aside>

## Цветовые темы

Пользователь также может выбрать светлую или темную тему для системы. В некоторых приложениях тема изменяется в зависимости от предпочтений пользователя, а в некоторых такой возможности нет.

Светлая тема используется в офисных приложениях. Примером может послужить набор приложений Microsoft Office. Светлая тема упрощает чтение длинного текста и выполнение длительных задач.

Темная тема повышает контрастность содержимого в приложениях, связанных с мультимедиа, а также в тех случаях, когда пользователям приходится просматривать множество видео и изображений. В этих сценариях основная задача далеко не всегда связана с чтением, в то время как удобство просмотра фильмов при слабом освещении может быть крайне важным.

Если эти описания не подходят вашему приложению, вы можете использовать системную тему, чтобы пользователи сами выбирали подходящий им вариант.

Чтобы упростить проектирование с учетом тем, Windows предоставляет дополнительную цветовую палитру, которая автоматически адаптируется под тему.

<!-- OP version -->
### Светлая тема
#### Базовая
![Базовая светлая тема](images/themes-light-base.png)
#### Альтернативная
![Альтернативная светлая тема](images/themes-light-alt.png)
#### Список
![Светлая тема списков](images/themes-light-list.png)
#### Хром
![Светлая тема хрома](images/themes-light-chrome.png)
### Темная тема
#### Базовая
![Базовая темная тема](images/themes-dark-base.png)
#### Альтернативная
![Альтернативная темная тема](images/themes-dark-alt.png)
#### Список
![Темная тема списков](images/themes-dark-list.png)
#### Хром
![Темная тема хрома](images/themes-dark-chrome.png)

<aside class="aside-dev">
    <div class="aside-dev-title">
    </div>
    <div class="aside-dev-content">
Каждый цвет доступен как [ресурс темы](https://msdn.microsoft.com/library/windows/apps/Mt187274.aspx#the_xaml_color_ramp_and_theme-dependent_brushes) XAML, выполняющий условия именования `System*Color` (например `SystemChromeHighColor`). Вы можете управлять темой своего приложения с помощью либо [Application.RequestedTheme](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.requestedtheme.aspx), либо [FrameworkElement.RequestedTheme](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.requestedtheme.aspx).
    </div>
</aside>

## Специальные возможности

Наша палитра оптимизирована для различных экранов. Мы рекомендуем поддерживать минимальный коэффициент контрастности текста к фону на уровне 4,5:1 для максимального удобства при чтении. Существует множество бесплатных средств для проверки соответствия выбранных вами цветов этим требованиям, например [Contrast Ratio](http://leaverou.github.io/contrast-ratio/).



<!--HONumber=Jun16_HO4-->


