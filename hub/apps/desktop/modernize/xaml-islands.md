---
description: Это руководство поможет вам создавать пользовательские интерфейсы UWP на основе принципов проектирования Fluent Design непосредственно в приложениях WPF и Windows Forms.
title: Элементы управления UWP в классических приложениях
ms.date: 07/26/2019
ms.topic: article
keywords: Windows 10, UWP, Windows Forms, WPF, о-ва XAML
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: 560d339476ef3cd45f30bfc678661fb0a4a11ee1
ms.sourcegitcommit: f6af7aeb8506379a184207035c8e43288cb31453
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601526"
---
# <a name="host-uwp-xaml-controls-in-desktop-apps-xaml-islands"></a>Размещение элементов управления XAML UWP в классических приложениях (острова XAML)

Начиная с Windows 10 версии 1903, можно размещать элементы управления UWP в настольных приложениях, не являющихся UWP, с помощью функции, именуемой " *острова XAML*". Эта функция позволяет улучшить внешний вид и функциональные возможности существующих настольных приложений с помощью новейших функций пользовательского интерфейса Windows 10, которые доступны только через элементы управления UWP. Это означает, что вы можете использовать такие функции UWP, как [Рукописный ввод](/windows/uwp/design/input/pen-and-stylus-interactions) и элементы управления Windows, поддерживающие [систему разработки Fluent](/windows/uwp/design/fluent-design-system/index) в существующих приложениях C++ WPF, Windows Forms и Win32.

Мы предоставляем несколько способов использования островов XAML в приложениях WPF, Windows Forms и C++ Win32 в зависимости от используемой технологии или платформы. 

> [!NOTE]
> Если у вас есть отзывы о островах XAML, создайте новую ошибку в [репозитории Microsoft. Toolkit. Win32](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/issues) и оставьте свои комментарии. Если вы предпочитаете отправить отзыв в частном порядке, его можно отправить в XamlIslandsFeedback@microsoft.com. Ваши ценные сведения и сценарии критически важны для нас.

## <a name="how-do-xaml-islands-work"></a>Как работают острова XAML?

Начиная с Windows 10 версии 1903 мы предоставляем два способа использования островов XAML в приложениях WPF, Windows Forms и C++ Win32:

* Windows SDK предоставляет несколько классов среда выполнения Windows и COM-интерфейсов, которые приложение может использовать для размещения любого элемента управления UWP, производного от [**Windows. UI. XAML. UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement). В совокупности эти классы и интерфейсы называются *API размещения класса UWP XAML*, и они позволяют размещать элементы управления UWP в любом элементе пользовательского интерфейса в приложении, имеющем связанный дескриптор окна (HWND). Дополнительные сведения об этом API см. [в разделе Использование API размещения XAML](using-the-xaml-hosting-api.md).

