---
Description: В этом разделе описывается новый пользовательский интерфейс Windows для выбора и манипулирования текстом, изображениями и элементами управления, а также приводятся рекомендации по работе с пользователем, которые следует учитывать при использовании этих новых механизмов выбора и управления в приложении Windows.
title: Выделение текста и изображений
ms.assetid: d973ffd8-602e-47b5-ab0b-4b2a964ec53d
label: Selecting text and images
template: detail.hbs
keywords: клавиатура, текст, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: a118a7160842154a656e0f2d29783b1b2e676755
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970099"
---
# <a name="selecting-text-and-images"></a>Выделение текста и изображений


В этой статье описывается выделение и обработка текста, изображений и элементов управления, а также представлены рекомендации по взаимодействию с пользователем, которые следует учитывать при использовании этих механизмов в приложениях.

> **Важные API-интерфейсы**: [**Windows.UI.Xaml.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input), [**Windows.UI.Xaml.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Input)
 


## <a name="dos-and-donts"></a>Полезные советы


-   Используйте глифы шрифтов при реализации собственного пользовательского интерфейса захвата. Захват представляет собой сочетание двух шрифтов Segoe UI, доступных в системе. Использование ресурсов шрифтов упрощает отрисовку при различных значениях DPI и хорошо работает с различными уровнями масштабирования пользовательского интерфейса. При реализации собственных захватов они должны обладать следующими характеристиками пользовательского интерфейса:

    -   круглая форма;
    -   видимость на любом фоне;
    -   согласованный размер.
-   Создать поле вокруг доступного для выбора содержимого, чтобы разместить пользовательский интерфейс захвата. Если ваше приложение допускает выделение текста в области, которая не затрагивается функциями сдвига и прокрутки, оставьте 1/2 поля захвата слева и справа от области текста, а также 1 высоту захвата сверху и снизу от области текста (как показано на следующих иллюстрациях). Это обеспечит доступность всего пользовательского интерфейса захвата для пользователя и минимизирует случайные взаимодействия с другими элементами пользовательского интерфейса, привязанными к границам экрана.

    ![Поля захвата выделения текста](images/textselection-gripper-margins.png)

-   Скрывайте пользовательский интерфейс захватов во время взаимодействия. Исключает загораживание экрана захватами во время взаимодействия. Это особенно удобно, когда захват не полностью скрыт пальцем или существует несколько захватов выделения текста. Это исключает появление визуальных артефактов при отображении дочерних окон.

-   Не допускайте выбора таких элементов пользовательского интерфейса, как элементы управления, наклейки, рисунки, защищаемое содержимое и т. д. Обычно приложения для Windows допускают выделение только определенного набора элементов управления. Такие элементы управления, как кнопки, метки и логотипы, не выделяются. Подумайте, необходимо ли выделение в вашем приложении, и если да, то определите области пользовательского интерфейса, в которых выделение следует запретить. 

## <a name="additional-usage-guidance"></a>Дополнительное рекомендации по использованию


Выделение текста и манипулирование им принадлежат к числу самых сложных задач, возникающих при использовании сенсорного взаимодействия. Ввод с помощью мыши, пера и клавиатуры в высшей степени точен: щелчок мыши или контакт пера обычно сопоставляются с определенным пикселем, а клавиша либо нажата, либо нет. Сенсорный ввод неточен. Сложно сопоставить всю поверхность пальца с определенными координатами X и Y на экране, чтобы точно установить курсор.

**Рекомендации**

С помощью встроенных элементов управления, доступных через языковые структуры в Windows, создавайте приложения, которые обеспечивают полное взаимодействие с пользователем платформы, в том числе поведение при выделении и манипуляциях. Вы найдете возможности взаимодействия встроенных элементов управления, достаточных для большинства приложений Windows.

При использовании стандартных элементов управления "текст Windows" поведение выбора и визуальные элементы, описанные в этом разделе, нельзя настроить.

**Выделение текста**

Если для вашего приложения требуется настраиваемый пользовательский интерфейс, который поддерживает выделение текста, рекомендуется придерживаться описанных здесь функций выделения Windows.

**Редактируемое и нередактируемое содержимое**


При сенсорном взаимодействии для выделения используются в основном жесты, например касание для установки курсора или выделения слова и скользящее движение для изменения выделения. Как и в случае других сенсорных взаимодействий Windows, основанные на времени взаимодействия нажатия и удерживания используются для отображения информационного пользовательского интерфейса. Подробнее см. в разделе [Рекомендации по визуальной обратной связи](guidelines-for-visualfeedback.md).

