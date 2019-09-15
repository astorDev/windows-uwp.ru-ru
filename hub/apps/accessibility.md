---
title: Специальные возможности в Windows 10
description: На этой странице содержатся сведения, которые помогут приступить к разработке доступных приложений Windows.
ms.topic: article
ms.date: 09/12/2019
keywords: Специальные возможности в Windows 10, Специальные возможности, создание доступных приложений Win32, создание доступных приложений UWP, создание доступных приложений WPF, создание доступных приложений WinForms
ms.author: kbridge
author: Karl-Bridge-Microsoft
ms.openlocfilehash: 76bbd3f0e04bbb2f729ad0950bae190b2fffb6ac
ms.sourcegitcommit: 6e7665b457ec4585db19b70acfa2554791ad6e10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70987188"
---
# <a name="accessibility-in-windows-10"></a>Специальные возможности в Windows 10

![Херо-акцессибилити-бар-смаллер. png](images/hero-accessibility-bar-smaller.png)

## <a name="build-accessibility-into-your-applications-to-empower-people-of-all-abilities"></a>Создавайте специальные возможности в приложениях, чтобы предоставить пользователям все возможности

Продукты и услуги, включая электронные носители, доступны, когда они предназначены для полного и успешного взаимодействия с максимально возможным количеством пользователей.

Создавайте доступные и инклюзивные приложения Windows с улучшенными функциональными возможностями и удобством использования для людей с ограниченными возможностями (временный и постоянный), личных настроек, специальных стилей работы или ограничений, связанных с ситуацией (например, общие рабочие пространства). движущиеся, приготовление, покрытие и т. д. Некоторые распространенные решения включают в себя предоставление сведений в альтернативных форматах (например, субтитры на видео) или разрешение использования вспомогательных технологий (таких как средства чтения с экрана).

**Каждый должен иметь доступ к одним и тем же комнатам в здании независимо от того, нужно ли использовать лестницу или Лифт.**

