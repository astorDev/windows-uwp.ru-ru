---
title: Специальные возможности в Windows 10
description: На этой странице содержатся сведения, которые помогут приступить к разработке приложений Windows со специальными возможностями.
ms.topic: article
ms.date: 09/12/2019
keywords: Специальные возможности в Windows 10, специальные возможности, создание приложений win32 со специальными возможностями, создание приложений UWP со специальными возможностями, создание приложений WPF со специальными возможностями, создание приложений WinForms со специальными возможностями
ms.author: kbridge
author: Karl-Bridge-Microsoft
ms.openlocfilehash: 76bbd3f0e04bbb2f729ad0950bae190b2fffb6ac
ms.sourcegitcommit: 6e7665b457ec4585db19b70acfa2554791ad6e10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70987188"
---
# <a name="accessibility-in-windows-10"></a>Специальные возможности в Windows 10

![hero-accessibility-bar-smaller.png](images/hero-accessibility-bar-smaller.png)

## <a name="build-accessibility-into-your-applications-to-empower-people-of-all-abilities"></a>Встраивание специальных возможностей в приложения, чтобы они были доступны для каждого пользователя

Продукты и службы, включая электронные мультимедиа, предоставляют специальные возможности, если они обеспечивают полные и эффективные возможности взаимодействия для максимально возможного количества пользователей.

Создавайте инклюзивные приложения Windows с улучшенными функциональными возможностями и удобством использования для людей с ограниченными возможностями (временными и постоянными), особыми предпочтениями, разными стилями работы или ситуационными ограничениями (например, общие рабочие пространства, работа во время вождения, готовки, отблеск поверхности и т. д.). Некоторые распространенные решения включают в себя предоставление сведений в альтернативных форматах (например, субтитры на видео) или разрешение использования вспомогательных технологий (таких как средства чтения с экрана).

**У всех должен быть доступ к одним и тем же помещениям в здании, независимо от того, что нужно использовать: лестницу или лифт.**

На этой странице содержатся сведения о том, как различные платформы разработки Windows обеспечивают поддержку специальных возможностей для разработчиков, создающих приложения Windows, разработчиков вспомогательных технологий, создающих такие инструменты, как средства чтения с экрана и экранные увеличители, а также для инженеров тестирования программного обеспечения, создающих автоматизированные сценарии для тестирования приложений.

## <a name="platform-specific-documentation"></a>Документация для определенных платформ

