---
title: Начало работы с POS-устройствами
description: Эта статья содержит сведения о начале работы с API-интерфейсами PointOfService для UWP.
ms.date: 06/13/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: f1e58dbf8bae22df0652ada6ff8aafff6d30e8aa
ms.sourcegitcommit: fca0132794ec187e90b2ebdad862f22d9f6c0db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63817400"
---
# <a name="getting-started-with-point-of-service"></a>Начало работы с POS-устройствами

## <a name="pointofservice-basics"></a>Основы PointOfService

Этот раздел содержит темы, которые являются общими для всех категорий POS-устройств.

|Раздел |Описание |
|------|------------|
| [Объявление функции](pos-basics-capability.md)      | Узнайте, как добавить возможность **pointOfService** в манифест приложения.  Эта возможность требуется для использования пространства имен Windows.Devices.PointOfService.  |
| [Перечисление устройств](pos-basics-enumerating.md)        | Узнайте, как определить средство выбора устройств, использующееся для запроса устройств, доступных системе, а также, как использовать это средство выбора для перечисления POS-устройств.  |
| [Создание объекта устройства](pos-basics-deviceobject.md)  | Узнайте, как создать объект устройства PointOfService, который предоставит вам доступ к нередактируемым свойствам периферийного устройства и возможность утвердить это устройство для исключительного использования. |
| [Утверждение и включить ](pos-basics-claim.md)  | Узнайте, как зарезервировать PointOfService, периферийных для монопольного использования и включить для операций ввода-вывода.  |
| [Совместно с другими пользователями периферийные устройства](pos-basics-sharing.md) | Узнайте, как доступ сети или периферийные устройства Bluetooth подключен с другими компьютерами в среде, где нескольких ПК зависят от периферийных устройств, а не выделенные периферийные устройства, подключенные к каждому компьютеру.
| [PointOfService end-to-end](pos-get-started.md)  | Это пример сквозного способы взаимодействия с периферийные устройства PointOfService, используя приведенные выше примеры. |
|

## <a name="see-also"></a>См. также

| Раздел   | Описание |
|:--------|:------------|
| [Распространение приложений](../publish/distribute-lob-apps-to-enterprises.md) | Узнайте о параметрах для распространения приложения для корпоративных клиентов. |
| [Жизненный цикл приложения](../launch-resume/app-lifecycle.md) | Дополнительные сведения о жизненном цикле приложения универсальной платформы Windows, и что происходит при запуске Windows, приостанавливает и возобновляет работу приложения. |
| [Ресурсы приложений](../app-resources/index.md) | Узнайте, как создавать, пакет и использовать строки, изображения и файловые ресурсы приложения. |
| [Привязка данных](../data-binding/index.md) | Узнайте, как использовать привязку данных для отображения данных в пользовательском Интерфейсе приложения. |
| [Перечисление устройства](enumerate-devices.md) | Ознакомьтесь с приемами перечисления использовать расширенные найти периферийных устройств.|
| [Версия адаптивных приложений](../debug-test-perf/version-adaptive-apps.md) | Узнайте, как разработать приложение таким образом, чтобы он выполняется в нескольких версиях Windows 10.|
|


## <a name="sample-code"></a>Образец кода
+ [Пример сканера штрихкодов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)
+ [Пример панель денежных средств]( https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CashDrawer)
+ [Пример отображения строки](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LineDisplay)
+ [Пример модуля чтения магнитных](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MagneticStripeReader)
+ [Пример POSPrinter](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/PosPrinter)

