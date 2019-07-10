---
description: Это руководство поможет вам создавать пользовательские интерфейсы UWP на основе принципов проектирования Fluent Design непосредственно в приложениях WPF и Windows Forms.
title: Элементы управления универсальной платформы Windows в приложениях для настольных систем
ms.date: 04/16/2019
ms.topic: article
keywords: Windows 10, универсальной платформы Windows, windows forms, wpf, о-ва xaml
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: 8ceb314424ae2611e141ef866a84c08e55b0ba2d
ms.sourcegitcommit: f9a30bfd1e8eab50d0b1db97dd2f650ce66b5d34
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67690885"
---
# <a name="host-uwp-xaml-controls-in-desktop-apps-xaml-islands"></a>Элементы управления XAML UWP узла в классических приложениях (XAML о-ва)

Начиная с Windows 10, версия 1903, вы можете размещать элементы управления универсальной платформы Windows в настольных приложениях не универсальной платформы Windows, используя компонент, именуемый *XAML о-ва*. Эта функция позволяет улучшить представление и функциональные возможности существующих классических приложений с новейшими функциями пользовательского интерфейса Windows 10, которые доступны только через элементы управления универсальной платформы Windows. Это означает, что можно использовать функции универсальной платформы Windows, такие как [рукописного ввода Windows](/windows/uwp/design/input/pen-and-stylus-interactions) и элементы управления, поддерживающие [Fluent Design System](/windows/uwp/design/fluent-design-system/index) в существующих WPF, Windows Forms и приложения C++ Win32.

Мы предоставляем несколько способов использования XAML острова в приложении WPF, Windows Forms, и C++ приложений Win32, в зависимости от технологии или платформы, которые вы используете. 

> [!NOTE]
> Если у вас есть отзыв о XAML о-ва, создание нового вопроса в [Microsoft.Toolkit.Win32 репозитория](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/issues) , а также оставить свои комментарии существует. Если вы хотите отправить свой отзыв в частном порядке, вы можете отправить его в XamlIslandsFeedback@microsoft.com. Свои предложения и сценарии критически важны для нас.

## <a name="how-do-xaml-islands-work"></a>Как работают острова XAML?

Начиная с Windows 10, версия 1903, мы предоставляем два способа использования XAML острова в приложении WPF, Windows Forms, и C++ приложений Win32:

* Пакет SDK Windows предоставляет несколько классов среды выполнения Windows и COM-интерфейсы, которые приложение может использовать для размещения любого элемента управления универсальной платформы Windows, который является производным от [ **Windows.UI.Xaml.UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement). В совокупности эти классы и интерфейсы, называются *XAML UWP, интерфейс API размещения*, и они позволяют создавать элементы управления ведущего приложения универсальной платформы Windows в любой элемент пользовательского интерфейса в приложении, которое содержит дескриптор окна (HWND). Дополнительные сведения об этом API см. в разделе [с помощью XAML, интерфейс API размещения](using-the-xaml-hosting-api.md).