:::row:::
   :::column:::
      ![Универсальная платформа Windows (UWP)](images/platform-uwp.png)

      **Универсальная платформа Windows (UWP)**

      Разрабатывайте приложения и средства со специальными возможностями на современных платформах для приложений и игр в Windows 10 на любом устройстве с Windows (включая ПК, телефоны, Xbox One, HoloLens и др.) и публикуйте их в Microsoft Store.

      [Проектирование инклюзивного программного обеспечения](https://docs.microsoft.com/windows/uwp/accessibility/designing-inclusive-software)

      [Разработка инклюзивных приложений для Windows](https://docs.microsoft.com/windows/uwp/accessibility/developing-inclusive-windows-apps)

      [Проверка специальных возможностей](https://docs.microsoft.com/windows/uwp/accessibility/accessibility-testing)

      [Специальные возможности в Магазине](https://docs.microsoft.com/windows/uwp/accessibility/accessibility-in-the-store)
   :::column-end:::
   :::column:::
      ![Приложения платформы Win32](images/platform-win32.png)

      **Платформа Win32**

      Разрабатывайте приложения и средства со специальными возможностями на исходной платформе для приложений C/C++ Windows.

      [Что нового в области специальных возможностей и автоматизации Windows](https://docs.microsoft.com/windows/desktop/accessibility-whatsnew)

      [Разработка приложений со специальными возможностями для Windows](https://docs.microsoft.com/windows/desktop/accessibility-appdev)

      [Разработка платформ пользовательского интерфейса со специальными возможностями для Windows](https://docs.microsoft.com/windows/desktop/accessibility-uiframeworkdev)

      [Разработка вспомогательных технологий для Windows](https://docs.microsoft.com/windows/desktop/accessibility-atdev)

      [Тестирование специальных возможностей](https://docs.microsoft.com/windows/desktop/accessibility-testwithuia)

      [Устаревшие технологии обеспечения специальных возможностей и автоматизации — от MSAA до автоматизации пользовательского интерфейса](https://docs.microsoft.com/windows/desktop/accessibility-legacy)

      [Специальные возможности Windows](https://docs.microsoft.com/windows/desktop/winauto/about-windows-accessibility-features)

      [Рекомендации по разработке приложений со специальными возможностями](https://docs.microsoft.com/windows/desktop/uxguide/inter-accessibility)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      ![Платформа WPF](images/platform-wpf2-small.png)

      **Windows Presentation Foundation (WPF)**

      Разрабатывайте приложения и средства со специальными возможностями на установленной платформе для управляемых приложений Windows с помощью модели пользовательского интерфейса XAML и .NET Framework.

      [Рекомендации по специальным возможностям](https://docs.microsoft.com/dotnet/framework/ui-automation/accessibility-best-practices)

      [Основы модели автоматизации пользовательского интерфейса](https://docs.microsoft.com/dotnet/framework/ui-automation/index)

      [Поставщики автоматизации пользовательского интерфейса для управляемого кода](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-providers-for-managed-code)

      [Клиенты автоматизации пользовательского интерфейса для управляемого кода](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-clients-for-managed-code)

      [Шаблоны элементов управления модели автоматизации пользовательского интерфейса](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-control-patterns)

      [Шаблон текста для модели автоматизации пользовательского интерфейса](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-text-pattern)

      [Типы элементов управления для автоматизации пользовательского интерфейса](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-control-types)

      [Спецификация модели автоматизации пользовательского интерфейса и Обещание сообществу](https://docs.microsoft.com/dotnet/framework/ui-automation/ui-automation-specification-and-community-promise)
   :::column-end:::
   :::column:::
      ![Приложения платформы Windows Forms](images/platform-winforms.png)

      **Windows Forms (WinForms)**

      Разрабатывайте приложения и средства со специальными возможностями для управляемых приложений Windows с помощью модели пользовательского интерфейса XAML и .NET Framework.

      [Поддержка специальных возможностей в Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/advanced/windows-forms-accessibility)

      [Создание приложения Windows с поддержкой специальных возможностей](https://docs.microsoft.com/dotnet/framework/winforms/advanced/walkthrough-creating-an-accessible-windows-based-application)

      [Свойства элементов управления Windows Forms, в которых соблюдены правила доступности](https://docs.microsoft.com/dotnet/framework/winforms/advanced/properties-on-windows-forms-controls-that-support-accessibility-guidelines)

      [Определение сведений, связанных со специальными возможностями, для элементов управления в Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/controls/providing-accessibility-information-for-controls-on-a-windows-form)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      **Специальные возможности в Интернете**

      Проектируйте, выполняйте сборку и тестирование веб-сайтов со специальными возможностями в Microsoft Edge.
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [Знакомство со специальными возможностями в Интернете](https://docs.microsoft.com/microsoft-edge/accessibility)

      [Проектирование доступных веб-сайтов](https://docs.microsoft.com/microsoft-edge/accessibility/design)
   :::column-end:::
   :::column:::
      [Создание доступных веб-сайтов ](https://docs.microsoft.com/microsoft-edge/accessibility/build)

      [Тестирование доступных веб-сайтов](https://docs.microsoft.com/microsoft-edge/accessibility/test)
   :::column-end:::
:::row-end:::

## <a name="samples"></a>Примеры

Загрузите и запустите полные примеры Windows, демонстрирующие различные компоненты и функции специальных возможностей.

:::row:::
   :::column:::
      [Браузер с примером кода](https://docs.microsoft.com/en-us/samples/browse/)

      Новый браузер примеров заменяет коллекцию кода MSDN.
   :::column-end:::
   :::column:::
      [Коллекция кода MSDN (прекращение использования)](https://code.msdn.microsoft.com/site/search?query=accessibility&f%5B0%5D.Value=accessibility&f%5B0%5D.Type=SearchText&ac=2)

      Загрузите примеры для Windows, Windows Phone, Microsoft Azure, Office, SharePoint, Silverlight и других продуктов.
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [Классические примеры Windows на GitHub](https://github.com/microsoft/Windows-classic-samples/search?q=accessibility&unscoped_q=accessibility)

      В этих примерах демонстрируются функциональные возможности и модель программирования для Windows и Windows Server. 
   :::column-end:::
   :::column:::
      [Примеры универсальной платформы Windows (UWP) на GitHub](https://github.com/microsoft/Windows-universal-samples/search?q=accessibility&unscoped_q=accessibility)

      В этих примерах демонстрируются шаблоны использования API для универсальной платформы Windows (UWP) в пакете средств разработки программного обеспечения (SDK) для Windows 10.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Коллекция элементов управления XAML](https://github.com/microsoft/Xaml-Controls-Gallery)

      В этом приложении демонстрируются различные элементы управления Xaml, поддерживаемые в системе Fluent Design.
   :::column-end:::
:::row-end:::

## <a name="videos"></a>Видео

Различные видеоматериалы, охватывающие темы от создания приложений Windows с поддержкой специальных возможностей до проблем общей доступности и того, как Microsoft их решает.

:::row:::
   :::column:::
      **Начало работы со специальными возможностями в приложениях Windows**
   :::column-end:::
   :::column:::
      **Блог One Dev Minute: Разработка специальных возможностей для приложений**
   :::column-end:::
   :::column:::
      **Общие сведения на тему ограниченных возможностей и специальных возможностей**
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
      **От hack (взлома) до продукта, управление глазами для Windows 10**
   :::column-end:::
   :::column:::
      **Специальные возможности в Windows 10**
   :::column-end:::
   :::column:::
      **Введение в создание доступных приложений UWP**
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
      **Специальные возможности Windows 10 расширяют потенциал для всех пользователей**
   :::column-end:::
   :::column:::
      **Улучшение видимости указателей мыши**
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

      Последние новости из мира специальных возможностей Microsoft.
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

      Место, где разработчики и пользователи Windows встречаются и учатся вместе.
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
      [Служба Answer Desk для людей с ограниченными возможностями](https://www.microsoft.com/Accessibility/disability-answer-desk)
   :::column-end:::
:::row-end:::
