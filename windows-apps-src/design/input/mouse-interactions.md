---
Description: В приложениях вы можете отвечать на ввод с помощью мыши, обрабатывая те же базовые события указателя, которые используются для сенсорного ввода и ввода с помощью пера.
title: Взаимодействие с помощью мыши
ms.assetid: C8A158EF-70A9-4BA2-A270-7D08125700AC
label: Mouse
template: detail.hbs
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 2d6ddf03541e94f89d0950a4f4c03eebaa0e396e
ms.sourcegitcommit: 87fd0ec1e706a460832b67f936a3014f0877a88c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83234469"
---
# <a name="mouse-interactions"></a>Взаимодействие с помощью мыши

Оптимизируйте структуру приложения Windows для сенсорного ввода и получите по умолчанию обычную поддержку мыши. 

![Мышь](images/input-patterns/input-mouse.jpg)

Взаимодействие при помощи мыши лучше всего подходит для приложений, требующих точного указания и щелчков. Эта точность естественным образом поддерживается пользовательским интерфейсом Windows, который оптимизирован для неточных по своей природе сенсорных взаимодействий.

Существенное отличие ввода с помощью мыши от сенсорного ввода — возможность эмуляции непосредственного взаимодействия с элементами пользовательского интерфейса на экране с использованием физических жестов (прокрутка, сдвиг, перетаскивание, поворот и т. п.) в случае сенсорного ввода. Для манипуляций с помощью мыши обычно требуются другие возможности пользовательского интерфейса, например использование маркеров для изменения размера или поворота объекта.

В этом разделе описываются вопросы разработки для взаимодействия с помощью мыши.

## <a name="the-uwp-app-mouse-language"></a>Язык мыши приложения UWP

Компактный набор взаимодействий с помощью мыши используется единообразно во всей системе.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Термин</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Обучение при наведении</p></td>
<td align="left"><p>Наведите указатель на элемент, чтобы показать подробную информацию или визуальные обучающие элементы (например, подсказку), не выполняя никакого действия.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Щелчок левой кнопкой для основного действия</p></td>
<td align="left"><p>Щелчок левой кнопкой вызывает основное действие (например, запуск приложения или выполнение команды).</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Прокрутка для изменения представления</p></td>
<td align="left"><p>Отображение полос прокрутки для перемещения вверх, вниз, влево и вправо в пределах области содержимого. Для прокрутки содержимого можно щелкать на полосах прокрутки или вращать колесо мыши. Полосы прокрутки могут обозначать расположение текущего представления в пределах области содержимого (сдвиг касанием отображает аналогичный пользовательский интерфейс).</p></td>
</tr>
<tr class="even">
<td align="left"><p>Щелчок правой кнопкой мыши для выбора и выполнения команды</p></td>
<td align="left"><p>Щелчок правой кнопкой мыши для отображения панели навигации (при наличии) и панели приложения с глобальными командами. Щелчок правой кнопкой мыши на элементе для его выделения и отображения контекстных команд для выбранного элемента.</p>
<div class="alert">
<strong>Примечание</strong>    . Щелкните правой кнопкой мыши, чтобы открыть контекстное меню, если выборка или команды панели приложений не соответствуют поведению пользовательского интерфейса. Однако настоятельно рекомендуется для всех вариантов поведения команд использовать панель приложения.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>Команды пользовательского интерфейса для масштабирования</p></td>
<td align="left"><p>Отображение команд пользовательского интерфейса на панели приложения (например, + и -) либо нажатие клавиши CTRL и вращение колеса мыши для эмуляции жестов увеличения и уменьшения масштаба.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Команды пользовательского интерфейса для поворота</p></td>
<td align="left"><p>Отображение команд пользовательского интерфейса на панели приложения либо нажатие клавиш CTRL+SHIFT и вращение колеса мыши для эмуляции жеста поворота. Чтобы повернуть весь экран, поверните само устройство.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Щелчок левой кнопкой мыши и перетаскивание для изменения взаимного расположения элементов</p></td>
<td align="left"><p>Щелчок левой кнопкой мыши и перетаскивание элемента для его перемещения.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Щелчок левой кнопкой мыши и перетаскивание для выделения текста</p></td>
<td align="left"><p>Щелчок левой кнопкой мыши в пределах доступного для выделения текста и перемещение для выделения текста. Двойной щелчок для выделения слова.</p></td>
</tr>
</tbody>
</table>

## <a name="mouse-input-events"></a>События ввода мыши

Большая часть ввода мыши может быть обработана с помощью общих перенаправленных событий ввода, поддерживаемых всеми объектами [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) . К ним относятся следующие объекты.

