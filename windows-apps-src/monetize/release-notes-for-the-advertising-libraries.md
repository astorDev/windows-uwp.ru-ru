---
author: mcleanbyron
ms.assetid: ca92bed1-ad9e-4947-ad91-87d12de727c0
description: "Изучите заметки о выпуске библиотек Microsoft Advertising в пакете SDK Microsoft Store Engagement and Monetization."
title: "Заметки о выпуске библиотек Microsoft Advertising"
translationtype: Human Translation
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: 8e2114e969b27d579f62195f026cfcfd9672a94a

---

# Заметки о выпуске библиотек Microsoft Advertising


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Этот раздел содержит заметки о текущем выпуске библиотек Microsoft Advertising в пакете SDK Microsoft Store Engagement and Monetization. Библиотеки поддерживают приложения XAML и JavaScript/HTML для Windows 10, Windows 8.1, Windows Phone 8.1 и Windows Phone 8.

## Установка


Библиотеки Microsoft Advertising доступны в составе пакета [SDK Microsoft Store Engagement and Monetization](http://aka.ms/store-em-sdk). Для всех других типов проектов, отличных от Windows Phone 8.x Silverlight, сборки Microsoft Advertising, которые распространялись в более ранних отдельных выпусках SDK Microsoft Universal Ad Client и Microsoft Advertising, теперь устанавливаются вместе с SDK Microsoft Store Engagement and Monetization. Дополнительные сведения об установке SDK и библиотек в его составе см. в разделе [Установка библиотек Microsoft Advertising](install-the-microsoft-advertising-libraries.md).

Чтобы получить сборки Microsoft Advertising для проектов Silverlight в Windows Phone 8.x, установите пакет [SDK Microsoft Store Engagement and Monetization](http://aka.ms/store-em-sdk), откройте проект в Visual Studio и последовательно щелкните **Проект** > **Добавить подключенную службу** > **Рекламный посредник**, чтобы автоматически загрузить сборки. После этого можно удалить ссылки на рекламный посредник из вашего проекта, если в дальнейшем вы не планируете использовать рекламное посредничество. Дополнительные сведения см. в разделе [AdControl в Windows Phone Silverlight](adcontrol-in-windows-phone-silverlight.md).

## Различие между библиотеками Microsoft Advertising и рекламным посредником

Несмотря на то что оба инструмента предоставляются SDK Microsoft Store Engagement and Monetization, они служат разным целям. Используйте классы [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) и [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) из библиотек Microsoft Advertising, если требуется показать баннерную или промежуточную видеорекламу от Майкрософт в приложении XAML или JavaScript. Используйте класс **AdMediatorControl** в библиотеках рекламного посредника, если требуется отображать в приложении XAML рекламные баннеры из нескольких рекламных сетей (рекламный посредник не поддерживается приложениями JavaScript/HTML). Дополнительные сведения см. в разделе [Отличия класса AdMediatorControl от класса AdControl](what-is-the-difference-admediatorcontrol-or-adcontrol.md).

## Удаление предыдущих версий

Перед установкой SDK Microsoft Store Engagement and Monetization настоятельно рекомендуется удалить все предыдущие экземпляры SDK Microsoft Universal Ad Client и Microsoft Advertising.

## Сборки для определенных архитектур

При использовании библиотек Microsoft Libraries невозможно ориентироваться в проекте на **Любой ЦП**. Если проект ориентирован на платформу **Любой ЦП**, может отобразиться предупреждение после добавления ссылки в библиотеки Microsoft Advertising. Чтобы убрать это предупреждение, обновите проект, чтобы использовать сборку определенной архитектуры (например, **x86**). Подробнее см. в статье [Известные проблемы](known-issues-for-the-advertising-libraries.md).

## Поддержка C++

Библиотеки Microsoft Advertising (которые содержат классы **AdControl** и **InterstitialAd**) поддерживают приложения на C++ и DirectX благодаря взаимодействию с компонентами среды выполнения Windows (*interop*). Дополнительные сведения и примеры программирования с использованием XAML b C++ см. в разделе [Система типов](https://msdn.microsoft.com/library/windows/apps/xaml/hh755822.aspx).

## Отсутствие элемента управления на панели инструментов

В текущем выпуске библиотек Microsoft Advertising в пакете SDK Microsoft Store Engagement and Monetization отсутствует элемент управления для перетаскивания классов **AdControl** или **InterstitialAd** на поверхность проектирования приложения. Инструкции о добавлении этих элементов управления в разметку и код см. в разделе [Пошаговые руководства для разработчиков](developer-walkthroughs.md).

## Свойства широты и долготы более недоступны

Класс **AdControl** более не имеет свойств **Latitude** и **Longitude** для приложений UWP. Вместо этого код, встроенный в элемент управления рекламным объявлением, обнаруживает и отправляет эти значения на серверы рекламы от имени приложения.

## Важное примечание

Обязательно прочитайте лицензионное соглашение с конечным пользователем (EULA) полностью. См. раздел [Важное примечание — EULA](important-notice-eula.md).

 

 



<!--HONumber=Jun16_HO4-->


