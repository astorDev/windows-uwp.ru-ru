---
Description: Создавайте приложения Windows с интуитивно понятными и удобными интерфейсами взаимодействия с пользователем, которые оптимизированы для сенсорного ввода, но функционально согласованы на всех входных устройствах.
title: Взаимодействия с помощью сенсорного ввода
ms.assetid: DA6EBC88-EB18-4418-A98A-457EA1DEA88A
label: Touch interactions
template: detail.hbs
keywords: сенсорный ввод, указатель, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 99926de0ea26b0cef9184756add777a39330d934
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970259"
---
# <a name="touch-interactions"></a>Взаимодействия с помощью сенсорного ввода


Проектируйте приложение с учетом того, что сенсорный ввод будет главным методом ввода для пользователей. Если вы используете элементы управления UWP, то для поддержки сенсорной панели, мыши и пера не требуется дополнительное программирование, поскольку приложения UWP предоставляют такую поддержку бесплатно.

Однако имейте в виду, что пользовательский интерфейс, оптимизированный для сенсорного ввода, не всегда будет лучше традиционного интерфейса. И тот и другой имеют свои достоинства и недостатки в технологии и применении. При переходе к пользовательскому интерфейсу Touch важно понимать основные различия между сенсорным касанием, сенсорной панелью, пером, планшетом и вводом с клавиатуры.

> **Важные API-интерфейсы**: [**Windows.UI.Xaml.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input), [**Windows.UI.Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Core), [**Windows.Devices.Input**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input)


Многие устройства оснащены мультисенсорными экранами, которые поддерживают использование одного или нескольких пальцев (либо сенсорных контактов) для ввода данных. Сенсорные контакты и их перемещение интерпретируются как сенсорные жесты и манипуляции для поддержки различных видов взаимодействия с пользователем.

Приложение Windows включает ряд различных механизмов обработки сенсорного ввода, позволяя создавать впечатляющие впечатления, которые пользователи могут исследовать с уверенностью. Здесь мы расскажем об основах использования сенсорного ввода в приложении Windows.

Для сенсорного взаимодействия требуются 3 вещи:

-   Сенсорный дисплей.
-   Прямое прикосновение (или близкое взаимодействие, если дисплей снабжен бесконтактными датчиками и поддерживает обнаружение наведения) из одного или нескольких пальцев к экрану.
-   Перемещение точек контакта (или отсутствие такового — в зависимости от порогового значения времени).

Входные данные, выводимые датчиком прикосновения, могут:

-   интерпретироваться как физический жест для непосредственного взаимодействия с одним или несколькими элементами пользовательского интерфейса (например, для сдвига, поворота, изменения размера или перемещения); в противоположность этому взаимодействие с элементом через его окно свойств, диалоговое окно или другую возможность пользовательского интерфейса называется косвенной манипуляцией;
-   распознаваться как альтернативный метод ввода, такой как мышь или перо;
-   использоваться для дополнения или изменения различных аспектов других методов ввода, например размытие росчерка пера.

Сенсорный ввод обычно предусматривает прямую манипуляцию элементом на экране. Элемент немедленно отвечает на любой сенсорный контакт в рамках своей области проверки и реагирует соответственно на любое последующее движение сенсорных контактов, в том числе удаление.

Нестандартные жесты сенсорного ввода и взаимодействия следует разрабатывать с особой аккуратностью. Они должны быть интуитивно понятными и обнаруживаемыми, иметь быстрый отклик и обеспечивать уверенность пользователя во время работы с вашим приложением.

Убедитесь, что функции приложения предоставляются согласованно на всех поддерживаемых типах устройств ввода. При необходимости используйте какую-либо форму режима косвенного ввода, например текстовый ввод для взаимодействий с помощью клавиатуры или возможности пользовательского интерфейса для мыши и пера.

Помните, что традиционные устройства ввода (такие как мышь и клавиатура) знакомы и привычны множеству пользователей. Они могут обеспечивать скорость работы, точность и тактильную обратную связь, недостижимые для сенсорного управления.

Предоставление уникальных и разнообразных процедур взаимодействия для всех устройств ввода обеспечит поддержку максимально широкого диапазона возможностей и параметров, благодаря чему ваши приложения будут иметь успех у самой широкой аудитории, привлекая к себе больше клиентов.

## <a name="compare-touch-interaction-requirements"></a>Сравнение требований, предъявляемых к взаимодействию с сенсорным экраном

В следующей таблице показаны некоторые различия между устройствами ввода, которые следует учитывать при проектировании оптимизированных для сенсорных приложений Windows.

