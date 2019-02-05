---
title: Что нового в документации Windows в января 2019 г. — Разработка приложений UWP
description: Новые возможности, видео и руководства для разработчиков добавлены в документацию для января 2019 года разработчиков Windows 10
keywords: новые возможности, обновления, компоненты, руководство разработчика, Windows 10 января
ms.date: 1/17/2019
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: cc5323ba12fa72fb5350e62f74206ea72fe96497
ms.sourcegitcommit: bf600a1fb5f7799961914f638061986d55f6ab12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "9046134"
---
# <a name="whats-new-in-the-windows-developer-docs-in-january-2019"></a>Что нового в документации для разработчиков Windows в января 2019 года

Документация для разработчиков Windows непрерывно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Следующие обзоры функций, руководства для разработчиков и видео, которые стали доступны в январе.

[Установив средства и пакет SDK](https://go.microsoft.com/fwlink/?LinkId=821431) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/create-uwp-apps.md), либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="windows-development-on-microsoft-learn"></a>Разработка для Windows на сведения корпорации Майкрософт

Сведения корпорации Майкрософт предоставляет новые практических обучения и возможности обучения для разработчиков Microsoft. Если вы хотите узнать, как разрабатывать приложения для Windows, ознакомьтесь с [нашей новый путь обучения](https://docs.microsoft.com/learn/paths/develop-windows10-apps/) эффективному сведения о платформе, средства и методика создания вашего первого несколько приложений.

![Изображение обучения разработки приложений для Windows](images/windows-learn.png)

### <a name="direct-3d-12"></a>Direct 3D 12

[Прохода отрисовки Direct3D 12](/windows/desktop/direct3d12/direct3d-12-render-passes) может повысить производительность вашего обработчика, если она основана на основе плитки отложенный отрисовки (TBDR), наряду с другими методами. Этот способ помогает вашего обработчика для повышения эффективности GPU путем включения приложения упростить отрисовки сортировки требования и данных зависимостей ресурсов и что сокращает трафик памяти с отключать микросхемы памяти.

### <a name="msix-modification-packages"></a>Изменение пакетов MSIX

Windows 10 версия 1809 улучшена поддержка для [пакетов MSIX изменения](https://docs.microsoft.com/windows/msix/modification-package-1809-update). Изменения пакетов можно включить реестра подключаемые модули и связанные настройки и позволит приложения, развернутые с помощью MSIX использовать виртуальный реестр и работать надлежащим образом.

![Создание пакета изменения MSIX](images/msix-modification-package.png)

### <a name="open-source-of-wpf-windows-forms-and-winui"></a>Откройте исходный WPF, Windows Forms и WinUI

Платформы WPF, Windows Forms и взаимодействие с Пользователем WinUI теперь доступны для открытым взносы на GitHub. Дополнительные сведения и ссылки см. в разделе [Создание блог приложений Windows](https://blogs.windows.com/buildingapps/2018/12/04/announcing-open-source-of-wpf-windows-forms-and-winui-at-microsoft-connect-2018/#OKZjJs1VVTrMMtkL.97).

### <a name="progressive-web-apps-for-xbox"></a>Прогрессивные веб-приложения для Xbox

[Прогрессивные веб-приложения для Xbox One](https://docs.microsoft.com/microsoft-edge/progressive-web-apps/xbox-considerations)можно расширять веб-приложения и сделать его доступным как Xbox One приложение через Microsoft Store во время по-прежнему по-прежнему использовать существующих платформ, CDN и сервера внутренний сервер. В большинстве случаев пакет вашего приложения PWA для Xbox One так же, как и для Windows, однако существуют некоторые основные различия, которые это руководство поможет выполнить.

### <a name="windows-machine-learning"></a>Windows машинного обучения

Мы реструктуризации [Целевая страница для API -интерфейсы WinML](https://docs.microsoft.com/windows/ai/api-reference), а новые документации для пользовательских оператор WinML и собственных API.

[Обучение модели с PyTorch](https://docs.microsoft.com/windows/ai/train-model-pytorch) содержит рекомендации о том, как обучить модель с помощью платформы PyTorch локально или в облаке. Затем можно загрузить этой модели в ONNX файл и использовать его в приложениях WinML.

![Рисунок WinML](images/winml-graphic.png)

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="choose-your-platform"></a>Выбор платформы

Интересуетесь созданием новых классическое приложение? Изучите наши переработанной страницу [Выбор своей платформы](https://docs.microsoft.com/windows/desktop/choose-your-technology) , подробное описание и сравнения платформы UWP, WPF и Windows Forms и Дополнительные сведения об Win32 API.

### <a name="faqs-on-win32-webview"></a>Часто задаваемые вопросы по Win32 WebView

Наши [вопросы и ответы по](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/webview#frequently-asked-questions-faqs) предоставляет ответы на часто задаваемые вопросы, при использовании Microsoft Edge WebView в классических приложениях, а также ссылки на примеры и дополнительные ресурсы.

### <a name="japanese-era-change"></a>Изменение японский

[Подготовка приложения для японский изменить](../design/globalizing/japanese-era-change.md) показано, как обеспечить Windows приложение будет готово, японский изменяйте набор воспользоваться размещать на 1 мая 2019 г. [Эта страница также доступна на японском языке](https://docs.microsoft.com/ja-jp/windows/uwp/design/globalizing/japanese-era-change).

## <a name="videos"></a>Видеоролики

### <a name="progressive-web-apps"></a>Прогрессивные веб-приложения

Прогрессивные веб-приложения — это веб-сайты, которые работают как собственные приложения в различных браузерах и широкий спектр устройств с Windows 10. [Посмотрите видео,](https://youtu.be/ugAewC3308Y) для получения дополнительных сведений, а затем [Изучите документы](https://aka.ms/Windows-PWA) для начала работы.

### <a name="vs-code-series"></a>Код серии VS

Ознакомьтесь с нашей [новой серии видео в Visual Studio Code](https://www.youtube.com/playlist?list=PLlrxD0HtieHjQX77y-0sWH9IZBTmv1tTx) сведения о том, что VSCode, как его использовать, и как он был создан.

### <a name="one-dev-question"></a>Вопрос

В серии видео вопрос привыкшим разработчики Майкрософт охватывает ряд вопросов о разработке Windows, язык и региональные параметры для совместной работы и журнал. Вот последние вопросы, на которые мы ответили!

Рэймонд Чен:

* [Почему у вас есть Program Files и Program Files (x86)?](https://youtu.be/N7o9eJpFYco)

Ларри Osterman:

* [Почему — это COM таким образом сложным?](https://youtu.be/-gkXAV-StVA )
* [Каково было вашего первого интервью как для Майкрософт?](https://youtu.be/qRb6otsHG5c)