Windows распознает два возможных состояния взаимодействий выделения — редактируемое и нередактируемое — и настраивает пользовательский интерфейс выделения, реакцию и функции соответствующим образом.

**Редактируемое содержимое**

Касание левой части слова помещает курсор непосредственно слева от слова, а касание правой части — непосредственно справа от слова.

Следующее изображение демонстрирует, как установить начальный курсор вставки с захватом, коснувшись экрана рядом с началом или концом слова.

![Коснитесь левой стороны слова (или нажмите и удерживайте), чтобы установить системный курсор с захватом в начале слова. Коснитесь правой стороны слова (или нажмите и удерживайте), чтобы установить системный курсор с захватом в конце слова.](images/textselection-place-caret.png)

Следующее изображение демонстрирует, как регулировать выделение, перетаскивая захват.

![Перетащите захват в любом направлении, чтобы отрегулировать выделение (первый захват остается на прежнем месте, второй захват отображается). Чтобы дополнительно отрегулировать выделение, перетащите любой захват.](images/adjust-selection.png)

Следующее изображение демонстрирует, как вызвать контекстное меню, коснувшись выделения или захвата (можно также использовать нажатие и удерживание).

![Коснитесь выделения или захвата (или нажмите и удерживайте), чтобы вызвать контекстное меню.](images/textselection-show-context.png)

**Обратите внимание**  , что эти взаимодействия немного отличаются в случае орфографических слов. Если вы коснетесь слова, которое отмечено как неверно написанное, вы тем самым выделите все слово и вызовете контекстное меню с предложением вариантов написания.

 

**Нередактируемое содержимое**

Следующее изображение демонстрирует, как выделить слово, коснувшись его (пробелы не включены в начальное выделение).

![Коснитесь слова, чтобы выделить его (пробелы не включены в начальное выделение).](images/select-word.png)

Выполните те же действия, что и для редактируемого текста, чтобы отрегулировать выделение и вызвать контекстное меню.

**Манипулирование объектами**

Везде, где это возможно, используйте те же или аналогичные ресурсы захвата в качестве выделения текста при реализации пользовательской манипуляции с объектами в приложении Windows. Это помогает обеспечить согласованное взаимодействие в масштабах платформы.

Например, захваты можно также использовать в графических редакторах, поддерживающих изменение размеров и обрезку, либо в приложениях проигрывателей мультимедиа, содержащих настраиваемые индикаторы выполнения, как показано на следующих рисунках.

![Проигрыватель мультимедиа с захватом хода выполнения](images/gripper-mediaplayer.png)

*Проигрыватель мультимедиа с настраиваемым индикатором выполнения.*

![Изображение с захватами обрезки](images/gripper-imagemanip.png)

*Редактор изображений с захватами обрезки.*

## <a name="related-articles"></a>Похожие статьи

### <a name="for-developers"></a>Для разработчиков

- [Настраиваемые взаимодействия с пользователем](https://docs.microsoft.com/windows/uwp/design/layout/index)

### <a name="samples"></a>Примеры

- [Пример базового ввода](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)
- [Пример ввода с малой задержкой](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LowLatencyInput)
- [Пример режима взаимодействия с пользователем](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/UserInteractionMode)
- [Пример визуальных элементов фокуса](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals)

### <a name="archive-samples"></a>Примеры архива

- [Ввод: пример событий пользовательского ввода на XAML](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20XAML%20user%20input%20events%20sample)
- [Ввод: пример возможностей устройства](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/Input%20Device%20capabilities%20sample%20(Windows%208))
- [Ввод: пример тестирования сенсорного ввода](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20desktop%20samples/%5BC%2B%2B%5D-Windows%208%20desktop%20samples/C%2B%2B/Windows%208%20desktop%20samples/Input%20Touch%20hit%20testing%20sample)
- [Пример прокрутки XAML, панорамирования и масштабирования](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Universal%20Windows%20app%20samples/111487-Universal%20Windows%20app%20samples/XAML%20scrolling%2C%20panning%2C%20and%20zooming%20sample)
- [Ввод: пример упрощенного рукописного ввода](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20Simplified%20ink%20sample)
- [Ввод: пример жестов в Windows 8](https://docs.microsoft.com/samples/browse/?redirectedfrom=MSDN-samples)
- [Входные данные: пример манипуляций и жестов](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20Gestures%20and%20manipulations%20with%20GestureRecognizer)
- [Пример сенсорного ввода в DirectX](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%2B%2B%5D-Windows%208%20app%20samples/C%2B%2B/Windows%208%20app%20samples/DirectX%20touch%20input%20sample%20(Windows%208))