<table>
<tbody><tr><th>Фактор</th><th>Взаимодействия с помощью сенсорного ввода</th><th>Взаимодействие с помощью мыши, клавиатуры, пера</th><th>Touchpad</th></tr>
<tr><td rowspan="3">Точность</td><td>Контактная поверхность кончиков пальцев больше, чем отдельная пара координат X-Y, поэтому увеличивается вероятность случайного выполнения команд.</td><td>Мышь и перо передают точные координаты X-Y.</td><td>Аналогично мыши.</td></tr>
<tr><td>Форма контактной поверхности изменяется при движении.  </td><td>Перемещения мыши и пера передают точные координаты X-Y. Клавиатурная фокусировка определена явно.</td><td>Аналогично мыши.</td></tr>
<tr><td>Отсутствует указатель мыши для нацеливания.</td><td>Указатель мыши, указатель пера и клавиатурная фокусировка позволяют выполнить нацеливание.</td><td>Аналогично мыши.</td></tr>
<tr><td rowspan="3">Анатомия человека</td><td>Движения кончиков пальцев являются неточными, так как прямолинейное перемещение одного или нескольких пальцев затруднено из-за изгиба суставов кисти и одновременного участия в движении нескольких суставов.</td><td>Мышью или пером прямолинейное движение выполняется легче, так как кисть руки передвигается на расстояние, которое значительно короче, чем перемещение курсора на экране.</td><td>Аналогично мыши.</td></tr>
<tr><td>Некоторые участки на поверхности сенсорного экрана могут быть труднодоступны при определенных положениях пальцев и захвате устройства пользователем.</td><td>Указатель мыши или перо может достигать любой части экрана, и при этом любой элемент управления должен быть доступен с клавиатуры с помощью последовательности табуляции. </td><td>Проблемой может стать расположение пальцев и руки, держащей устройство.</td></tr>
<tr><td>Объекты могут заслоняться одним или несколькими пальцами или кистью руки. Это называется загораживанием экрана.</td><td>Устройства ввода непрямого действия не вызывают загораживание экрана.</td><td>Аналогично мыши.</td></tr>
<tr><td>Состояние объекта</td><td>Для сенсорного ввода характерны два состояния: сенсорная поверхность дисплея может либо соприкасаться с рукой (активна), либо нет (не активна). Состояние при наведении, при котором может быть инициирована дополнительная визуальная обратная связь, отсутствует.</td><td>
<p>Для мыши, пера и клавиатуры существуют три состояния: верхнее (неактивное), нижнее (активное) и при наведении (фокусное).</p>
<p>Состояние при наведении позволяет изучить элементы пользовательского интерфейса с помощью всплывающих подсказок. Также оно помогает понять, какие объекты доступны для взаимодействия, и выполнить нацеливание. 
</p>
</td><td>Аналогично мыши.</td></tr>
<tr><td rowspan="2">Сложное взаимодействие</td><td>Поддерживается мультисенсорная технология: множественные точки ввода (кончиками пальцев) на поверхности сенсорного экрана.</td><td>Поддерживается единственная точка ввода.</td><td>Аналогично сенсорному вводу.</td></tr>
<tr><td>Поддерживается прямое манипулирование объектами с помощью таких операций, как касание, перетаскивание, скольжение, изменение размера и вращение.</td><td>Прямая манипуляция не поддерживается, так как мышь, перо и клавиатура являются устройствами ввода непрямого действия.</td><td>Аналогично мыши.</td></tr>
</tbody></table>

> [!NOTE]
> Преимущество непрямого ввода состоит в том, что он совершенствовался более 25 лет. Такие функции, как всплывающие при наведении подсказки, были разработаны с целью облегчить работу с пользовательским интерфейсом специально для ввода с помощью сенсорной панели, мыши, пера и клавиатуры. Подобные элементы пользовательского интерфейса были переработаны, чтобы обогатить взаимодействие путем использования сенсорного ввода, не нарушая взаимодействие с пользователем с помощью других устройств.

 

## <a name="use-touch-feedback"></a>Использование обратной связи сенсорного ввода

Надлежащая визуальная обратная связь при взаимодействии с вашим приложением помогает пользователям узнать, изучить и приспособиться к тому, как их взаимодействия интерпретируются самим приложением и платформой Windows. Визуальная обратная связь может показать успешные взаимодействия, определить состояние системы, улучшить чувствительность системы управления, сократить количество ошибок, помочь пользователям понять систему и устройство ввода, а также поощрять взаимодействие с системой.