* [Windows Community Toolkit](https://docs.microsoft.com/windows/uwpcommunitytoolkit/) также предоставляет дополнительные элементы управления остров XAML для WPF и Windows Forms. Эти элементы управления использовать XAML UWP, интерфейс API размещения внутренним образом и реализовать все возможности, в противном случае необходимо обработать самостоятельно, если вы использовали XAML UWP, интерфейс API размещения напрямую, включая клавиатуры меняется в навигации и макет. Для приложений WPF и Windows Forms настоятельно рекомендуется использовать эти элементы управления вместо XAML UWP размещение API непосредственно, так как они абстрактными многие детали реализации с помощью API. Обратите внимание, что начиная с Windows 10, версия 1903, эти элементы управления [доступна в предварительной версии разработчика](#feature-roadmap).

> [!NOTE]
> Классическим приложениям C++ Win32 необходимо использовать XAML UWP, интерфейс API размещения для размещения элементов управления универсальной платформы Windows. Элементы управления XAML, о-в наборе средств сообщества Windows недоступны для C++ классическим приложениям Win32.

Существует два типа элементов управления XAML остров, предоставляемые Windows Community Toolkit для приложений WPF и Windows Forms: *элементов управления в оболочку* и *элементы управления ведущего приложения*. 

### <a name="wrapped-controls"></a>Упакованное элементов управления

Приложения WPF и Windows Forms можно использовать набор выбранных оболочку элементов управления универсальной платформы Windows в [Windows Community Toolkit](https://docs.microsoft.com/windows/uwpcommunitytoolkit/). Они называются *элементов управления в оболочку* так, как они wrap интерфейса и функциональных возможностей конкретного элемента управления универсальной платформы Windows. Можно добавить эти элементы управления непосредственно в область конструктора проекта WPF или Windows Forms и затем использовать их как и любой другой WPF или Windows Forms элемент управления в конструкторе.

Следующие упакованного элементы управления универсальной платформы Windows для реализации XAML острова в настоящее время доступны для приложений WPF и Windows Forms.

| Элемент управления | Минимальная поддерживаемая ОС | Описание |
|-----------------|-------------------------------|-------------|
| [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas)<br>[InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) | Windows 10, версия 1903 | Укажите поверхности и сопутствующих инструментов для взаимодействия с пользователем на основе рукописного ввода Windows в своего настольного приложения Windows Forms или WPF. |
| [MediaPlayerElement](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/mediaplayerelement) | Windows 10, версия 1903 | Внедряет представление, которое выполняет потоковую передачу и отображает содержимое носителя, например видео в приложения Windows Forms или WPF для настольных компьютеров. |
| [MapControl](https://docs.microsoft.com/en-us/windows/communitytoolkit/controls/wpf-winforms/mapcontrol) | Windows 10, версия 1903 | Позволяет отображать карту символьную или фотографического качества в своего настольного приложения Windows Forms или WPF. |

В дополнение к оболочке элементы управления для XAML о-ва Windows Community Toolkit предоставляет следующие элементы управления для размещения веб-содержимого.

| Элемент управления | Минимальная поддерживаемая ОС | Описание |
|-----------------|-------------------------------|-------------|
| [WebView](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/webview) | Windows 10 версии 1803 | Использует механизм отрисовки Microsoft Edge для отображения веб-содержимого. |
| [WebViewCompatible](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/webviewcompatible) | Windows 7 | Предоставляет версию **WebView** совместимый с еще большим количеством версий операционной системы. Этот элемент управления использует механизм отрисовки Microsoft Edge для отображения веб-содержимого в Windows 10 версии 1803 и более поздних версий, а также механизм отрисовки Internet Explorer для отображения веб-содержимого в более ранних версиях Windows 10, Windows 8.x и Windows 7. |

### <a name="host-controls"></a>Элементы управления ведущего приложения

Для сценариев не устраняемых с помощью доступных элементов управления упакованного приложения WPF и Windows Forms можно также использовать [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) контролировать [Windows Community Toolkit](https://docs.microsoft.com/windows/uwpcommunitytoolkit/). Этот элемент управления можно разместить любой элемент управления универсальной платформы Windows, который является производным от [ **Windows.UI.Xaml.UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), включая любой элемент управления универсальной платформы Windows, предоставляемый пакетом SDK для Windows, а также собственные пользовательские элементы управления. Этот элемент управления поддерживает сборки пакета SDK для Windows 10 Insider Preview 17709 и более поздних версиях.

### <a name="architecture-overview"></a>Обзор архитектуры

Вот краткий обзор этих элементов управления с точки зрения архитектуры.

![Архитектура размещаемых элементов управления](images/xaml-islands/host-controls.png)

API-интерфейсы, представленные в нижней части этой схемы, входят в состав Windows SDK. Упакованное элементы управления и элементы управления ведущего приложения доступны посредством пакетов Nuget в наборе средств сообщества Windows.

## <a name="requirements"></a>Требования

О-ва XAML требуется Windows 10, версия 1903 и более поздние версии. В приложении в XAML о-ва, необходимо настроить проект.

### <a name="step-1-modify-your-project-to-use-windows-runtime-apis"></a>Шаг 1. Изменение проекта для использования API среды выполнения Windows

Инструкции см. в разделе [в этой статье](desktop-to-uwp-enhance.md#set-up-your-project).

### <a name="step-2-enable-xaml-island-support-in-your-project"></a>Шаг 2. Остров XAML включить поддержку в вашем проекте

Сделать одну из следующих изменений в проект, чтобы включить поддержку остров XAML. Дополнительные сведения см. в разделе [этой записи блога](https://techcommunity.microsoft.com/t5/Windows-Dev-AppConsult/Using-XAML-Islands-on-Windows-10-19H1-fixing-the-quot/ba-p/376330#M117).

#### <a name="option-1-package-your-application-in-an-msix-package"></a>Вариант 1. Приложение упаковано в пакет MSIX  

Установите Windows 10, версия 1903 пакета SDK (или более поздней версии). Затем пакет приложения в пакете MSIX путем добавления [проект упаковки приложений Windows](https:/docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) решение и добавить ссылку на проект WPF или Windows Forms.

#### <a name="option-2-set-the-maxversiontested-value-in-your-assembly-manifest"></a>Вариант 2. Задайте значение maxversiontested укажите в манифесте сборки

Если вы не хотите упаковать приложение в пакете MSIX, можно добавить [манифест приложения](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) в проект и добавьте **maxversiontested укажите** в манифест, чтобы указать, что ваш приложение совместимо с Windows 10, 1903 или более поздней версии.

1. Если у вас еще нет приложения манифеста в проекте, добавьте новый XML-файл в проект и назовите его **app.manifest**. Для приложений WPF и Windows Forms, убедитесь, что также назначить **манифеста** свойства **. app.manifest** в **приложения** странице вашего [проекта свойства](https://docs.microsoft.com/visualstudio/ide/reference/application-page-project-designer-csharp?view=vs-2019#resources).
2. В манифесте приложения, включают **совместимости** и дочерних элементов, показано в следующем примере. Замените **идентификатор** атрибут **maxversiontested укажите** элемент с номером версии, вы используете Windows 10 (это должен быть Windows 10, версия 1903 года или более поздней версии).

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
        <compatibility xmlns="urn:schemas-microsoft-com:compatibility.v1">
            <application>
                <!-- Windows 10 -->
                <maxversiontested Id="10.0.18362.0"/>
                <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}" />
            </application>
        </compatibility>
    </assembly>
    ```

> [!NOTE]
> При добавлении **maxversiontested укажите** манифест приложения в C++ Win32 проекта (с помощью шаблона проекта классического приложения Windows в Visual Studio), может появиться следующее предупреждение построения в проекте: `manifest authoring warning 81010002: Unrecognized Element "maxversiontested" in namespace "urn:schemas-microsoft-com:compatibility.v1"` . Это предупреждение указывает, что-то не так в проекте, что его можно пропустить.

## <a name="feature-roadmap"></a>Схема компонентов

В выпуске Windows 10, версия 1903, упакованное элементы управления и элементы управления ведущего приложения в наборе средств сообщества Windows, по-прежнему в состоянии предварительной версии до версии 1.0, элементов управления.

* Версии 1.0, элементов управления для платформы .NET Framework 4.6.2 и более поздней версии мы планируем выпуск ожидается в [6.0 выпуск набора средств](https://github.com/windows-toolkit/WindowsCommunityToolkit/milestones).
* Элементы управления для .NET Core 3 версии 1.0 планируются для более поздней версии набора средств.
* Если вы хотите попробовать выполнить последние предварительные версии выпусков версии 1.0, эти элементы управления .NET Framework и .NET Core 3, см. в разделе **6.0.0-preview3** пакетов NuGet в [UWP Community Toolkit](https://dotnet.myget.org/gallery/uwpcommunitytoolkit) коллекции.

Дополнительные сведения см. в разделе [этой записи блога](https://blogs.windows.com/windowsdeveloper/2019/06/13/xaml-islands-v1-updates-and-roadmap).

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения и руководства об использовании XAML о-ва см. следующие статьи и справочные материалы:

* [Модернизируйте учебник приложения WPF](modernize-wpf-tutorial.md): Этот учебник содержит пошаговые инструкции по использованию оболочку элементы управления и элементы управления ведущего приложения в наборе средств сообщества Windows для добавления элементов управления универсальной платформы Windows для существующего бизнес-приложения WPF. Этот учебник содержит полный код для приложения WPF, а также подробные инструкции для каждого шага в процессе.
* [О-ва XAML v1 - схема и обновления](https://blogs.windows.com/windowsdeveloper/2019/06/13/xaml-islands-v1-updates-and-roadmap): Этой записи блога рассматриваются многие часто задаваемые вопросы об островах XAML и предоставляет подробные развития.