* [Набор средств Windows Community Toolkit](https://docs.microsoft.com/windows/uwpcommunitytoolkit/) также предоставляет дополнительные элементы управления островами XAML для WPF и Windows Forms. Эти элементы управления используют API размещения универсального кода класса UWP для внутреннего использования и реализуют все поведение, которое в противном случае необходимо для самостоятельной работы, если вы используете API размещения в формате UWP XAML напрямую, включая навигацию с помощью клавиатуры и изменение макета. Для приложений WPF и Windows Forms мы настоятельно рекомендуем использовать эти элементы управления вместо API размещения класса UWP XAML напрямую, так как они являются абстрактным множеством деталей реализации использования API. Обратите внимание, что начиная с Windows 10 версия 1903, эти элементы управления [доступны в качестве предварительной версии для разработчиков](#feature-roadmap).

> [!NOTE]
> C++Классические приложения Win32 должны использовать API размещения UWP XAML для размещения элементов управления UWP. Элементы управления "Остров XAML" в наборе средств Windows Community Toolkit C++ недоступны для классических приложений Win32.

Существует два типа элементов управления острова XAML, предоставляемых набором средств Windows Community Toolkit для WPF и Windows Forms приложений: *инкапсулированные элементы управления* и *элементы управления ведущего*приложения. 

### <a name="wrapped-controls"></a>Обтекаемые элементы управления

Приложения WPF и Windows Forms могут использовать оболочку с упакованными элементами управления UWP в [наборе средств сообщества Windows](https://docs.microsoft.com/windows/uwpcommunitytoolkit/). Они называются *упакованными элементами управления* , так как они заключают интерфейс и функциональные возможности определенного элемента управления UWP. Эти элементы управления можно добавить непосредственно в область конструктора проекта WPF или Windows Forms, а затем использовать их как любой другой элемент управления WPF или Windows Forms в конструкторе.

Следующие Упакованные элементы управления UWP для реализации островов XAML в настоящее время доступны для WPF (см. пакет [Microsoft. Toolkit. WPF. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls) ) и Windows Forms приложений (см. пакет [Microsoft. Toolkit. Forms. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls) ). ).

| Элемент управления | Минимальная поддерживаемая ОС | Описание |
|-----------------|-------------------------------|-------------|
| [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas)<br>[InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) | Windows 10, версия 1903 | Укажите поверхность и связанные панели инструментов для взаимодействия пользователя с Windows на основе рукописного ввода в Windows Forms или в классическом приложении WPF. |
| [медиаплайерелемент](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/mediaplayerelement) | Windows 10, версия 1903 | Внедряет представление, которое выполняет потоковую передачу и визуализацию мультимедийного содержимого, например видео в Windows Forms или в классических приложениях WPF. |
| [MapControl](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/mapcontrol) | Windows 10, версия 1903 | Позволяет отображать символьную или реалистичную карту в Windows Forms или в классическом приложении WPF. |

Помимо упакованных элементов управления для островов XAML, набор средств сообщества Windows Community также предоставляет следующие элементы управления для размещения веб-содержимого в WPF (см [. пакет Microsoft. Toolkit. WPF. UI. Controls. WebView](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls.WebView) Package) и приложений Windows Forms (см. пакет [Microsoft. Toolkit. Forms. UI. Controls. WebView](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls.WebView) ).

| Элемент управления | Минимальная поддерживаемая ОС | Описание |
|-----------------|-------------------------------|-------------|
| [WebView](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/webview) | Windows 10 версии 1803 | Использует механизм визуализации Microsoft ребра для отображения веб-содержимого. |
| [вебвиевкомпатибле](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/webviewcompatible) | Windows 7 | Предоставляет версию **WebView** , совместимую с дополнительными версиями ОС. Этот элемент управления использует механизм визуализации Microsoft ребра для отображения веб-содержимого в Windows 10 версии 1803 и более поздних версий, а также механизм подготовки отчетов Internet Explorer для отображения веб-содержимого в более ранних версиях Windows 10, Windows 8. x и Windows 7. |

### <a name="host-controls"></a>Элементы управления ведущего приложения

Для сценариев, которые выходят за рамки доступных упакованных элементов управления, приложения WPF и Windows Forms могут также использовать элемент управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в [наборе средств сообщества Windows](https://docs.microsoft.com/windows/uwpcommunitytoolkit/). Этот элемент управления может размещать любой элемент управления UWP, производный от [**Windows. UI. XAML. UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), включая любой элемент управления UWP, предоставленный Windows SDK, а также пользовательские элементы управления. Этот элемент управления поддерживает предварительную версию пакета SDK для Windows 10 предварительной версии 17709 и более поздних версий.

Эти элементы управления доступны в пакетах [Microsoft. Toolkit. WPF. UI. ксамлхост](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.XamlHost) (для WPF) и [Microsoft. Toolkit. Forms. UI. ксамлхост](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.XamlHost) (для Windows Forms). Эти пакеты включены в пакеты [Microsoft. Toolkit. WPF. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls) и [Microsoft. Toolkit. Forms. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls) , которые содержат элементы управления с оболочкой.

### <a name="architecture-overview"></a>Обзор архитектуры

Вот краткий обзор этих элементов управления с точки зрения архитектуры.

![Архитектура размещаемых элементов управления](images/xaml-islands/host-controls.png)

API-интерфейсы, представленные в нижней части этой схемы, входят в состав Windows SDK. Упакованные элементы управления и элементы управления ведущего приложения доступны через пакеты NuGet в наборе средств сообщества Windows.

<span id="requirements" />

## <a name="configure-your-project-to-use-xaml-islands"></a>Настройка проекта для использования островов XAML

Для всех островов XAML требуется Windows 10, версия 1903 и более поздние версии. Чтобы использовать острова XAML в приложении, необходимо сначала настроить проект:

1. Измените проект, чтобы использовать среда выполнения Windows API. Инструкции см. в [этой статье](desktop-to-uwp-enhance.md#set-up-your-project).
2. Установите один из этих пакетов NuGet в проекте. Убедитесь, что установлена версия 6.0.0-Preview 6.4 или более поздняя версия пакета.
    * WPF Установка [Microsoft. Toolkit. WPF. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls)
    * Windows Forms: [Microsoft. Toolkit. Forms. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls)
    * C++Платформа [Microsoft. Toolkit. Win32. UI. Ксамлаппликатион](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication)

> [!NOTE]
> В предыдущих версиях этих инструкций был добавлен элемент **maxversiontested укажите установленную** в манифест приложения в проекте. Начиная с последней предварительной версии пакетов NuGet, вам больше не нужно добавлять этот элемент в манифест.

## <a name="feature-roadmap"></a>Схема возможностей

Начиная с выпуска Windows 10 версии 1903, Упакованные элементы управления и элементы управления ведущего приложения в наборе средств Windows Community Toolkit остаются в предварительной версии для разработчиков до тех пор, пока не будет выпущена версия 1,0 элементов управления.

* Версии 1,0 элементов управления для .NET Framework 4.6.2 и более поздних версий планируется выпустить в [выпуске 6,0 набора средств](https://github.com/windows-toolkit/WindowsCommunityToolkit/milestones).
* Версия 1,0 элементов управления для .NET Core 3 планируется для более поздней версии набора средств.
* Если вы хотите ознакомиться с последним предварительным просмотром выпусков этих элементов управления версии 1,0 для .NET Framework и .NET Core 3, см. пакеты NuGet **6.0.0-Preview 6.4** в коллекции [набора средств сообщества UWP](https://dotnet.myget.org/gallery/uwpcommunitytoolkit) .

Дополнительные сведения см. в разделе [этой записи блога](https://blogs.windows.com/windowsdeveloper/2019/06/13/xaml-islands-v1-updates-and-roadmap).

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения и руководства по использованию островов XAML см. в следующих статьях и ресурсах:

* [Руководство по модернизировать в приложении WPF](modernize-wpf-tutorial.md): В этом руководстве содержатся пошаговые инструкции по использованию упакованных элементов управления и элементов управления ведущего приложения в наборе средств сообщества Windows для добавления элементов управления UWP в существующее бизнес-приложение WPF. Этот учебник содержит полный код для приложения WPF, а также подробные инструкции для каждого этапа процесса.
* [Острова XAML v1 — обновления и планы развития](https://blogs.windows.com/windowsdeveloper/2019/06/13/xaml-islands-v1-updates-and-roadmap): В этой записи блога обсуждаются многие распространенные вопросы о островах XAML, а также подробная схема разработки.