Визуальная обратная связь особенно важна, когда сенсорный ввод применяется для операций, которые требуют правильного и точного задания расположения. Отображение обратной связи всякий раз, когда обнаруживается сенсорный ввод, позволит пользователю понять правила наведения, которые действуют в вашем приложении и его элементах управления.


## <a name="targeting"></a>Целевая настройка

Для оптимизации нацеливания используются:

-   Размеры целей касания

    Четкие рекомендации по размерам позволяют оснастить приложения удобным пользовательским интерфейсом, с объектами и элементами управления которого легко и безопасно работать.

-   Форма области контакта

    Наиболее вероятный целевой объект определяется по всей контактной области пальца.

-   Проведение

    Чтобы заново нацелиться на элементы группы (например, переключатели), проведите между ними пальцем. Текущий элемент активируется, когда касание прекращается.

-   Качание

    Чтобы заново нацелиться на близко расположенные элементы (например, гиперссылки), нажмите их пальцем и, не давая соскользнуть, покачайте его туда-сюда над элементами. Так как элементы загораживаются, текущий элемент определяется по подсказке или строке состояния; он активируется при прекращении касания.

## <a name="accuracy"></a>Точность

Для проектирования обработки небрежных касаний используются:

-   Точки прикрепления, которые помогут остановиться в нужном месте при взаимодействии с содержимым.
-   Направляющие "рельсы", облегчающие вертикальный или горизонтальный сдвиг, даже если рука движется слегка по дуге. Дополнительные сведения см. в [Руководстве по функции сдвига](guidelines-for-panning.md).

## <a name="occlusion"></a>Загораживание

Чтобы пальцы и рука не загораживали элементы, сделайте следующее:

-   Размер и размещение пользовательского интерфейса

    Делайте элементы пользовательского интерфейса достаточно большими, чтобы контактная область пальца не закрывала их полностью.

    Где возможно, размещайте меню и всплывающие окна над областью контакта.

-   Всплывающие подсказки

    Показывайте подсказки, когда пользователь удерживает палец на объекте. Это удобно для описания функций объекта. Пользователь может сдвинуть палец с объекта, чтобы не открывать подсказку.

    Если объекты небольшие, сместите подсказки так, чтобы контактная область пальца их не закрывала. Это удобно при нацеливании.

-   Маркеры для точности

    Там, где нужна точность (например, при выборе текста), обеспечьте маркеры выделения, смещенные в целях повышения точности. Подробнее: [Руководство по выделению текста и изображений (приложения среды выполнения Windows)](guidelines-for-textselection.md).

## <a name="timing"></a>Временные свойства

Вместо смены режима по времени пользуйтесь прямой манипуляцией. Прямая манипуляция имитирует непосредственное физическое воздействие на объект в реальном времени. Объект реагирует на перемещение пальцев.

С другой стороны, после взаимодействия с сенсорным экраном происходит реакция, основанная на времени. Обычно взаимодействия на основе времени зависят от неочевидных пороговых величин: времени, расстояния или скорости, определяющих, какая команда будет выполнена. Основанные на времени взаимодействия не имеют визуальной обратной связи, пока в системе не будет выполнено действие.

Прямое манипулирование обеспечивает ряд преимуществ по сравнению с взаимодействиями, основанными на времени.

-   Мгновенная визуальная обратная связь при взаимодействии повышает заинтересованность пользователей и укрепляет уверенность в себе.
-   Прямое манипулирование обратимо, поэтому позволяет без лишнего риска исследовать систему. Пользователи легко могут отменять свои действия шаг за шагом. Для этого используются логические и интуитивно понятные приемы.
-   Взаимодействия, непосредственно влияющие на объекты и имитирующие то, что происходит в реальности, легче обнаружить, понять и запомнить. Они не связаны с неочевидными или абстрактными взаимодействиями.
-   Основанные на времени взаимодействия могут быть трудны для выполнения, так как пользователи должны достигать произвольных и невидимых пороговых значений.

Кроме того, настоятельно рекомендуется следующее.

-   Манипуляции не следует различать по количеству используемых пальцев.
-   Взаимодействия должны поддерживать сложные манипуляции. Например, пользователь может свести пальцы, чтобы изменить размер содержимого, и одновременно провести ими, чтобы сдвинуть его.
-   Взаимодействия не должны различаться по времени. Одно и то же взаимодействие должно приводить к определенному результату, сколько бы времени ни потребовалось на его выполнение. Зависимые от времени активации вводят обязательные задержки для пользователей, в результате чего обесценивается иммерсивный характер прямого манипулирования и снижается скорость восприятия ответа системы.

   > [!NOTE]
   > Исключением является то, что вы используете определенные временные взаимодействия для обучения и исследования (например, для нажатия и удерживания).

     

