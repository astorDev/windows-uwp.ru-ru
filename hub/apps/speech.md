---
title: Речь, голосовая связь и беседа в Windows 10
description: На этой странице содержатся сведения, которые помогут приступить к разработке приложений Windows с поддержкой речи.
ms.topic: article
ms.date: 09/12/2019
keywords: Речь в Windows 10, речь, голосовая связь, беседа, речевые приложения Win32, приложения для распознавания речи, речевые приложения WPF, речевые приложения WinForms
ms.author: kbridge
author: Karl-Bridge-Microsoft
ms.openlocfilehash: 7ac8d782591ce8f3716e491714c4cbf241e80b6c
ms.sourcegitcommit: 8a88a05ad89aa180d41a93152632413694f14ef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726554"
---
# <a name="speech-voice-and-conversation-in-windows-10"></a>Речь, голосовая связь и беседа в Windows 10

![Изображение Hero речи](images/hero-speech-composite-small.png)

Речь может быть эффективным, естественным и приятным способом взаимодействия с приложениями Windows, дополнения или даже замены традиционных интерфейсов взаимодействия на основе мыши, клавиатуры, сенсорного ввода, контроллера или жестов.

Функции на основе речи, такие как распознавание речи, Диктовка, синтез речи (также известные как преобразование текста в речь или TTS), и диалоговые помощники по голосовой связи (например, Кортана или Алекса) могут предоставлять доступ к доступным и инклюзивным пользователям, которые позволяют пользователям использовать ваши приложения могут оказаться недостаточными для других устройств ввода.

На этой странице содержатся сведения о том, как различные платформы разработки Windows обеспечивают распознавание речи, синтез речи и поддержку сообщений для разработчиков, создающих приложения Windows.

## <a name="platform-specific-documentation"></a>Документация по определенным платформам

