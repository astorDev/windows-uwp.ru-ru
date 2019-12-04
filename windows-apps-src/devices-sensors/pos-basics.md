---
title: Основы точки обслуживания
description: Эта статья содержит сведения о начале работы с API-интерфейсами PointOfService для UWP.
ms.date: 12/3/2019
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 4749b66cc5ce2593aead75d65993f70106da7c8b
ms.sourcegitcommit: 2d709ddcc31f52d2a4ace1134aea45057d99a615
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782575"
---
# <a name="point-of-service-basics"></a>Основы точки обслуживания

Этот раздел содержит темы, которые являются общими для всех категорий POS-устройств.

|Статья |Описание |
|------|------------|
| [Объявление возможности](pos-basics-capability.md)      | Узнайте, как добавить возможность **pointOfService** в манифест приложения.  Эта возможность требуется для использования пространства имен Windows.Devices.PointOfService.  |
| [Перечисление устройств](pos-basics-enumerating.md)        | Узнайте, как определить средство выбора устройств, использующееся для запроса устройств, доступных системе, а также, как использовать это средство выбора для перечисления POS-устройств.  |
| [Создание объекта устройства](pos-basics-deviceobject.md)  | Узнайте, как создать объект устройства PointOfService, который предоставит вам доступ к нередактируемым свойствам периферийного устройства и возможность утвердить это устройство для исключительного использования. |
| [Утверждение и включение](pos-basics-claim.md)  | Узнайте, как зарезервировать периферийный PointOfService для монопольного использования и включить операции ввода-вывода.  |
| [Совместное использование периферийных устройств с другими пользователями](pos-basics-sharing.md) | Узнайте, как совместно использовать сеть или подключенные к Bluetooth периферийные устройства с другими компьютерами в среде, где несколько компьютеров используют общие периферийные устройства, а не выделенные периферийные устройства, подключенные к каждому компьютеру.
| [Сквозная PointOfService](pos-get-started.md)  | Это полный пример того, как взаимодействовать с периферийными устройствами PointOfService, используя приведенные выше примеры. |
|

## <a name="see-also"></a>См. также

| Статья   | Описание |
|:--------|:------------|
| [Распространение приложений](../publish/distribute-lob-apps-to-enterprises.md) | Узнайте о вариантах распространения приложения для корпоративных клиентов. |
| [Жизненный цикл приложения](../launch-resume/app-lifecycle.md) | Узнайте о жизненном цикле приложения UWP и о том, что происходит при запуске, приостановке и возобновлении работы приложения Windows. |
| [Ресурсы приложения](../app-resources/index.md) | Узнайте, как создавать, упаковывать и использовать строки, изображения и файловые ресурсы приложения. |
| [Привязка данных](../data-binding/index.md) | Узнайте, как использовать привязку данных для отображения данных в пользовательском интерфейсе приложения. |
| [Перечисление устройств](enumerate-devices.md) | Узнайте, как использовать дополнительные методы перечисления для поиска периферийных устройств.|
| [Адаптивные приложения версии](../debug-test-perf/version-adaptive-apps.md) | Полагается, как спроектировать приложение, чтобы оно выполнялось в нескольких версиях Windows 10.|
|


## <a name="sample-code"></a>Образец кода
+ [Пример сканера штрихкодов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)
+ [Пример кассового ящика]( https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CashDrawer)
+ [Пример вывода строки](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LineDisplay)
+ [Пример считывателя магнитных полос](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MagneticStripeReader)
+ [Пример Поспринтер](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/PosPrinter)