-   Подходящие описания и визуальные подсказки очень эффективны при использовании расширенных взаимодействий.


## <a name="app-views"></a>Представления приложения


Оптимизируйте взаимодействие с пользователем, настроив параметры сдвига, прокрутки и масштабирования в представлениях ваших приложений. Представление приложения контролирует то, как пользователь выполняет доступ к вашему приложению с его содержимым и управляет ими. Представления также обеспечивают различное поведение, например инерцию, отскок от границы содержимого, точки прикрепления.

Параметры сдвига и прокрутки элемента управления [**ScrollViewer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer) управляют навигацией в рамках отдельного представления, когда его содержимое не умещается в одном окне просмотра. Отдельным представлением может быть, например, страница журнала или книги, структура папок на компьютере, библиотека документов или фотоальбом.

Параметры масштабирования относятся как к визуальному масштабированию (поддерживается элементом управления [**ScrollViewer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer)), так и к элементу управления [**Контекстное масштабирование**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom). Контекстное масштабирование — это оптимизированный для сенсорной технологии метод представления большого количества взаимосвязанных данных или содержимого в пределах отдельного представления и навигации по таким данным. В нем используются два различных режима классификации (масштаба). Это похоже на сдвиг и прокрутку в отдельном представлении. Сдвиг и прокрутка могут использоваться в связке с контекстным масштабированием.

Используйте представления и события приложений для изменения поведения сдвига или прокрутки и масштабирования. Таким образом можно обеспечить более плавное взаимодействие, чем при обработке событий указателя и жестов.

