---
title: Специальные возможности в Windows 10
description: На этой странице содержатся сведения, которые помогут приступить к разработке доступных приложений Windows.
ms.topic: article
ms.date: 04/03/2019
ms.localizationpriority: medium
ms.author: kbridge
author: Karl-Bridge-Microsoft
keywords: Специальные возможности в Windows 10, Специальные возможности, создание доступных приложений Win32, создание доступных приложений UWP, создание доступных приложений WPF, создание доступных приложений WinForms
ms.openlocfilehash: 63d9b321f71019c164fe4de238a618f2730b2052
ms.sourcegitcommit: 81213db9de2c12400d2e4d176346b466ee0794c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70237786"
---
# <a name="accessibility-in-windows-10"></a>Специальные возможности в Windows 10

![Херо-акцессибилити-бар-смаллер. png](images/hero-accessibility-bar-smaller.png)

Доступ к специальным приложениям обеспечивается за счет повышения удобства использования для максимально возможного количества людей, включая пользователей с ограниченными возможностями, личные настройки, Специальные стили работы или ограничения на ситуацию (например, движущие, приготовленные и т. д.).

На этой странице содержатся сведения о том, как различные платформы разработки Windows поддерживают специальные возможности для разработчиков, создающих приложения Windows, разработчиков вспомогательных технологий, таких как средства чтения с экрана и экранные лупы, а также тестирование программного обеспечения. инженеры, создающие автоматизированные сценарии для тестирования приложений.

## <a name="platform-specific-documentation"></a>Документация для определенных платформ

:::row:::
   :::column:::
      ![Универсальная платформа Windows (UWP)](images/platform-uwp.png)

      **Универсальная платформа Windows (UWP)**

      Разрабатывайте доступные приложения и средства на современных платформах для приложений и игр Windows 10 на любом устройстве Windows (включая ПК, телефоны, Xbox One, HoloLens и др.) и публикуйте их в Microsoft Store.

      [Проектирование инклюзивного программного обеспечения](https://docs.microsoft.com/windows/uwp/accessibility/designing-inclusive-software)

      [Разработка инклюзивных приложений для Windows](https://docs.microsoft.com/windows/uwp/accessibility/developing-inclusive-windows-apps)

      [Проверка специальных возможностей](https://docs.microsoft.com/windows/uwp/accessibility/accessibility-testing)

      [Специальные возможности в Магазине](https://docs.microsoft.com/windows/uwp/accessibility/accessibility-in-the-store)
   :::column-end:::
   :::column:::
      ![Приложения платформы Win32](images/platform-win32.png)

      **Платформа Win32**

      Разрабатывайте доступные приложения и средства на исходной платформе для приложений CC++ /Windows.

      [Новые возможности в области специальных возможностей и автоматизации Windows](https://docs.microsoft.com/windows/desktop/accessibility-whatsnew)

      [Разработка приложений со специальными возможностями для Windows](https://docs.microsoft.com/windows/desktop/accessibility-appdev)

      [Разработка доступных платформ пользовательского интерфейса для Windows](https://docs.microsoft.com/windows/desktop/accessibility-uiframeworkdev)

      [Разработка вспомогательных технологий для Windows](https://docs.microsoft.com/windows/desktop/accessibility-atdev)

      [Проверка специальных возможностей](https://docs.microsoft.com/windows/desktop/accessibility-testwithuia)

      [Устаревшие технологии обеспечения доступности и автоматизации — MSAA для модели автоматизации пользовательского интерфейса](https://docs.microsoft.com/windows/desktop/accessibility-legacy)

      [Специальные возможности Windows](https://docs.microsoft.com/windows/desktop/winauto/about-windows-accessibility-features)

      [Рекомендации по разработке приложений со специальными возможностями](https://docs.microsoft.com/windows/desktop/uxguide/inter-accessibility)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      ![Платформа WPF](images/platform-wpf.png)

      **Windows Presentation Foundation (WPF)**

      Разрабатывайте доступные приложения и средства на установленной платформе для управляемых приложений Windows с помощью модели пользовательского интерфейса XAML и .NET Framework.

      [Рекомендации по специальным возможностям](https://docs.microsoft.com/dotnet/framework/ui-automation/accessibility-best-practices)

      [Основы модели автоматизации пользовательского интерфейса](https://docs.microsoft.com/dotnet/framework/ui-automation/index)

      [Поставщики автоматизации пользовательского интерфейса для управляемого кода](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-providers-for-managed-code)

      [Клиенты автоматизации пользовательского интерфейса для управляемого кода](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-clients-for-managed-code)

      [Шаблоны элементов управления модели автоматизации пользовательского интерфейса](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-control-patterns)

      [Шаблон текста модели автоматизации пользовательского интерфейса](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-text-pattern)

      [Типы элементов управления модели автоматизации пользовательского интерфейса](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-control-types)

      [Спецификация модели автоматизации пользовательского интерфейса и обещание сообщества](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-specification-and-community-promise)
      :::column-end:::
   :::column:::
      ![Приложения платформы Windows Forms](images/platform-winforms.png)

      **Windows Forms (WinForms)**

      Разрабатывайте доступные приложения и средства для управляемых приложений Windows с помощью модели пользовательского интерфейса XAML и .NET Framework.

      [Специальные возможности Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/advanced/windows-forms-accessibility)

      [Создание приложения Windows со специальными возможностями](https://docs.microsoft.com/dotnet/framework/winforms/advanced/walkthrough-creating-an-accessible-windows-based-application)

      [Свойства элементов управления Windows Forms, которые поддерживают рекомендации по обеспечению доступности](https://docs.microsoft.com/dotnet/framework/winforms/advanced/properties-on-windows-forms-controls-that-support-accessibility-guidelines)

      [Предоставление сведений о специальных возможностях для элементов управления в форме Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/controls/providing-accessibility-information-for-controls-on-a-windows-form)
   :::column-end:::
:::row-end:::
