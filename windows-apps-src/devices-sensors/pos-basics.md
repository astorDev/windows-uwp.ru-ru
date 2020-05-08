---
title: Основы точки обслуживания
description: Эта статья содержит сведения о начале работы с API-интерфейсами среда выполнения Windows PointOfService.
ms.date: 12/3/2019
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: cb8acf5f7dce8e81d72850a4dbd097083b240864
ms.sourcegitcommit: ef723e3d6b1b67213c78da696838a920c66d5d30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2020
ms.locfileid: "82730047"
---
# <a name="point-of-service-basics"></a>Основы точки обслуживания

Этот раздел содержит темы, которые являются общими для всех категорий POS-устройств.

|Раздел |Описание |
|------|------------|
| [Объявление возможностей](pos-basics-capability.md)      | Узнайте, как добавить возможность **pointOfService** в манифест приложения.  Эта возможность требуется для использования пространства имен Windows.Devices.PointOfService.  |
| [Перечисление устройств](pos-basics-enumerating.md)        | Узнайте, как определить средство выбора устройств, использующееся для запроса устройств, доступных системе, а также, как использовать это средство выбора для перечисления POS-устройств.  |
| [Создание объекта устройства](pos-basics-deviceobject.md)  | Узнайте, как создать объект устройства PointOfService, который предоставит вам доступ к нередактируемым свойствам периферийного устройства и возможность утвердить это устройство для исключительного использования. |
| [Утверждение и включение](pos-basics-claim.md)  | Узнайте, как зарезервировать периферийный PointOfService для монопольного использования и включить операции ввода-вывода.  |
| [Общий доступ к периферийным устройствам для других пользователей](pos-basics-sharing.md) | Узнайте, как совместно использовать сеть или подключенные к Bluetooth периферийные устройства с другими компьютерами в среде, где несколько компьютеров используют общие периферийные устройства, а не выделенные периферийные устройства, подключенные к каждому компьютеру.
| [Сквозная PointOfService](pos-get-started.md)  | Это полный пример того, как взаимодействовать с периферийными устройствами PointOfService, используя приведенные выше примеры. |
|

## <a name="see-also"></a>См. также

| Раздел   | Описание |
|:--------|:------------|
| [Распространение приложений](../publish/distribute-lob-apps-to-enterprises.md) | Узнайте о вариантах распространения приложения для корпоративных клиентов. |
| [Жизненный цикл приложения](../launch-resume/app-lifecycle.md) | Узнайте о жизненном цикле приложения UWP и о том, что происходит при запуске, приостановке и возобновлении работы приложения Windows. |
| [Ресурсы приложений](../app-resources/index.md) | Узнайте, как создавать, упаковывать и использовать строки, изображения и файловые ресурсы приложения. |
| [Привязка данных](../data-binding/index.md) | Узнайте, как использовать привязку данных для отображения данных в пользовательском интерфейсе приложения. |
| [Перечисление устройств](enumerate-devices.md) | Узнайте, как использовать дополнительные методы перечисления для поиска периферийных устройств.|
| [Адаптивные приложения версии](../debug-test-perf/version-adaptive-apps.md) | Полагается, как спроектировать приложение, чтобы оно выполнялось в нескольких версиях Windows 10.|
|


## <a name="sample-code"></a>Образец кода
+ [Пример сканера штрихкодов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)
+ [Образец кассового аппарата]( https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CashDrawer)
+ [Пример строкового дисплея](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LineDisplay)
+ [Пример считывателя магнитных карт](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MagneticStripeReader)
+ [Пример POSPrinter](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/PosPrinter)
