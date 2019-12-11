---
title: Что нового в документации Windows в январе 2019 г. — разработка приложений UWP
description: В январе 2019 г документация для разработчиков Windows 10 пополнилась описанием новых возможностей, видеоматериалами и руководствами для разработчиков.
keywords: what's new, update, features, developer guidance, Windows 10, january
ms.date: 01/17/2019
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 7947fb6e71a9f2ddbedcd8e3ee8bab7b720dc444
ms.sourcegitcommit: 6169660ea437915265165c4631d9702587e4793d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74902468"
---
# <a name="whats-new-in-the-windows-developer-docs-in-january-2019"></a>Что нового в документации для разработчиков Windows в январе 2019 г.

Документация для разработчиков Windows постоянно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. В январе появились приведенные ниже обзоры функций, руководства для разработчиков и видео.

[Установив средства и пакет SDK](https://developer.microsoft.com/windows/downloads#_blank) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/create-uwp-apps.md) либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="windows-development-on-microsoft-learn"></a>Разработка приложений Windows в Microsoft Learn

Microsoft Learn предоставляет новые практические возможности обучения и подготовки для разработчиков Microsoft. Если вы хотите узнать, как разрабатывать приложения Windows, см. [нашу новую схему обучения](/learn/paths/develop-windows10-apps/) с подробными сведениями о платформе, инструментах и принципах создания приложений.

![Изображение схемы обучения разработки приложений Windows](images/windows-learn.png)

### <a name="direct-3d-12"></a>Direct3D 12

[Этапы визуализации Direct3D 12](/windows/desktop/direct3d12/direct3d-12-render-passes) могут повысить производительность визуализации, если в ней, наряду с другими методами, используется принцип отсроченной отрисовки на основе плиток (TBDR). Этот метод помогает повысить эффективность использования графического процессора для отрисовки, позволяя вашему приложению лучше определять требования к порядку отрисовки ресурсов и зависимости данных и за счет этого сокращать трафик данных, поступающих из памяти в память вне микросхемы и в обратном направлении.

### <a name="msix-modification-packages"></a>Пакеты MSIX с модификациями

Windows 10 версии 1809 отличается улучшенной поддержкой [пакетов MSIX с модификациями](/windows/msix/modification-package-1809-update). Пакеты с модификациями могут содержать подключаемые модули на основе реестра и связанные с ними настройки, что позволяет приложению, развернутому через MSIX, использовать виртуальный реестр и запускаться надлежащим образом.

![Создание пакетов MSIX с модификациями](images/msix-modification-package.png)

### <a name="open-source-of-wpf-windows-forms-and-winui"></a>Открытый исходный код WPF, Windows Forms и WinUI

Платформы взаимодействия с пользователем WPF, Windows Forms и WinUI теперь доступны для публикаций с открытым исходным кодом на GitHub. Дополнительные сведения и ссылки см. в [блоге о сборке приложений Windows](https://blogs.windows.com/buildingapps/2018/12/04/announcing-open-source-of-wpf-windows-forms-and-winui-at-microsoft-connect-2018/#OKZjJs1VVTrMMtkL.97).

### <a name="progressive-web-apps-for-xbox"></a>Прогрессивные веб-приложения для Xbox

С помощью [прогрессивных веб-приложений для Xbox One](/microsoft-edge/progressive-web-apps/xbox-considerations) веб-приложение можно расширить и сделать его доступным как приложение для Xbox One в Microsoft Store, и при этом по-прежнему использовать имеющиеся платформы, CDN и серверную среду. В большинстве случаев пакеты PWA для Xbox One можно создавать таким же образом, как и для Windows, однако есть несколько ключевых отличий, которые будут приведены в этом руководстве.

### <a name="windows-machine-learning"></a>Машинное обучение Windows

Мы реорганизовали [целевую страницу для программных интерфейсов WinML](/windows/ai/api-reference) и добавили новую документацию для пользовательского оператора WinML и собственных программных интерфейсов.

[Обучение модели с помощью PyTorch](/windows/ai/train-model-pytorch) — это руководство по обучению модели с использованием платформы PyTorch как локально, так и в облаке. После обучения вы сможете загрузить эту модель в виде ONNX-файла и использовать ее в своих приложениях WinML.

![Графический объект WinML](images/winml-graphic.png)

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="choose-your-platform"></a>Выбор платформы

Если вы заинтересованы в создании новых классических приложений, ознакомьтесь с нашей обновленной страницей[Выбор платформы для приложения](/windows/desktop/choose-your-technology). Здесь приведены подробные описания и сравнения платформ UWP, WPF и Windows Forms, а также дополнительные сведения о API Win32.

### <a name="faqs-on-win32-webview"></a>Часто задаваемые вопросы о Win32 WebView

Наш раздел [Вопросы и ответы](/windows/communitytoolkit/controls/wpf-winforms/webview#frequently-asked-questions-faqs) содержит ответы на часто задаваемые вопросы, которые возникают при использовании Microsoft Edge WebView в классических приложениях, а также ссылки на образцы кода и дополнительные ресурсы.

### <a name="japanese-era-change"></a>Смена эры в Японии

В статье [Подготовка приложения к смене эры в Японии](../design/globalizing/japanese-era-change.md) показано, как убедиться, что приложение в Windows готово к ряду изменений, связанных со сменой эры в Японии, которая состоится 1 мая 2019 г. [Эта страница доступна также на японском языке](/windows/uwp/design/globalizing/japanese-era-change).

## <a name="videos"></a>Видео

### <a name="progressive-web-apps"></a>Прогрессивные веб-приложения

Прогрессивные веб-приложения — это веб-сайты, которые работают как встроенные приложения в различных браузерах и самых разнообразных устройствах на платформе Windows 10. [Посмотрите видео](https://youtu.be/ugAewC3308Y), чтобы получить дополнительные сведения, а затем [изучите документы](https://developer.microsoft.com/windows/pwa), чтобы начать работу.

### <a name="vs-code-series"></a>Серия обучающих видео по VS Code

Ознакомьтесь с [новой серией обучающих видео по Visual Studio Code](https://www.youtube.com/playlist?list=PLlrxD0HtieHjQX77y-0sWH9IZBTmv1tTx), чтобы узнать о возможностях VSCode, способах его использования и создания.

### <a name="one-dev-question"></a>One Dev Question

В серии видеороликов One Dev Question опытные разработчики Майкрософт рассматривают серию вопросов, касающихся разработки Windows, командной культуры и истории. Вот последние вопросы, на которые мы ответили.

Рэймонд Чен (Raymond Chen):

* [Почему существует Program Files и Program Files (x86)?](https://youtu.be/qRb6otsHG5c)
* [Каким было ваше первое интервью для корпорации Майкрософт?](https://youtu.be/MfzzbNp8kfw)

Ларри Остерман (Larry Osterman):

* [Почему так сложно использовать модель COM?](https://youtu.be/-gkXAV-StVA)
* [Каким было ваше первое интервью для корпорации Майкрософт?](https://youtu.be/N7o9eJpFYco)