- [**брингинтовиеврекуестед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.bringintoviewrequested)
- [**чарактеррецеивед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.characterreceived)
- [**контекстканцелед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.contextcanceled)
- [**контекстрекуестед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.contextrequested)
- [**DoubleTapped**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.doubletapped)
- [**DragEnter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.dragenter)
- [**DragLeave**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.dragleave)
- [**DragOver**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.dragover)
- [**драгстартинг**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.dragstarting)
- [**Тени**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.drop)
- [**дропкомплетед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.dropcompleted)
- [**жеттингфокус**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.gettingfocus)
- [**GotFocus**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.gotfocus)
- [**Holding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.holding)
- [**KeyDown**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.keydown)
- [**KeyUp**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.keyup)
- [**лосингфокус**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.losingfocus)
- [**LostFocus**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.lostfocus)
- [**ManipulationCompleted**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationcompleted)
- [**ManipulationDelta**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationdelta)
- [**ManipulationInertiaStarting**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationinertiastarting)
- [**ManipulationStarted**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationstarted)
- [**ManipulationStarting**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationstarting)
- [**нофокускандидатефаунд**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.nofocuscandidatefound)
- [**PointerCanceled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointercanceled)
- [**PointerCaptureLost**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointercapturelost)
- [**PointerEntered**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerentered)
- [**PointerExited**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerexited)
- [**PointerMoved**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointermoved)
- [**PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed)
- [**PointerReleased**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerreleased)
- [**PointerWheelChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerwheelchanged)
- [**PreviewKeyDown**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.previewkeydown)
- [**превиевкэйуп**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.previewkeyup)
- [**RightTapped**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.righttapped)
- [**Tapped**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.tapped)

Тем не менее можно воспользоваться возможностями каждого устройства (например, события колесика мыши), используя события указателя, жеста и манипуляции в [Windows. UI. Input](https://docs.microsoft.com/uwp/api/windows.ui.input).

**Примеры:** См. наш [Пример басиЦинпут](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)для.

## <a name="guidelines-for-visual-feedback"></a>Руководство по визуальной обратной связи

- Обнаружив мышь (при помощи событий перемещения или наведения), отображайте пользовательский интерфейс для мыши, чтобы показать функциональность, которую предоставляет элемент. Если мышь не перемещается в течение определенного времени или если пользователь инициирует взаимодействие касанием, сделайте так, чтобы пользовательский интерфейс для мыши постепенно исчезал с экрана. В этом случае пользовательский интерфейс будет аккуратным, не загроможденным лишними элементами.
- Не используйте курсор для обратной связи при наведении, для этого достаточно обратной связи от элемента (см. ниже раздел "Курсоры").
- Не отображайте визуальную обратную связь, если элемент не поддерживает взаимодействие (например, статический текст).
- Не используйте прямоугольник фокуса для взаимодействий с помощью мыши. Оставьте их для взаимодействий с использованием клавиатуры.
- Отображайте визуальную обратную связь одновременно для всех элементов, представляющих одну и ту же цель ввода.
- Предусмотрите кнопки (например, + и -) для эмуляции манипуляций касания, таких как сдвиг, поворот, масштабирование и пр.

Дополнительные общие рекомендации по визуальной обратной связи см. в разделе [Руководство по визуальной обратной связи](guidelines-for-visualfeedback.md).

## <a name="cursors"></a>Курсоры

Для указателя мыши предусмотрен набор стандартных курсоров. Они указывают на основное действие элемента.

С каждым из стандартных указателей сопоставлено изображение по умолчанию. Пользователь или приложение могут в любое время замещать изображение по умолчанию любым другим стандартным курсором. Изображение курсора можно выбрать с помощью функции [**PointerCursor**](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.pointercursor).

Если требуется настроить курсор мыши:

- Обязательно используйте курсор-стрелку (![Курсор-стрелка](images/cursor-arrow.png)) для элементов, реагирующих на щелчок. не используйте курсор в виде руки (![Курсор в виде руки](images/cursor-pointinghand.png)) для ссылок и других интерактивных элементов. Вместо него применяйте эффекты наведения (описанные выше).
- Используйте текстовый курсор (![Текстовый курсор](images/cursor-text.png)) для текста, доступного для выделения.
- Используйте курсор перемещения (![Курсор перемещения](images/cursor-move.png)), если основное действие — это перемещение (перетаскивание или обрезка). Не используйте курсор перемещения для элементов, у которых основное действие — навигация (например, плитки начального экрана).
- Используйте курсоры изменения размеров по горизонтали, вертикали и диагонали (![Курсор изменения размеров по вертикали](images/cursor-vertical.png), ![Курсор изменения размеров по горизонтали](images/cursor-horizontal.png), ![Курсор изменения размеров по диагонали (левый нижний, правый верхний)](images/cursor-diagonal2.png), ![Курсор изменения размеров по диагонали (левый верхний, правый нижний)](images/cursor-diagonal1.png)), если размер объекта можно изменить.
- Используйте курсоры "хватающая рука" (![Курсор "хватающая рука" (открыта)](images/cursor-pan1.png), ![Курсор "хватающая рука" (сжатая)](images/cursor-pan2.png)) для сдвига содержимого в пределах фиксированного холста, например карты.

## <a name="related-articles"></a>Похожие статьи

- [Работа с входными данными указателя](handle-pointer-input.md)
- [Распознавание устройств ввода](identify-input-devices.md)
- [Общие сведения о событиях и перенаправленных событиях](https://docs.microsoft.com/windows/uwp/xaml-platform/events-and-routed-events-overview)

### <a name="samples"></a>Примеры

- [Пример базового ввода](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)
- [Пример ввода с малой задержкой](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LowLatencyInput)
- [Пример режима взаимодействия с пользователем](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/UserInteractionMode)
- [Пример визуальных элементов фокуса](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals)
