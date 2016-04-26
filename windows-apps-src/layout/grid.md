---
label: Grid
template: detail.hbs
extraBodyClass: layout-grid
brief: With its rigorous hierarchies and geometry, the grid orients us. It tells us what’s important and what can wait. As people become comfortable with reductive, flat design, the grid can be more abstract, with fewer cues and signposts. The explicit grid starts to fade, leaving behind the elegant relationships between its elements.
---

# Базовая единица

Сетка для Windows состоит из единиц 4×4 пикселя. Речь идет об **эффективных пикселях**, а не физических. Элементы в макетах всегда масштабируются шагами по 4 пикселя. Это формирует знакомый ритм, обеспечивающий равновесие и связность.

![Отображает 4-пиксельную сетку](assets/grid/grid.png)

# Эффективные пиксели

Эффективный пиксель — это виртуальная единица измерения. Она используется для обозначения измерений макета и интервалов вне зависимости от плотности пикселей экрана (это измерение также известно под названием "точек на дюйм" или DPI). Измерение в эффективных пикселях позволяет сконцентрироваться на фактическом воспринимаемом размере элементов пользовательского интерфейса, не беспокоясь о плотности пикселей устройства, на котором выполняется приложение.

В большинстве случаев под названием "пкс" мы подразумеваем эффективные пиксели, потому что макеты никогда не измеряются в физических пикселях.

<video class="video-responsive" controls>
    <source src="assets/grid/epx.mp4" type="video/mp4" />
    Oops! Your browser doesn't seem to support this video. Sorry about that.
</video>

<aside class="aside-dev">
    <div class="aside-dev-title">
        Developer Notes
    </div>
    <div class="aside-dev-content">
            Although XAML doesn't specify units for its widths, heights, margins, and padding, they are all implicitly measured in effective pixels.
    </div>
</aside>

# Целевые объекты сенсорного ввода

48×48 пкс — это оптимальный размер элемента, предназначенного для касания. Тем не менее при наличии ограничений или в случае, если элемент используется не очень часто, его размер можно уменьшить до 44×44 пкс. Если требуется объект меньшей высоты, можно использовать формат 32×120 пкс, однако он подходит только для настольных ПК и компьютеров "два в одном".

![Показывает целевой объект сенсорного ввода размером 48×48 пкс](assets/grid/touch-target.png)


<!--HONumber=Mar16_HO4-->


