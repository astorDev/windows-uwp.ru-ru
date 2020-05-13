---
Description: Основные текстовые интерфейсы API в пространстве имен Windows. UI. Text. Core позволяют приложению Windows принимать текстовые данные из любой текстовой службы, поддерживаемой на устройствах Windows.
title: Обзор пользовательского ввода текста
ms.assetid: 58F5F7AC-6A4B-45FC-8C2A-942730FD7B74
label: Custom text input
template: detail.hbs
keywords: клавиатура, текст, основной текст, пользовательский текст, инфраструктура текстовых служб, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: bf2fb934d4ae6f2e954cf32e612ebf2b2538d7ce
ms.sourcegitcommit: 87fd0ec1e706a460832b67f936a3014f0877a88c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83234445"
---
# <a name="custom-text-input"></a>Ввод пользовательского текста



Основные текстовые интерфейсы API в пространстве имен [**Windows. UI. Text. Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core) позволяют приложению Windows принимать текстовые данные из любой текстовой службы, поддерживаемой на устройствах Windows. API очень похожи на API [инфраструктуры текстовых служб](https://docs.microsoft.com/windows/desktop/TSF/text-services-framework) в этом приложении в том, что приложению не требуются подробные данные текстовых служб. Это позволяет приложению получать текст на любом языке и из любого типа ввода, например клавиатуры, речи или пера.

> **Важные API-интерфейсы**: [**Windows.UI.Text.Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core), [**CoreTextEditContext**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core.CoreTextEditContext)

## <a name="why-use-core-text-apis"></a>Зачем использовать API основного текста?


Для многих приложений элементов управления XAML или HTML достаточно для текстового ввода и редактирования. Однако, если ваше приложение обрабатывает сложные сценарии текста, например предназначено для обработки текстов, возможно, потребуется гибкость пользовательского элемента управления редактированием текста. Вы можете использовать API клавиатуры [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) для создания элемента управления редактированием текста, но они не предоставляют способ получить ввод текста на основе композиции, необходимый для поддержки восточноазиатских языков.

Вместо этого используйте API [**Windows.UI.Text.Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core), когда вам нужно создать пользовательский элемент управления редактированием текста. Эти API предназначены для предоставления вам достаточной гибкости в обработке ввода текста на любом языке и обеспечивают удобство работы с текстом соответственно вашему приложению. Ввод текста и элементы управления редактированием, созданные с помощью основных API текста, могут получать ввод текста от всех существующих методов ввода текста на устройствах с Windows: от редакторов метода ввода (IME) на основе [инфраструктуры текстовых служб](https://docs.microsoft.com/windows/desktop/TSF/text-services-framework) и рукописного ввода на компьютерах до клавиатуры WordFlow (которая предоставляет автозамену, прогнозирование и диктовки) на мобильных устройствах.

## <a name="architecture"></a>Architecture


Ниже приведено простое представление системы ввода текста.

-   "Приложение" представляет приложение Windows, в котором размещается пользовательский элемент управления "поле ввода", созданный с помощью основных текстовых API.
-   API [**Windows.UI.Text.Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core) упрощают связь с текстовыми службами с помощью Windows. Связь между элементом управления редактированием текста и текстовыми службами обрабатывается главным образом объектом [**CoreTextEditContext**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core.CoreTextEditContext), который предоставляет методы и события для облегчения связи.

![схема архитектуры основного текста](images/coretext/architecture.png)

## <a name="text-ranges-and-selection"></a>Диапазоны текста и выделение


Элементы управления редактированием предоставляют место для ввода текста, и пользователи могут редактировать текст в любом месте в этом пространстве. Здесь мы описываем систему позиционирования текста, используемую API основного текста, а также диапазоны и выделения, представленные в этой системе.

### <a name="application-caret-position"></a>Положение курсора приложения

Диапазоны текста, используемые с API основного текста, выражаются положениями курсора. "Положение курсора приложения (ACP)" — это число на основе нуля, указывающее количество символов от начала потока текста непосредственно перед курсором, как показано здесь.

![пример схемы потока текста](images/coretext/stream-1.png)
### <a name="text-ranges-and-selection"></a>Диапазоны текста и выделение

Диапазоны и выделения текста представлены структурой [**CoreTextRange**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core.CoreTextRange), содержащей два поля:

| Поле                  | Тип данных                                                                 | Описание                                                                      |
|------------------------|---------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| **StartCaretPosition** | **Число** \[ Скриптов\] | **System. Int32** \[ .NET\] | **Int32** \[ C++\] | Начальное положение диапазона — это ACP непосредственно перед первым символом. |
| **EndCaretPosition**   | **Число** \[ Скриптов\] | **System. Int32** \[ .NET\] | **Int32** \[ C++\] | Конечное положение диапазона — это ACP непосредственно после последнего символа.     |

 

Например, в диапазоне, показанном ранее, диапазон \[ 0, 5 \] указывает слово Hello. Значение **StartCaretPosition** должно быть меньше или равно **EndCaretPosition**. \[Недопустимый диапазон 5, 0 \] .

### <a name="insertion-point"></a>Точка вставки

Текущее положение курсора, часто называемое точкой вставки, представлено установкой **StartCaretPosition**, равного **EndCaretPosition**.

### <a name="noncontiguous-selection"></a>Несвязанное выделение

Некоторые элементы управления редактированием поддерживают несвязанное выделение. Например, приложения Microsoft Office поддерживают несколько произвольных выделений, и многие редакторы исходного кода поддерживают выделение столбца. Однако API основного текста не поддерживают несвязанное выделение. Элементы управления редактированием должны сообщать только об одном связанном выделении. Чаще всего это активный поддиапазон несвязанных выделений.

Например, рассмотрим этот поток текста.

![Пример диаграммы потока текста ](images/coretext/stream-2.png) существует два выбора: \[ 0, 1 \] и \[ 6, 11 \] . Элемент управления "поле ввода" должен сообщать только один из них; \[0, 1 \] или \[ 6, 11 \] .

## <a name="working-with-text"></a>Работа с текстом


Класс [**CoreTextEditContext**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core.CoreTextEditContext) обеспечивает поток текста между Windows и элементами управления редактированием с помощью события [**TextUpdating**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textupdating), события [**TextRequested**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textrequested) и метода [**NotifyTextChanged**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.notifytextchanged).

Элемент управления редактированием получает текст с помощью событий [**TextUpdating**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textupdating), которые создаются, когда пользователи взаимодействуют с методами ввода текста с клавиатуры, речевым вводом или IME.

При изменении текста в вашем элементе управления редактированием, например путем вставки текста в элемент управления, необходимо уведомить Windows, вызвав [**NotifyTextChanged**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.notifytextchanged).

Если текстовая служба запрашивает новый текст, вызывается событие [**TextRequested**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textrequested). Необходимо предоставить новый текст в обработчике событий **TextRequested**.

### <a name="accepting-text-updates"></a>Принятие обновлений текста

Элемент управления редактированием обычно должен принимать запросы на обновление текста, так как они представляют собой текст, который хочет ввести пользователь. В обработчике событий [**TextUpdating**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textupdating) эти действия ожидаются от элемента управления редактированием:

1.  Вставьте текст, определенный в [**CoreTextTextUpdatingEventArgs.Text**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextupdatingeventargs.text), в положение, указанное в [**CoreTextTextUpdatingEventArgs.Range**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextupdatingeventargs.range).
2.  Установите выделение в положении, указанном в [**CoreTextTextUpdatingEventArgs.NewSelection**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextupdatingeventargs.newselection).
3.  Уведомите систему об успешном обновлении, установив для параметра [**CoreTextTextUpdatingEventArgs.Result**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextupdatingeventargs.result) значение [**CoreTextTextUpdatingResult.Succeeded**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core.CoreTextTextUpdatingResult).

Например, это состояние элемента управления редактированием до того, как пользователь ввел "d". Точка вставки — \[ 10, 10 \] .

![Пример диаграммы потока текста ](images/coretext/stream-3.png) Если пользователь вводит "d", событие [**текступдатинг**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textupdating) вызывается со следующими данными [**коретексттекступдатинжевентаргс**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core.CoreTextTextUpdatingEventArgs) :

-   [**Диапазон значений**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextupdatingeventargs.range)  =  \[ 10, 10\]
-   [**Text**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextupdatingeventargs.text) = "d"
-   [**NewSelection**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextupdatingeventargs.newselection)  =  Невселектион \[ 11, 11\]

В вашем элементе управления редактированием примените указанные изменения и установите для параметра [**Result**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextupdatingeventargs.result) значение **Succeeded**. Далее описано состояние элемента управления после применения изменений.

![пример схемы потока текста](images/coretext/stream-4.png)
### <a name="rejecting-text-updates"></a>Отклонение обновлений текста

Иногда вы не можете применить обновления текста, так как запрошенный диапазон находится в области элемента управления редактированием, которую не следует изменять. В этом случае вам не нужно применять изменения. Вместо этого уведомите систему о невозможности обновления, установив для параметра [**CoreTextTextUpdatingEventArgs.Result**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextupdatingeventargs.result) значение [**CoreTextTextUpdatingResult.Failed**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core.CoreTextTextUpdatingResult).

Например, рассмотрим элемент управления редактированием, который принимает только адрес электронной почты. Пробелы должны быть отклонены, так как адреса электронной почты не могут содержать пробелов. Поэтому, когда вызываются события [**TextUpdating**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textupdating) для клавиши ПРОБЕЛ, необходимо просто установить для параметра [**Result**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextupdatingeventargs.result) значение **Failed** в элементе управления редактированием.

### <a name="notifying-text-changes"></a>Уведомление об изменениях текста

Иногда элемент управления редактированием вносит изменения в текст, например при вставке текста или автоматических исправлениях. В таких случаях вы должны уведомить текстовые службы об этих изменениях, вызвав метод [**NotifyTextChanged**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.notifytextchanged).

Например, это состояние элемента управления редактированием до вставки пользователем слова World. Точка вставки — \[ 6, 6 \] .

![Пример диаграммы потока текста ](images/coretext/stream-5.png) . пользователь выполняет операцию вставки, и элемент управления "поле ввода" завершается следующим текстом:

![Пример диаграммы потока текста ](images/coretext/stream-4.png) . в этом случае следует вызвать [**нотифитекстчанжед**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.notifytextchanged) с этими аргументами:

-   *modifiedRange*  =  модифиедранже \[ 6, 6\]
-   *newLength* = 5
-   *newSelection*  =  невселектион \[ 11, 11\]

Последует одно или несколько событий [**TextRequested**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textrequested), которые вы обрабатываете для обновления текста, находящегося в ведении текстовых служб.

### <a name="overriding-text-updates"></a>Переопределение обновлений текста

В вашем элементе управления редактированием, возможно, вам придется переопределить обновление текста для предоставления функций автозамены.

Например, рассмотрим элемент управления редактированием, который предоставляет функцию исправления, оформляющую сужения. Это состояние элемента управления редактированием до того, как пользователь нажал клавишу ПРОБЕЛ для активации исправления. Точка вставки — \[ 3, 3 \] .

![Пример диаграммы потока текста ](images/coretext/stream-6.png) пользователь нажимает клавишу пробела и вызывается соответствующее событие [**текступдатинг**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textupdating) . Элемент управления редактированием принимает обновление текста. Это состояние элемента управления редактированием в течение короткого момента перед завершением исправления. Точка вставки — \[ 4, 4 \] .

![Пример диаграммы потока текста ](images/coretext/stream-7.png) вне обработчика событий [**текступдатинг**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textupdating) элемент управления "поле ввода" делает следующее исправление. Это состояние элемента управления редактированием после завершения исправления. Точка вставки — \[ 5, 5 \] .

![Пример диаграммы потока текста ](images/coretext/stream-8.png) . в этом случае следует вызвать [**нотифитекстчанжед**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.notifytextchanged) с этими аргументами:

-   *modifiedRange*  =  модифиедранже \[ 1, 2\]
-   *newLength* = 2
-   *newSelection*  =  невселектион \[ 5, 5\]

Последует одно или несколько событий [**TextRequested**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textrequested), которые вы обрабатываете для обновления текста, находящегося в ведении текстовых служб.

### <a name="providing-requested-text"></a>Предоставление запрашиваемого текста

Важно, чтобы текстовые службы имели правильный текст для предоставления таких функций, как автозамена или прогнозирование, особенно для текста, который уже существовал в элементе управления редактированием, например с загрузки документа, или текста, который вставлен элементом управления редактированием, как описано в предыдущих разделах. Поэтому, когда возникает событие [**TextRequested**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textrequested), необходимо предоставить текст, который в настоящее время находится в вашем элементе управления редактированием для определенного диапазона.

Иногда [**Range**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexttextrequest.range) в [**CoreTextTextRequest**](https://docs.microsoft.com/uwp/api/Windows.UI.Text.Core.CoreTextTextRequest) определяет диапазон, который ваш элемент управления редактированием не может вставить "как есть". Например, **Range** больше, чем размер элемента управления редактированием в момент события [**TextRequested**](https://docs.microsoft.com/uwp/api/windows.ui.text.core.coretexteditcontext.textrequested), или конец **Range** выходит за рамки. В таких случаях вы должны вернуть любой разумный диапазон. Обычно это подмножество запрашиваемого диапазона.

## <a name="related-articles"></a>Похожие статьи

### <a name="samples"></a>Примеры

- [Пример пользовательского элемента управления для редактирования](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CustomEditControl)

### <a name="archive-samples"></a>Примеры архива

- [Пример редактирования текста на XAML](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BVB%5D-Windows%208%20app%20samples/VB/Windows%208%20app%20samples/XAML%20text%20editing%20sample%20(Windows%208))
