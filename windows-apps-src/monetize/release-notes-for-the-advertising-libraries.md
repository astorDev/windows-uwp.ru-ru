---
author: mcleanbyron
ms.assetid: ca92bed1-ad9e-4947-ad91-87d12de727c0
description: "Изучите заметки о выпуске библиотек Microsoft Advertising в пакете Microsoft Store Services SDK."
title: "Заметки о выпуске библиотек Microsoft Advertising"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, рекламные объявления, реклама, заметки о выпуске"
ms.openlocfilehash: f3d07df6e64c96e9070cb82bd7ac6e96b9cad1ee
ms.sourcegitcommit: d053f28b127e39bf2aee616aa52bb5612194dc53
translationtype: HT
---
# <a name="release-notes-for-the-advertising-libraries"></a>Заметки о выпуске библиотек Microsoft Advertising




Этот раздел содержит заметки о текущем выпуске библиотек Microsoft Advertising в Microsoft Store Services SDK (для приложений UWP) и Microsoft Advertising SDK для Windows и Windows Phone 8.x (для приложений Windows 8.1 и Windows Phone 8.x). Библиотеки поддерживают приложения XAML и JavaScript/HTML для Windows 10, Windows 8.1, Windows Phone 8.1 и Windows Phone 8.

## <a name="installation"></a>Установка


Библиотеки Microsoft Advertising доступны в составе пакета [Microsoft Store Services SDK](http://aka.ms/store-em-sdk) (для приложений UWP) и [Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk) (для приложений Windows 8.1 и Windows Phone 8.x). Дополнительные сведения об установке SDKs и библиотек в его составе см. в разделе [Установка библиотек Microsoft Advertising](install-the-microsoft-advertising-libraries.md).

Чтобы получить сборки Microsoft Advertising для проектов Silverlight в Windows Phone 8.x, установите пакет [Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk), откройте проект в Visual Studio и последовательно щелкните **Проект** > **Добавить подключенную службу** > **Рекламный посредник**, чтобы автоматически загрузить сборки. После этого можно удалить ссылки на рекламный посредник из вашего проекта, если в дальнейшем вы не планируете использовать рекламное посредничество. Дополнительные сведения см. в разделе [AdControl в Windows Phone Silverlight](adcontrol-in-windows-phone-silverlight.md).


## <a name="uninstall-previous-versions"></a>Удаление предыдущих версий

Перед установкой пакета Microsoft Store Services SDK (для приложений UWP) или Microsoft Advertising SDK для Windows и Windows Phone 8.x (для приложений Windows 8.1 и Windows Phone 8.x) настоятельно рекомендуется удалить все предыдущие экземпляры Microsoft Universal Ad Client SDK или Microsoft Advertising SDK.

## <a name="target-architecture-specific-build-outputs"></a>Сборки для определенных архитектур

При использовании библиотек Microsoft Libraries невозможно ориентироваться в проекте на **Любой ЦП**. Если проект ориентирован на платформу **Любой ЦП**, может отобразиться предупреждение после добавления ссылки в библиотеки Microsoft Advertising. Чтобы убрать это предупреждение, обновите проект, чтобы использовать сборку определенной архитектуры (например, **x86**). Подробнее см. в статье [Известные проблемы](known-issues-for-the-advertising-libraries.md).

## <a name="c-support"></a>Поддержка C++

Библиотеки Microsoft Advertising (которые содержат классы **AdControl** и **InterstitialAd**) поддерживают приложения на C++ и DirectX благодаря взаимодействию с компонентами среды выполнения Windows (*interop*). Дополнительные сведения и примеры программирования с использованием XAML b C++ см. в разделе [Система типов](https://msdn.microsoft.com/library/windows/apps/xaml/hh755822.aspx).

## <a name="no-toolbox-control"></a>Отсутствие элемента управления на панели инструментов

В текущем выпуске библиотек Microsoft Advertising в Microsoft Store Services SDK или Microsoft Advertising SDK для Windows и Windows Phone 8.x на панели инструментов отсутствует элемент управления для перетаскивания **AdControl** или **InterstitialAd** в рабочую область конструирования в вашем приложении. Инструкции о добавлении этих элементов управления в разметку и код см. в разделе [Пошаговые руководства для разработчиков](developer-walkthroughs.md).

## <a name="latitude-and-longitude-properties-no-longer-available"></a>Свойства широты и долготы более недоступны

Класс **AdControl** более не имеет свойств **Latitude** и **Longitude** для приложений UWP. Вместо этого код, встроенный в элемент управления рекламным объявлением, обнаруживает и отправляет эти значения на серверы рекламы от имени приложения.


 

 
