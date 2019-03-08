---
title: Рекомендации для Xbox
description: Оптимизация приложения для Xbox.
ms.date: 10/12/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e273b1b3bb84929005cfbe4a205397fa298ea1c8
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57657129"
---
# <a name="xbox-best-practices"></a>Рекомендации для Xbox

По умолчанию все приложения UWP запускаются на Xbox One без дополнительных усилий с вашей стороны. Однако если вы хотите, чтобы ваше приложение нравилось пользователям и соответствовало уровню лучших приложений для Xbox, необходимо следовать приведенным ниже рекомендациям.
  > [!NOTE]
  > Перед началом работы ознакомьтесь с рекомендациями по проектированию, приведенными в статье [Проектирование для Xbox и телевизора](../design/devices/designing-for-tv.md).   

## <a name="to-build-the-best-experiences-for-xbox-one"></a>Обеспечение оптимального взаимодействия с пользователями Xbox One

### <a name="do-turn-off-mouse-mode"></a>*Выполните действия.* Отключить режим мыши

С ними контроллеров любимых пользователями Xbox. Для оптимизации для ввода контроллера [отключить режим мыши](how-to-disable-mouse-mode.md) и включить направленное перемещение между элементами (также называется [XY сосредоточиться навигации и взаимодействие](../design/input/gamepad-and-remote-interactions.md#xy-focus-navigation-and-interaction)). Остерегайтесь ловушек фокус и пользовательский Интерфейс недоступен.

### <a name="do-draw-a-focus-rectangle-that-is-appropriate-for-a-10-foot-experience"></a>*Выполните действия.* Нарисовать прямоугольник фокуса, подходящий для работы 10 foot

Большинство пользователей Xbox находятся перед телевизором на большом расстоянии, поэтому следует учитывать, что стандартный прямоугольник фокуса сложно увидеть с расстояния трех метров. Чтобы убедиться, что элемент пользовательского интерфейса с фокусом ввода всегда виден пользователю, следуйте рекомендациям по [визуальным элементам фокуса](../design/input/gamepad-and-remote-interactions.md#focus-visual). В языке XAML вы получите это поведение бесплатно при запуске приложения на Xbox, а в HTML-приложениях для этого потребуется добавить пользовательский стиль CSS.

### <a name="do-integrate-with-the-systemmediatransportcontrols-class"></a>*Выполните действия.* Интеграция с классом SystemMediaTransportControls

Пользователи Xbox хотят управлять мультимедийными приложениями с помощью пульта дистанционного управления Xbox, Кортаны (особенно с использованием голосовых команд «Воспроизведение» и «Пауза») и Xbox SmartGlass. Чтобы получить эти функции бесплатно, ваше приложение должно использовать класс [SystemMediaTransportControls](https://msdn.microsoft.com/library/windows/apps/windows.media.systemmediatransportcontrols.aspx), который автоматически включается в элементы управления мультимедиа Xbox. Если ваше приложение содержит пользовательские элементы управления мультимедиа, обеспечьте интеграцию с классом **SystemMediaTransportControls** для предоставления пользователям этих возможностей. Если вы создаете приложение для воспроизведения музыки в фоновом режиме, обеспечьте интеграцию с классом **SystemMediaTransportControls**, чтобы элементы управления фоновой музыкой во вкладке многозадачности Xbox работали правильно.

<!-- ### *Do:* Use adaptive UI to account for snapped apps
One of the unique features of Xbox One is that users can snap apps such as Cortana next to any other app, so your app should respond gracefully when it runs in *fill mode*. Implement [adaptive UI](../get-started/universal-application-platform-guide.md#design-adaptive-ui-with-adaptive-panels) and make sure to test your app during development by snapping an app next to it. -->

### <a name="consider-draw-to-the-edge-of-the-screen"></a>*Примите во внимание.* Нарисовать относительно края экрана

На многих телевизорах содержимое у краев экрана обрезается, поэтому все важное содержимое приложения должно отображаться в [безопасной области телевизора](../design/devices/designing-for-tv.md#tv-safe-area). Платформа UWP использует *нерабочие области* для отображения содержимого в пределах безопасной области телевизора, но при использовании данного поведения по умолчанию вокруг вашего приложения может отображаться видимая граница. Чтобы обеспечить наилучшее взаимодействие, отключите поведение по умолчанию и следуйте инструкциям в разделе [Рисование границ экрана пользовательского интерфейса](turn-off-overscan.md).
> [!IMPORTANT]
  > При отключении нерабочих областей вы несете ответственность за размещение интерактивных элементов и текста в пределах безопасной области телевизора. 

### <a name="consider-use-tv-safe-colors"></a>*Примите во внимание.* Использование ТВ-цвета

Телевизоры обрабатывают предельно интенсивные цвета не так хорошо, как компьютерные мониторы. Избегайте использования в приложении цветов с высокой интенсивностью, чтобы избежать появления странного эффекта чередования цветов или их размытия. Кроме того, необходимо помнить, что все телевизоры разные, и цвета, которые отлично выглядят на *вашем* телевизоре, могут смотреться совершенно иначе на телевизорах пользователей. Чтение [цвета](../design/devices/designing-for-tv.md#colors) понять, как сделать приложение всем отлично выглядело!

### <a name="remember-you-can-disable-scaling"></a>*Помните:* Вы можете отключить масштабирование

Приложения UWP автоматически масштабируются, чтобы элементы пользовательского интерфейса, такие как элементы управления и шрифты оставались разборчивыми на всех устройствах. XAML-приложения масштабируются до 200%, а HTML-приложения — до 150%. Чтобы лучше контролировать отображение приложения на Xbox, отключите коэффициент масштабирования по умолчанию и используйте фактические размеры телевизора высокой четкости в пикселях (1920 x 1080 пикселей). Ознакомьтесь с разделами [Отключение масштабирования](disable-scaling.md) и [Эффективные пиксели и масштабирование](../design/basics/design-and-ui-intro.md#effective-pixels-and-scaling) для получения дополнительных сведений о настройке приложения для оптимального отображения на Xbox.

Из этого видео вы узнаете об этих практиках в работе с приложениями UWP.
</br>
</br>
<iframe src="https://channel9.msdn.com/Blogs/One-Dev-Minute/Tailoring-your-UWP-app-for-Xbox/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="channel-9"></a>Channel 9

Следующие презентации на канале [Channel 9](https://channel9.msdn.com/) являются источником полезной информации о создании великолепных приложений для Xbox.

- [Разработка отличных универсальной Windows платформы (UWP) приложений для Xbox](https://channel9.msdn.com/Events/Build/2016/B883)
- [Адаптировать приложения для Xbox One и ТВ](https://channel9.msdn.com/Events/Build/2016/T651-R1)
- [Разработка UWP 1: Создание адаптивного пользовательского интерфейса](https://channel9.msdn.com/Events/Build/2016/L724-R1)
- [Веб-приложений за его пределами браузера: Кросс платформенных соответствует Cross устройства](https://channel9.msdn.com/Events/Build/2016/B888)

## <a name="app-dev-on-xbox"></a>Разработка приложения на Xbox

**Разработки приложения на Xbox** событие является отличной отправной точкой для разработчиков — новичков в создании приложений на Xbox.

* [Записанные сеансы](https://developer.microsoft.com/windows/projects/campaigns/app-dev-on-xbox-event#WatchNow)
* [Чтение в записях блога:](https://developer.microsoft.com/windows/projects/campaigns/app-dev-on-xbox-event#BlogSeries)

## <a name="see-also"></a>См. также

- [Приложения UWP для Xbox One](index.md)
- [Проектирование для Xbox и телевизора](../design/devices/designing-for-tv.md)
- [Прогрессивное веб-приложений для Xbox One](https://docs.microsoft.com/en-us/microsoft-edge/progressive-web-apps/xbox-considerations)