:::row:::
   :::column:::
      ![Универсальная платформа Windows (UWP)](images/platform-uwp.png)

      **Универсальная платформа Windows (UWP)**

      Создавайте приложения с поддержкой речи на современной платформе для приложений и игр Windows 10, на любом устройстве Windows (включая ПК, телефоны, Xbox One, HoloLens и др.) и публикуйте их в Microsoft Store.

      [Взаимодействия с помощью речи](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)

      [Распознавание речи](https://docs.microsoft.com/windows/uwp/design/input/speech-recognition)

      [Непрерывная диктовка](https://docs.microsoft.com/windows/uwp/design/input/enable-continuous-dictation)

      [Синтез речи](https://docs.microsoft.com/uwp/api/windows.media.speechsynthesis)

      [Агенты взаимодействия](https://docs.microsoft.com/uwp/api/windows.applicationmodel.conversationalagent)

      [Voice Commands кортаны](https://docs.microsoft.com/cortana/voice-commands/vcd)
   :::column-end:::
   :::column:::
      ![Приложения платформы Win32](images/platform-win32.png)

      **Платформа Win32**

      Разрабатывайте приложения с поддержкой речи для настольных систем и Windows Server с помощью средств, сведений и примеров модулей и приложений, предоставляемых здесь.

      [Платформа Microsoft Speech Platform — пакет средств разработки программного обеспечения (SDK) (версия 11)](https://www.microsoft.com/download/details.aspx?id=27226)
      
      [Пакет SDK для Microsoft Speech, версия 5.1](https://www.microsoft.com/download/details.aspx?id=10121)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      ![.NET](images/platform-dotnet.png)

      **.NET Framework**

      Разрабатывайте приложения и средства со специальными возможностями на установленной платформе для управляемых приложений Windows с помощью модели пользовательского интерфейса XAML и .NET Framework.

      [Руководство по программированию System.Speech для .NET Framework](https://docs.microsoft.com/previous-versions/office/developer/speech-technologies/hh361625(v=office.14))
   :::column-end:::
   :::column:::
      ![Службы распознавания речи Azure](images/platform-azure-speech.png)

      **Службы распознавания речи Azure**

      Разрабатывайте, создавайте и тестируйте доступные веб-сайты с помощью служб распознавания речи Azure.

      [Преобразование речи в текст](https://azure.microsoft.com/services/cognitive-services/speech-to-text/)

      [Преобразование текста в речь](https://azure.microsoft.com/services/cognitive-services/text-to-speech/)
      
      [Перевод речи](https://azure.microsoft.com/services/cognitive-services/speech-translation/)

      [Первые виртуальные помощники](https://docs.microsoft.com/azure/cognitive-services/speech-service/voice-first-virtual-assistants)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      **Устаревшие функции**

      Устаревшие, устаревшие и (или) неподдерживаемые версии технологий Microsoft Speech.
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [Microsoft Agent](https://docs.microsoft.com/windows/win32/lwef/microsoft-agent)

      [Пакет средств разработки программного обеспечения для приложений Microsoft Speech (САСДК) версии 1,0](https://www.microsoft.com/download/details.aspx?id=2200)
   :::column-end:::
   :::column:::
      [Microsoft Speech API (SAPI) 5,3](https://docs.microsoft.com/previous-versions/windows/desktop/ms723627(v=vs.85))

      [Microsoft Speech API (SAPI) 5,4](https://docs.microsoft.com/previous-versions/windows/desktop/ee125663(v=vs.85))

      [Элемент управления Распознавание речи Bingного распознавания](https://docs.microsoft.com/previous-versions/bing/speech/dn434583(v%3dmsdn.10))
   :::column-end:::
:::row-end:::

## <a name="samples"></a>Примеры

Загрузите и запустите полные примеры Windows, демонстрирующие различные компоненты и функции специальных возможностей.

:::row:::
   :::column:::
      [Браузер с примером кода](https://docs.microsoft.com/samples/browse/?term=speech)

      Новый браузер образцов (заменяет коллекцию кода MSDN).
   :::column-end:::
   :::column:::
      [Классические примеры Windows на GitHub](https://github.com/microsoft/Windows-classic-samples/search?q=speech&unscoped_q=speech)

      В этих примерах демонстрируются функциональные возможности и модель программирования для Windows и Windows Server. 
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [Примеры универсальной платформы Windows (UWP) на GitHub](https://github.com/microsoft/Windows-universal-samples/search?q=speech&unscoped_q=speech)

      В этих примерах демонстрируются шаблоны использования API для универсальной платформы Windows (UWP) в пакете средств разработки программного обеспечения (SDK) для Windows 10.
   :::column-end:::
   :::column:::
      [XAML Controls Gallery](https://github.com/microsoft/Xaml-Controls-Gallery)

      В этом приложении демонстрируются различные элементы управления Xaml, поддерживаемые в системе Fluent Design.
   :::column-end:::
:::row-end:::

## <a name="videos"></a>Видео

Различные видеоматериалы, посвященные созданию приложений Windows, которые включают речевое взаимодействие.

:::row:::
   :::column:::
      **Подробнее о Кортане и платформе обработки речи**
   :::column-end:::
   :::column:::
      **Расширяемость кортаны в универсальных приложениях Windows**
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      > [!VIDEO https://channel9.msdn.com/Events/Build/2015/3-716/player]
   :::column-end:::
   :::column:::
      > [!VIDEO https://channel9.msdn.com/Events/Build/2015/2-691/player]
   :::column-end:::
:::row-end:::

## <a name="other-resources"></a>Другие ресурсы

:::row:::
   :::column:::
      **Блоги и новости**

      Новейшее из мира Microsoft Speech.
   :::column-end:::
   :::column:::
      **Сообщество и поддержка**

      Где разработчики и пользователи Windows могут встретиться и изучать вместе.
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [В новостях](https://news.microsoft.com/?s=speech)

      [Блоги для распознавания речи](https://blogs.windows.com/windowsdeveloper/?s=speech)
   :::column-end:::
   :::column:::
      [Сообщество Windows — речь](https://community.windows.com/search?q=speech)

      [Форум разработчика речи Windows](https://social.msdn.microsoft.com/Forums/home?filter=alltypes&sort=firstpostdesc&searchTerm=speech)
   :::column-end:::
:::row-end:::