На этой странице содержатся сведения о том, как различные платформы разработки Windows обеспечивают поддержку специальных возможностей для разработчиков, создающих приложения Windows, разработчиков вспомогательных технологий, таких как средства чтения с экрана и экранные лупы, а также программное обеспечение инженеры тестирования, создающие автоматизированные сценарии для тестирования приложений.

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
      ![Платформа WPF](images/platform-wpf2-small.png)

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
:::row:::
   :::column span="2":::
      **Веб-доступ**

      Проектирование, сборка и тестирование доступных веб-сайтов в Microsoft ребро.
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [Общие сведения о веб-специальных возможностях](https://docs.microsoft.com/microsoft-edge/accessibility)

      [Проектирование доступных веб-сайтов](https://docs.microsoft.com/microsoft-edge/accessibility/design)
   :::column-end:::
   :::column:::
      [Создание доступных веб-сайтов ](https://docs.microsoft.com/microsoft-edge/accessibility/build)

      [Тестирование доступных веб-сайтов](https://docs.microsoft.com/microsoft-edge/accessibility/test)
   :::column-end:::
:::row-end:::

## <a name="samples"></a>Примеры

Загрузите и запустите полный набор примеров Windows, демонстрирующих различные специальные возможности и функциональные возможности.

:::row:::
   :::column:::
      [Браузер с примером кода](https://docs.microsoft.com/en-us/samples/browse/)

      Новый браузер примеров заменяет коллекцию кода MSDN.
   :::column-end:::
   :::column:::
      [Коллекция кода MSDN (с снятой с учета)](https://code.msdn.microsoft.com/site/search?query=accessibility&f%5B0%5D.Value=accessibility&f%5B0%5D.Type=SearchText&ac=2)

      Загрузите примеры для Windows, Windows Phone, Microsoft Azure, Office, SharePoint, Silverlight и других продуктов.
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [Классические примеры Windows на GitHub](https://github.com/microsoft/Windows-classic-samples/search?q=accessibility&unscoped_q=accessibility)

      В этих примерах демонстрируются функциональные возможности и модель программирования для Windows и Windows Server. 
   :::column-end:::
   :::column:::
      [Примеры универсальная платформа Windows (UWP) на GitHub](https://github.com/microsoft/Windows-universal-samples/search?q=accessibility&unscoped_q=accessibility)

      В этих примерах демонстрируются шаблоны использования API для универсальная платформа Windows (UWP) в пакете средств разработки программного обеспечения (SDK) для Windows 10.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Коллекция элементов управления XAML](https://github.com/microsoft/Xaml-Controls-Gallery)

      В этом приложении демонстрируются различные элементы управления XAML, поддерживаемые в системе разработки Fluent.
   :::column-end:::
:::row-end:::

## <a name="videos"></a>Видео

Различные видеоматериалы, охватывающие создание приложений Windows с поддержкой специальных возможностей и их адресации в корпорации Майкрософт.

:::row:::
   :::column:::
      **Как приступить к работе со специальными возможностями в приложениях Windows**
   :::column-end:::
   :::column:::
      **Одна минута разработки: Разработка приложений для специальных возможностей**
   :::column-end:::
   :::column:::
      **Введение в инвалидность и специальные возможности**
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      > [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4072/player]
   :::column-end:::
   :::column:::
      > [!VIDEO https://channel9.msdn.com/Blogs/One-Dev-Minute/Developing-Apps-for-Accessibility/player]
   :::column-end:::
   :::column:::
      > [!VIDEO https://www.youtube.com/embed/Kl4CT4DaypM]
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      **От взлома до продукта, управление глазами для Windows 10**
   :::column-end:::
   :::column:::
      **Специальные возможности в Windows 10**
   :::column-end:::
   :::column:::
      **Общие сведения о создании доступных приложений UWP**
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      > [!VIDEO https://www.youtube.com/embed/AShNPfmAkvY]
   :::column-end:::
   :::column:::
      > [!VIDEO https://channel9.msdn.com/Events/Build/2016/P541/player]
   :::column-end:::
   :::column:::
      > [!VIDEO https://channel9.msdn.com/Events/Build/2016/P497/player]
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      **Разработка специальных возможностей Windows**
   :::column-end:::
   :::column:::
      **Специальные возможности Windows 10 позволяют всем пользователям**
   :::column-end:::
   :::column:::
      **Упрощение просмотра указателей мыши**
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      > [!VIDEO https://www.youtube.com/embed/Y_NJbE7wxlk]
   :::column-end:::
   :::column:::
      > [!VIDEO https://www.youtube.com/embed/BseTf-4q9GA]
   :::column-end:::
   :::column:::
      > [!VIDEO https://www.youtube.com/embed/4UzaF7_T3bw]
   :::column-end:::
:::row-end:::

## <a name="other-resources"></a>Другие ресурсы

:::row:::
   :::column span="3":::
      **Блоги и новости**

      Новейшее из мира Microsoft Accessibility.
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [В новостях](https://news.microsoft.com/presskits/accessibility/)
   :::column-end:::
   :::column:::
      [Блоги по специальным возможностям](https://blogs.microsoft.com/accessibility/)
   :::column-end:::
   :::column:::
      [Блоги по автоматизации пользовательского интерфейса Windows](https://blogs.msdn.microsoft.com/winuiautomation/)
   :::column-end:::
:::row-end:::

:::row:::
   :::column span="3":::
      **Сообщество и поддержка**

      Место, где разработчики и пользователи Windows соблюдают и изучены вместе.
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [Сообщество Windows — специальные возможности](https://community.windows.com/search?q=accessibility)
   :::column-end:::
   :::column:::
      [Форум по разработке специальных возможностей и автоматизации Windows](https://social.msdn.microsoft.com/Forums/windows/home?forum=windowsaccessibilityandautomation)
   :::column-end:::
   :::column:::
      [Служба ответов с ограниченными ИТ](https://www.microsoft.com/Accessibility/disability-answer-desk)
   :::column-end:::
:::row-end:::
