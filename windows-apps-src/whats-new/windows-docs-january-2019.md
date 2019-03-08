---
title: Новые возможности в документации Windows в января 2019 — разработка приложений универсальной платформы Windows
description: В документации разработчика Windows 10 января 2019 были добавлены новые функции, видео и материалы для разработчиков
keywords: новые возможности, обновления, функции, материалы для разработчиков, Windows 10 января
ms.date: 01/17/2019
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: beb80c28866b8f8207f203b70cb504dcd034098d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57636579"
---
# <a name="whats-new-in-the-windows-developer-docs-in-january-2019"></a>Новые возможности в документации по Windows Developer в января 2019

Документация для разработчиков Windows непрерывно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Обзор компонентов, материалы для разработчиков и видеоматериалы были выпущены в январе.

[Установив средства и пакет SDK](https://go.microsoft.com/fwlink/?LinkId=821431) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/create-uwp-apps.md) либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="windows-development-on-microsoft-learn"></a>Для разработки приложений Windows в Майкрософт сведения

Узнайте Microsoft предоставляет новый Практическое обучение и возможности обучения для разработчиков Microsoft. Если вы хотите узнать, как разрабатывать приложения Windows, см. статью [наш новый путь обучения](https://docs.microsoft.com/learn/paths/develop-windows10-apps/) для подробным введением платформы, инструменты и как написать свое первое приложение на несколько.

![Снимок экрана: разработки Windows, учебные материалы](images/windows-learn.png)

### <a name="direct-3d-12"></a>Direct 3D 12

[Direct3D 12 отображения](/windows/desktop/direct3d12/direct3d-12-render-passes) может повысить производительность вашего модуля подготовки отчетов, если она основана на плитке отложенный подготовки отчетов на основе (TBDR), наряду с другими методами. Метод помогает вашего модуля подготовки отчетов для повышения эффективности GPU приложения упростить отрисовки упорядочивания требования и данных зависимостей ресурсов, и таким образом снижая трафик памяти из памяти вне кристалла.

### <a name="msix-modification-packages"></a>MSIX изменения пакетов

Windows 10 версии 1809 Улучшенная поддержка [MSIX изменения пакетов](https://docs.microsoft.com/windows/msix/modification-package-1809-update). Изменение пакетов могут включать системного реестра подключаемые модули и связанные настройки, а также позволят приложение, развернутое через MSIX виртуальный реестр и выполняться правильно.

![При создании пакета MSIX изменения](images/msix-modification-package.png)

### <a name="open-source-of-wpf-windows-forms-and-winui"></a>Открытый код WPF, Windows Forms и WinUI

Платформы WPF, Windows Forms и WinUI UX теперь доступны для публикаций с открытым исходным кодом на сайте GitHub. Дополнительные сведения и ссылки, см. в разделе [построение блоге о приложениях Windows](https://blogs.windows.com/buildingapps/2018/12/04/announcing-open-source-of-wpf-windows-forms-and-winui-at-microsoft-connect-2018/#OKZjJs1VVTrMMtkL.97).

### <a name="progressive-web-apps-for-xbox"></a>Прогрессивное веб-приложений для Xbox

С помощью [прогрессивное веб-приложений для Xbox One](https://docs.microsoft.com/microsoft-edge/progressive-web-apps/xbox-considerations), можно расширить веб-приложение и сделать его доступным как приложение Xbox One через Microsoft Store продолжают по-прежнему использовать свои имеющиеся платформы, CDN и сервера базы данных. В большинстве случаев можно упаковать в PWA для Xbox One таким же образом, что для Windows, однако существуют некоторые ключевые отличия, которые это руководство поможет вам выполнить.

### <a name="windows-machine-learning"></a>Windows машинного обучения

Мы реструктуризации [целевая страница для API-интерфейсов WinML](https://docs.microsoft.com/windows/ai/api-reference)и добавить новую документацию для пользовательского оператора WinML и собственные API-интерфейсы.

[Обучение модели с PyTorch](https://docs.microsoft.com/windows/ai/train-model-pytorch) содержит рекомендации о том, как обучить модель с помощью платформы PyTorch, локально или в облаке. Затем можно загрузить эту модель в виде файла ONNX и использовать его в приложениях WinML.

![График WinML](images/winml-graphic.png)

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="choose-your-platform"></a>Выберите платформу

Вы заинтересованы в создании новых настольных приложений? Ознакомьтесь с нашей переработанной [выберите платформу](https://docs.microsoft.com/windows/desktop/choose-your-technology) страницы подробное описание и сравнение платформы UWP, WPF и Windows Forms и Дополнительные сведения об интерфейсе API Win32.

### <a name="faqs-on-win32-webview"></a>Часто задаваемые вопросы по веб-представления Win32

Наши [ответы на вопросы о](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/webview#frequently-asked-questions-faqs) содержатся ответы на часто задаваемые вопросы, при использовании веб-представления Microsoft Edge в настольных приложениях, а также ссылки на образцы кода и дополнительные ресурсы.

### <a name="japanese-era-change"></a>Изменение японской эры

[Подготовка приложения для изменения японской эры](../design/globalizing/japanese-era-change.md) показано, как обеспечить в Windows, приложение будет готово, для японской эры измените набор, чтобы воспользоваться разместить на 1 мая 2019 г. [Эта страница доступна также на японском языке](https://docs.microsoft.com/ja-jp/windows/uwp/design/globalizing/japanese-era-change).

## <a name="videos"></a>Видео

### <a name="progressive-web-apps"></a>Прогрессивные веб-приложения

Прогрессивное веб-приложений, веб-сайтов, которые работают как собственные приложения для различных браузеров и самых разнообразных устройствах с Windows 10. [Просмотрите видео](https://youtu.be/ugAewC3308Y) для получения дополнительных сведений, а затем [ознакомьтесь с документацией](https://aka.ms/Windows-PWA) Чтобы приступить к работе.

### <a name="vs-code-series"></a>Серия кода VS

Ознакомьтесь с нашей [новой серии видеороликов по Visual Studio Code](https://www.youtube.com/playlist?list=PLlrxD0HtieHjQX77y-0sWH9IZBTmv1tTx) сведения о возможностях VSCode, способы его использования, и как он был создан.

### <a name="one-dev-question"></a>Один вопрос разработки

В серии видео один вопрос разработки привыкшим разработчиков Microsoft охватывают ряд вопросов о разработке Windows, команды языка и региональных параметров и журнал. Вот последние вопросы, которые мы выбрали ответ!

Рэймонд Чен:

* [Почему у, Program Files и Program Files (x86)?](https://youtu.be/N7o9eJpFYco)

Блоге Ларри Остермана:

* [Почему не COM таким уж сложным?](https://youtu.be/-gkXAV-StVA )
* [Какова первое интервью как корпорации Майкрософт?](https://youtu.be/qRb6otsHG5c)
