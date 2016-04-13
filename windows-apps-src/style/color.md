---
Description: Цвета позволяют интуитивно ориентироваться среди различных информационных уровней приложения и служат важнейшим средством для усиления модели взаимодействия.
title: Цвет
ms.assetid: 3ba7176f-ac47-498c-80ed-4448edade8ad
label: Color
template: detail.hbs
extraBodyClass: style-color
brief: Color provides intuitive wayfinding through an app's various levels of information and serves as a crucial tool for reinforcing the interaction model.<br /><br />In Windows, color is also personal. Users can choose a color and a light or dark theme to be reflected throughout their experience.
---

# Цвет для приложений UWP
Цвета позволяют интуитивно ориентироваться среди различных информационных уровней приложения и служат важнейшим средством для усиления модели взаимодействия.

## Контрастный цвет

Пользователь может выбрать один цвет, называемый контрастным. Выбор представлен проверенным набором из 48 цветовых палитр.


<!-- Alternate version for the dev center. Need to add hex values. -->
<figure>
![Accent colors](images/accentcolorswatch.png)
<figcaption>Общее правило таково: если исходный контрастный цвет используется в качестве фона, размещайте поверх него текст белого цвета.</figcaption>
</figure>

Когда пользователи выбирают контрастный цвет, он появляется в составе системной темы. Затрагиваются следующие области: меню "Пуск", панель задач, хром окон, определенные состояния взаимодействия и гиперссылки в [общих элементах управления](https://dev.windows.com/design/controls-patterns). Контрастный цвет может еще шире использоваться в каждом приложении за счет оформления, фонов и взаимодействий или переопределяться ради сохранения фирменной символики приложения.

## Выбор цвета

После выбора цвета создаются его светлые и темные оттенки на основании значений яркости цвета в списке совместимого оборудования. Приложения могут использовать варианты затенения для создания визуальной иерархии и предоставления указания на взаимодействие.

![Один контрастный цвет с 6 вариантами затенения](images/shades.png)

<aside class="aside-dev">
    <div class="aside-dev-title">
    </div>
    <div class="aside-dev-content">
            In XAML, the accent color is exposed as a [theme resource](https://msdn.microsoft.com/en-us/library/windows/apps/Mt187274.aspx) named `SystemAccentColor`. It's also available programmatically from [UISettings.GetColorValue](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.viewmanagement.uisettings.getcolorvalue.aspx). You can programmatically access the different shades from [UISettings.GetColorValue](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.viewmanagement.uisettings.getcolorvalue.aspx), see the [UIColorType](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.viewmanagement.uicolortype.aspx) enum.
    </div>
</aside>

## Цветовые темы

Пользователь также может выбрать светлую или темную тему для системы (только на телефоне; эта функция пока не доступна на планшетах и настольных компьютерах — на них возможна настройка в приложении). В некоторых приложениях тема изменяется в зависимости от предпочтений пользователя, а в некоторых такой возможности нет.

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
            Each color is available as a XAML [theme resource](https://msdn.microsoft.com/en-us/library/windows/apps/Mt187274.aspx#the_xaml_color_ramp_and_theme-dependent_brushes) that follows the `System*Color` naming convention (ex: `SystemChromeHighColor`). You can control your app's theme through either [Application.RequestedTheme](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.application.requestedtheme.aspx) or [FrameworkElement.RequestedTheme](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.frameworkelement.requestedtheme.aspx).
    </div>
</aside>

## Специальные возможности

Наша палитра оптимизирована для различных экранов. Мы рекомендуем поддерживать минимальный коэффициент контрастности текста на уровне 4,5:1 для максимального удобства при чтении.


<!--HONumber=Mar16_HO5-->