Подробнее о представлениях приложения см. в разделе [Элементы управления, макеты и текст](https://docs.microsoft.com/windows/uwp/design/basics/).

## <a name="custom-touch-interactions"></a>Настраиваемые сенсорные взаимодействия


Если вы реализуете собственную поддержку взаимодействий, помните: пользователи ожидают, что способ взаимодействия с элементами пользовательского интерфейса приложения будет интуитивно понятным. Рекомендуется моделировать взаимодействия с пользователем на базе библиотек элементов управления платформы, чтобы обеспечить единообразие элементов и возможность их обнаруживать. Элементы управления в этих библиотеках предоставляют все механизмы взаимодействия с пользователем, в том числе стандартные взаимодействия, анимированные физические эффекты, визуальную обратную связь и специальные возможности. Создавайте пользовательские взаимодействия, только если существует явное, четко определенное требование и если ни один стандартный механизм взаимодействия не поддерживает ваш сценарий.

Чтобы обеспечить настраиваемую поддержку сенсорного ввода, вы можете обрабатывать различные события [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement). Эти события относятся к трем разным уровням абстракции.

-   Статические события жестов срабатывают по завершении взаимодействия. События жестов включают в себя [**Tapped**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.tapped), [**DoubleTapped**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.doubletapped), [**RightTapped**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.righttapped) и [**Holding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.holding).

    Вы можете отключить события жестов для определенных элементов, установив для параметров [**IsTapEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.istapenabled), [**IsDoubleTapEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.isdoubletapenabled), [**IsRightTapEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.isrighttapenabled) и [**IsHoldingEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.isholdingenabled) значение **false**.

-   События указателя, такие как [**поинтерпрессед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed) и [**поинтермовед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointermoved) , предоставляют низкоуровневые сведения для каждого сенсорного контакта, включая перемещение указателя и возможность отличать события нажатия и выпуска.

    Указатель — это универсальный тип ввода с унифицированным механизмом событий. Он предоставляет базовую информацию, такую как экранные координаты, для активного источника ввода, которым может быть сенсорный экран, сенсорная панель, мышь или перо.

-   Манипуляционные события жестов, например [**ManipulationStarted**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationstarted), информируют о взаимодействии с пользователем, которое идет в данный момент. Они запускаются, когда пользователь касается элемента, и продолжаются, до тех пор пока пользователь не отведет пальцы или взаимодействие не отменится.

    События манипуляции включают в себя мультисенсорные взаимодействия, такие как масштабирование, сдвиг или поворот, и взаимодействия, использующие данные инерции и скорости, такие как перетаскивание. Информация, предоставляемая событиями манипуляций, не идентифицирует форму выполненного взаимодействия, а содержит данные, касающиеся, например, положения, дельты преобразования и скорости. Эти данные сенсорного ввода можно использовать для определения типа взаимодействия, которое необходимо выполнить.

Вот основной набор мультисенсорных жестов, поддерживаемых UWP.

| Название           | Type                 | Описание                                                                            |
|----------------|----------------------|----------------------------------------------------------------------------------------|
| Касание            | Статический жест       | Пользователь касается экрана одним пальцем, затем отводит палец.                                            |
| Нажатие и удерживание | Статический жест       | Пользователь прикасается к экрану одним пальцем и оставляет палец на месте.                                      |
| Slide (Слайд)          | Жест манипуляции | Пользователь прикасается к экрану одним или несколькими пальцами и двигает их в одном направлении.                   |
| Swipe          | Жест манипуляции | Пользователь прикасается к экрану одним или несколькими пальцами и перемещает их на короткое расстояние в одном направлении.  |
| Вращение           | Жест манипуляции | Пользователь прикасается к экрану двумя или несколькими пальцами и двигает их по дуге в направлении по часовой стрелке или против нее. |
| Сжатие          | Жест манипуляции | Пользователь прикасается к экрану двумя или более пальцами и сдвигает их.                         |
| Stretch        | Жест манипуляции | Пользователь прикасается к экрану двумя или более пальцами и раздвигает их.                           |

 

<!-- mijacobs: Removing for now. We don't have a real page to link to yet. 
For more info about gestures, manipulations, and interactions, see [Custom user interactions](custom-user-input-portal.md).
-->

## <a name="gesture-events"></a>События жестов


Подробнее об отдельных элементах управления см. в разделе [Список элементов управления](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/).

## <a name="pointer-events"></a>События указателя


События указателя вызываются различными активными источниками ввода, включая сенсорный экран, сенсорную панель, перо и мышь (они заменяют традиционные события мыши).

События указателя основаны на единственной точке ввода (палец, кончик пера, курсор мыши) и не поддерживают взаимодействия на основе обработки данных скорости.

Вот список событий указателя и их связанный аргумент события.

| Событие или класс                                                       | Описание                                                   |
|----------------------------------------------------------------------|---------------------------------------------------------------|
| [**PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed)             | Происходит при касании экрана одним пальцем.               |
| [**PointerReleased**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerreleased)           | Происходит, когда тот же сенсорный контакт прекращается.                |
| [**PointerMoved**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointermoved)                 | Происходит, когда указатель перетаскивается по экрану.         |
| [**PointerEntered**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerentered)             | Происходит, когда указатель входит в область проверки нажатия элемента. |
| [**PointerExited**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerexited)               | Происходит, когда указатель выходит из области проверки нажатия элемента.  |
| [**PointerCanceled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointercanceled)           | Происходит при ненормальном завершении сенсорного контакта.               |
| [**PointerCaptureLost**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointercapturelost)     | Происходит, когда перехват событий указателя выполняется другим элементом.    |
| [**PointerWheelChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerwheelchanged)   | Происходит при изменении Дельта-значения колесика мыши и при сжатии сенсорной панели.         |
| [**поинтерраутедевентаргс**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.PointerRoutedEventArgs) | Предоставляет данные для всех событий указателя.                         |

 

В следующем примере показано, как использовать события [**PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed), [**PointerReleased**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerreleased) и [**PointerExited**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerexited) для обработки взаимодействия в форме касания на объекте [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle).

Сначала в XAML создается объект [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) с именем `touchRectangle`.

```XAML
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Rectangle Name="touchRectangle"
           Height="100" Width="200" Fill="Blue" />
</Grid>
```
Далее указываются прослушиватели для событий [**PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed), [**PointerReleased**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerreleased) и [**PointerExited**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerexited).

```cpp
MainPage::MainPage()
{
    InitializeComponent();

    // Pointer event listeners.
    touchRectangle->PointerPressed += ref new PointerEventHandler(this, &MainPage::touchRectangle_PointerPressed);
    touchRectangle->PointerReleased += ref new PointerEventHandler(this, &MainPage::touchRectangle_PointerReleased);
    touchRectangle->PointerExited += ref new PointerEventHandler(this, &MainPage::touchRectangle_PointerExited);
}
```

```cs
public MainPage()
{
    this.InitializeComponent();

    // Pointer event listeners.
    touchRectangle.PointerPressed += touchRectangle_PointerPressed;
    touchRectangle.PointerReleased += touchRectangle_PointerReleased;
    touchRectangle.PointerExited += touchRectangle_PointerExited;
}
```

```vb
Public Sub New()

    ' This call is required by the designer.
    InitializeComponent()

    ' Pointer event listeners.
    AddHandler touchRectangle.PointerPressed, AddressOf touchRectangle_PointerPressed
    AddHandler touchRectangle.PointerReleased, AddressOf Me.touchRectangle_PointerReleased
    AddHandler touchRectangle.PointerExited, AddressOf touchRectangle_PointerExited

End Sub
```

Наконец, обработчик событий [**PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed) увеличивает параметры [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) и [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) объекта [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle), в то время, как обработчики событий [**PointerReleased**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerreleased) и [**PointerExited**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerexited) возвращают параметры **Height** и **Width** к начальным значениям.

```cpp
// Handler for pointer exited event.
void MainPage::touchRectangle_PointerExited(Object^ sender, PointerRoutedEventArgs^ e)
{
    Rectangle^ rect = (Rectangle^)sender;

    // Pointer moved outside Rectangle hit test area.
    // Reset the dimensions of the Rectangle.
    if (nullptr != rect)
    {
        rect->Width = 200;
        rect->Height = 100;
    }
}

// Handler for pointer released event.
void MainPage::touchRectangle_PointerReleased(Object^ sender, PointerRoutedEventArgs^ e)
{
    Rectangle^ rect = (Rectangle^)sender;

    // Reset the dimensions of the Rectangle.
    if (nullptr != rect)
    {
        rect->Width = 200;
        rect->Height = 100;
    }
}

// Handler for pointer pressed event.
void MainPage::touchRectangle_PointerPressed(Object^ sender, PointerRoutedEventArgs^ e)
{
    Rectangle^ rect = (Rectangle^)sender;

    // Change the dimensions of the Rectangle.
    if (nullptr != rect)
    {
        rect->Width = 250;
        rect->Height = 150;
    }
}
```

```cs
// Handler for pointer exited event.
private void touchRectangle_PointerExited(object sender, PointerRoutedEventArgs e)
{
    Rectangle rect = sender as Rectangle;

    // Pointer moved outside Rectangle hit test area.
    // Reset the dimensions of the Rectangle.
    if (null != rect)
    {
        rect.Width = 200;
        rect.Height = 100;
    }
}
// Handler for pointer released event.
private void touchRectangle_PointerReleased(object sender, PointerRoutedEventArgs e)
{
    Rectangle rect = sender as Rectangle;

    // Reset the dimensions of the Rectangle.
    if (null != rect)
    {
        rect.Width = 200;
        rect.Height = 100;
    }
}

// Handler for pointer pressed event.
private void touchRectangle_PointerPressed(object sender, PointerRoutedEventArgs e)
{
    Rectangle rect = sender as Rectangle;

    // Change the dimensions of the Rectangle.
    if (null != rect)
    {
        rect.Width = 250;
        rect.Height = 150;
    }
}
```

```vb
' Handler for pointer exited event.
Private Sub touchRectangle_PointerExited(sender As Object, e As PointerRoutedEventArgs)
    Dim rect As Rectangle = CType(sender, Rectangle)

    ' Pointer moved outside Rectangle hit test area.
    ' Reset the dimensions of the Rectangle.
    If (rect IsNot Nothing) Then
        rect.Width = 200
        rect.Height = 100
    End If
End Sub

' Handler for pointer released event.
Private Sub touchRectangle_PointerReleased(sender As Object, e As PointerRoutedEventArgs)
    Dim rect As Rectangle = CType(sender, Rectangle)

    ' Reset the dimensions of the Rectangle.
    If (rect IsNot Nothing) Then
        rect.Width = 200
        rect.Height = 100
    End If
End Sub

' Handler for pointer pressed event.
Private Sub touchRectangle_PointerPressed(sender As Object, e As PointerRoutedEventArgs)
    Dim rect As Rectangle = CType(sender, Rectangle)

    ' Change the dimensions of the Rectangle.
    If (rect IsNot Nothing) Then
        rect.Width = 250
        rect.Height = 150
    End If
End Sub
```

## <a name="manipulation-events"></a>События манипуляции


Используйте события манипуляции, если в приложении нужно обеспечить поддержку взаимодействия с помощью нескольких пальцев или взаимодействий, требующих данные о скорости.

С помощью событий манипуляций можно обнаруживать такие взаимодействия, как перетаскивание, масштабирование и удерживание.

> [!NOTE]
> Сенсорная панель не создает события манипуляции. Вместо этого для ввода с сенсорной панели будут созданы события указателя.

Вот список событий манипуляций и аргументы связанных событий.

| Событие или класс                                                                                               | Описание                                                                                                                               |
|--------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| [**Событие ManipulationStarting**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationstarting)                                   | Происходит при первоначальном создании процессора манипулирования.                                                                                  |
| [**Событие ManipulationStarted**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationstarted)                                     | Происходит, когда устройство ввода начинает манипуляцию над [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement).                                            |
| [**Событие ManipulationDelta**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationdelta)                                         | Происходит, когда устройство ввода меняет положение в процессе манипуляции.                                                                      |
| [**Событие ManipulationInertiaStarting**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationinertiastartingevent)                | Происходит, если во время манипуляции устройство ввода теряет контакт с объектом [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) и начинает действовать инерция. |
| [**Событие ManipulationCompleted**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationcompleted)                                 | Происходит, когда манипуляция и движение по инерции для [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) завершены.                                          |
| [**ManipulationStartingRoutedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ManipulationStartingRoutedEventArgs)               | Предоставляет данные для события [**ManipulationStarting**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationstarting).                                         |
| [**ManipulationStartedRoutedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ManipulationStartedRoutedEventArgs)                 | Предоставляет данные для события [**ManipulationStarted**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationstarted).                                           |
| [**ManipulationDeltaRoutedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ManipulationDeltaRoutedEventArgs)                     | Предоставляет данные для события [**ManipulationDelta**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationdelta).                                               |
| [**ManipulationInertiaStartingRoutedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ManipulationInertiaStartingRoutedEventArgs) | Предоставляет данные для события [**ManipulationInertiaStarting**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationinertiastarting).                           |
| [**ManipulationVelocities**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.ManipulationVelocities)                                              | Описывает скорость, с которой происходят манипуляции.                                                                                         |
| [**ManipulationCompletedRoutedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ManipulationCompletedRoutedEventArgs)             | Предоставляет данные для события [**ManipulationCompleted**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationcompleted).                                       |

 

Жест состоит из серии событий манипуляции. Каждый жест начинается с события [**ManipulationStarted**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationstarted) , например, когда пользователь касается экрана.

Далее запускаются одно или несколько событий [**ManipulationDelta**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationdelta). Например, если коснуться экрана, а затем провести по нему пальцем. Наконец, после завершения взаимодействия вызывается событие [**ManipulationCompleted**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationcompleted).

> [!NOTE]
> Если у вас нет монитора сенсорного экрана, можно протестировать код события манипуляции в симуляторе, используя интерфейс мыши и колесика мыши.

 

Следующий пример показывает, как использовать события [**ManipulationDelta**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationdelta) для обработки взаимодействия в форме скольжения на объекте [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) и его перемещения по экрану.

Сначала в XAML создается объект [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) с именем `touchRectangle`, свойства [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) и [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) которого имеют значение 200.

```XAML
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Rectangle Name="touchRectangle"
               Width="200" Height="200" Fill="Blue" 
               ManipulationMode="All"/>
</Grid>
```

Затем создается глобальный параметр [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform) с именем `dragTranslation` для преобразования [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle). Прослушиватель событий [**ManipulationDelta**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationdelta) указывается на объекте **Rectangle**, и параметр `dragTranslation` добавляется к параметру [**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform) объекта **Rectangle**.

```cpp
// Global translation transform used for changing the position of 
// the Rectangle based on input data from the touch contact.
Windows::UI::Xaml::Media::TranslateTransform^ dragTranslation;
```

```cs
// Global translation transform used for changing the position of 
// the Rectangle based on input data from the touch contact.
private TranslateTransform dragTranslation;
```

```vb
' Global translation transform used for changing the position of 
' the Rectangle based on input data from the touch contact.
Private dragTranslation As TranslateTransform
```

```cpp
MainPage::MainPage()
{
    InitializeComponent();

    // Listener for the ManipulationDelta event.
    touchRectangle->ManipulationDelta += 
        ref new ManipulationDeltaEventHandler(
            this, 
            &MainPage::touchRectangle_ManipulationDelta);
    // New translation transform populated in 
    // the ManipulationDelta handler.
    dragTranslation = ref new TranslateTransform();
    // Apply the translation to the Rectangle.
    touchRectangle->RenderTransform = dragTranslation;
}
```

```cs
public MainPage()
{
    this.InitializeComponent();

    // Listener for the ManipulationDelta event.
    touchRectangle.ManipulationDelta += touchRectangle_ManipulationDelta;
    // New translation transform populated in 
    // the ManipulationDelta handler.
    dragTranslation = new TranslateTransform();
    // Apply the translation to the Rectangle.
    touchRectangle.RenderTransform = this.dragTranslation;
}
```

```vb
Public Sub New()

    ' This call is required by the designer.
    InitializeComponent()

    ' Listener for the ManipulationDelta event.
    AddHandler touchRectangle.ManipulationDelta,
        AddressOf testRectangle_ManipulationDelta
    ' New translation transform populated in 
    ' the ManipulationDelta handler.
    dragTranslation = New TranslateTransform()
    ' Apply the translation to the Rectangle.
    touchRectangle.RenderTransform = dragTranslation

End Sub
```

Наконец, в обработчике событий [**ManipulationDelta**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationdelta) положение объекта [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) обновляется путем применения [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform) к свойству [**Delta**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.manipulationdeltaroutedeventargs.delta).

```cpp
// Handler for the ManipulationDelta event.
// ManipulationDelta data is loaded into the
// translation transform and applied to the Rectangle.
void MainPage::touchRectangle_ManipulationDelta(Object^ sender,
    ManipulationDeltaRoutedEventArgs^ e)
{
    // Move the rectangle.
    dragTranslation->X += e->Delta.Translation.X;
    dragTranslation->Y += e->Delta.Translation.Y;
    
}
```

```cs
// Handler for the ManipulationDelta event.
// ManipulationDelta data is loaded into the
// translation transform and applied to the Rectangle.
void touchRectangle_ManipulationDelta(object sender,
    ManipulationDeltaRoutedEventArgs e)
{
    // Move the rectangle.
    dragTranslation.X += e.Delta.Translation.X;
    dragTranslation.Y += e.Delta.Translation.Y;
}
```

```vb
' Handler for the ManipulationDelta event.
' ManipulationDelta data Is loaded into the
' translation transform And applied to the Rectangle.
Private Sub testRectangle_ManipulationDelta(
    sender As Object,
    e As ManipulationDeltaRoutedEventArgs)

    ' Move the rectangle.
    dragTranslation.X = (dragTranslation.X + e.Delta.Translation.X)
    dragTranslation.Y = (dragTranslation.Y + e.Delta.Translation.Y)

End Sub
```

## <a name="routed-events"></a>Перенаправленные события


Все события указателя, события жестов и события манипуляций, упомянутые здесь, реализованы как *перенаправленные события*. Это значит, что события потенциально могут обрабатываться объектами, отличными от объектов, которые вызвали событие. Последующие родительские объекты в дереве, такие как родительские контейнеры элементов [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) или корень [**Page**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page) вашего приложения, могут обрабатывать эти события, даже если исходный элемент этого не делает. И наоборот, любой объект, который обрабатывает событие, может пометить событие как обработанное, чтобы оно не обращалось к родительским элементам. Подробнее о концепции маршрутизированных событий и их влиянии на метод написания обработчиков для перенаправленных событий см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://docs.microsoft.com/previous-versions/windows/apps/hh758286(v=win.10)).

## <a name="dos-and-donts"></a>Полезные советы


-   Разрабатывайте приложения с учетом того, что сенсорное взаимодействие является приоритетным методом ввода.
-   Обеспечьте визуальную обратную связь для всех видов взаимодействия (касание, перо, стилус, мышь и пр.).
-   Оптимизируйте нацеливание с помощью настройки размеров целей касания, формы области контакта, проведения и качания.
-   Оптимизируйте точность благодаря использованию точек прикрепления и направляющих "рельсов".
-   Подготовьте подсказки и маркеры, чтобы улучшить точность касания для плотно упакованных элементов пользовательского интерфейса.
-   По возможности не используйте взаимодействие, зависимое от времени (пример правильного использования — касание и удержание).
-   По возможности не используйте количество пальцев для различения манипуляций.

## <a name="related-articles"></a>Похожие статьи

- [Работа с входными данными указателя](handle-pointer-input.md)
- [Распознавание устройств ввода](identify-input-devices.md)

### <a name="samples"></a>Примеры

- [Пример базового ввода](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)
- [Пример ввода с малой задержкой](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LowLatencyInput)
- [Пример режима взаимодействия с пользователем](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/UserInteractionMode)
- [Пример визуальных элементов фокуса](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals)

### <a name="archive-samples"></a>Примеры архивов

- [Ввод: пример возможностей устройства](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/Input%20Device%20capabilities%20sample%20(Windows%208))
- [Ввод: пример событий пользовательского ввода на XAML](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20XAML%20user%20input%20events%20sample)
- [Пример прокрутки XAML, панорамирования и масштабирования](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Universal%20Windows%20app%20samples/111487-Universal%20Windows%20app%20samples/XAML%20scrolling%2C%20panning%2C%20and%20zooming%20sample)
- [Ввод: жесты и манипуляции с помощью GestureRecognizer](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20Gestures%20and%20manipulations%20with%20GestureRecognizer)
