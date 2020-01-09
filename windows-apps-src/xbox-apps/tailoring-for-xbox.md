---
title: Рекомендации для Xbox
description: Оптимизация приложения для Xbox.
ms.date: 10/12/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1ce69549996a5adfb8c5d2d585753cf95ef3fdc3
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684905"
---
# <a name="xbox-best-practices"></a>Рекомендации для Xbox

По умолчанию все приложения UWP запускаются на Xbox One без дополнительных усилий с вашей стороны. Однако если вы хотите, чтобы ваше приложение нравилось пользователям и соответствовало уровню лучших приложений для Xbox, необходимо следовать приведенным ниже рекомендациям.
  > [!NOTE]
  > Перед началом работы ознакомьтесь с рекомендациями по проектированию, приведенными в статье [Проектирование для Xbox и телевизора](../design/devices/designing-for-tv.md).   

## <a name="to-build-the-best-experiences-for-xbox-one"></a>Обеспечение оптимального взаимодействия с пользователями Xbox One

### <a name="do-turn-off-mouse-mode"></a>*Рекомендуется:* отключение режима мыши

Пользователи Xbox понравятся свои контроллеры. Чтобы оптимизировать входные данные контроллера, [Отключите режим мыши](how-to-disable-mouse-mode.md) и включите навигацию по направлению (также известное как [точечная Навигация по фокусу и взаимодействие](../design/input/gamepad-and-remote-interactions.md#xy-focus-navigation-and-interaction)). Наблюдайте за ловушками фокуса и недоступным пользовательским интерфейсом.

### <a name="do-draw-a-focus-rectangle-that-is-appropriate-for-a-10-foot-experience"></a>*Рекомендуется:* оптимизация прямоугольника фокуса для просмотра на большом расстоянии

Большинство пользователей Xbox находятся перед телевизором на большом расстоянии, поэтому следует учитывать, что стандартный прямоугольник фокуса сложно увидеть с расстояния трех метров. Чтобы убедиться, что элемент пользовательского интерфейса с фокусом ввода всегда виден пользователю, следуйте рекомендациям по [визуальным элементам фокуса](../design/input/gamepad-and-remote-interactions.md#focus-visual). В языке XAML вы получите это поведение бесплатно при запуске приложения на Xbox, а в HTML-приложениях для этого потребуется добавить пользовательский стиль CSS.

### <a name="do-integrate-with-the-systemmediatransportcontrols-class"></a>*Рекомендуется:* интеграция с классом SystemMediaTransportControls

Пользователи Xbox хотят управлять мультимедийными приложениями с помощью пульта дистанционного управления Xbox, Кортаны (особенно с использованием голосовых команд «Воспроизведение» и «Пауза») и Xbox SmartGlass. Чтобы получить эти функции бесплатно, ваше приложение должно использовать класс [SystemMediaTransportControls](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols), который автоматически включается в элементы управления мультимедиа Xbox. Если ваше приложение содержит пользовательские элементы управления мультимедиа, обеспечьте интеграцию с классом **SystemMediaTransportControls** для предоставления пользователям этих возможностей. Если вы создаете приложение для воспроизведения музыки в фоновом режиме, обеспечьте интеграцию с классом **SystemMediaTransportControls**, чтобы элементы управления фоновой музыкой во вкладке многозадачности Xbox работали правильно.

<!-- ### *Do:* Use adaptive UI to account for snapped apps
One of the unique features of Xbox One is that users can snap apps such as Cortana next to any other app, so your app should respond gracefully when it runs in *fill mode*. Implement [adaptive UI](../get-started/universal-application-platform-guide.md#design-adaptive-ui-with-adaptive-panels) and make sure to test your app during development by snapping an app next to it. -->

### <a name="consider-draw-to-the-edge-of-the-screen"></a>*Рассмотрите:* рисование границ экрана

На многих телевизорах содержимое у краев экрана обрезается, поэтому все важное содержимое приложения должно отображаться в [безопасной области телевизора](../design/devices/designing-for-tv.md#tv-safe-area). Платформа UWP использует *нерабочие области* для отображения содержимого в пределах безопасной области телевизора, но при использовании данного поведения по умолчанию вокруг вашего приложения может отображаться видимая граница. Чтобы обеспечить наилучшее взаимодействие, отключите поведение по умолчанию и следуйте инструкциям в разделе [Рисование границ экрана пользовательского интерфейса](turn-off-overscan.md).
> [!IMPORTANT]
  > При отключении нерабочих областей вы несете ответственность за размещение интерактивных элементов и текста в пределах безопасной области телевизора. 

### <a name="consider-use-tv-safe-colors"></a>*Рассмотрите:* применение безопасных цветов для использования на телевизионных экранах

Телевизоры обрабатывают предельно интенсивные цвета не так хорошо, как компьютерные мониторы. Избегайте использования в приложении цветов с высокой интенсивностью, чтобы избежать появления странного эффекта чередования цветов или их размытия. Кроме того, необходимо помнить, что все телевизоры разные, и цвета, которые отлично выглядят на *вашем* телевизоре, могут смотреться совершенно иначе на телевизорах пользователей. Ознакомьтесь с [цветами](../design/devices/designing-for-tv.md#colors) , чтобы понять, как сделать приложение удобным для всех!

### <a name="remember-you-can-disable-scaling"></a>*Помните:* масштабирование можно отключить

Приложения UWP автоматически масштабируются, чтобы элементы пользовательского интерфейса, такие как элементы управления и шрифты оставались разборчивыми на всех устройствах. XAML-приложения масштабируются до 200%, а HTML-приложения — до 150%. Чтобы лучше контролировать отображение приложения на Xbox, отключите коэффициент масштабирования по умолчанию и используйте фактические размеры телевизора высокой четкости в пикселях (1920 x 1080 пикселей). Ознакомьтесь с разделами [Отключение масштабирования](disable-scaling.md) и [Эффективные пиксели и масштабирование](../design/basics/design-and-ui-intro.md#effective-pixels-and-scaling) для получения дополнительных сведений о настройке приложения для оптимального отображения на Xbox.

Из этого видео вы узнаете об этих практиках в работе с приложениями UWP.
</br>
</br>
<iframe src="https://channel9.msdn.com/Blogs/One-Dev-Minute/Tailoring-your-UWP-app-for-Xbox/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="channel-9"></a>Channel 9

Следующие презентации на канале [Channel 9](https://channel9.msdn.com/) являются источником полезной информации о создании великолепных приложений для Xbox.

- [Building Great Universal Windows Platform (UWP) Apps for Xbox](https://channel9.msdn.com/Events/Build/2016/B883) (Создание отличных приложений универсальной платформы Windows (UWP) для Xbox)
- [Adapt Your App for Xbox One and TV](https://channel9.msdn.com/Events/Build/2016/T651-R1) (Адаптация приложения для Xbox One и телевизоров)
- [Разработка UWP 1. Создание адаптивного пользовательского интерфейса](https://channel9.msdn.com/Events/Build/2016/L724-R1)
- [Веб-приложения, выходящие за рамки браузера: кросс-платформенные соответствия требованиям перекрестных устройств](https://channel9.msdn.com/Events/Build/2016/B888)

## <a name="app-dev-on-xbox"></a>Разработка приложений на Xbox

Событие " **Разработка приложений на Xbox** " — отличная отправная точка для разработчиков, занимающихся созданием приложений на Xbox.

* [Просмотр записанных сеансов](https://developer.microsoft.com/windows/projects/campaigns/app-dev-on-xbox-event#WatchNow)
* [Чтение записей блога](https://developer.microsoft.com/windows/projects/campaigns/app-dev-on-xbox-event#BlogSeries)

## <a name="see-also"></a>См. также статью

- [Приложения UWP для Xbox One](index.md)
- [Проектирование для Xbox и телевизора](../design/devices/designing-for-tv.md)
- [Progressive Web Apps for Xbox One](https://docs.microsoft.com/microsoft-edge/progressive-web-apps/xbox-considerations) (Прогрессивные веб-приложения для Xbox One)
