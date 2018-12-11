---
title: Начало работы с POS-устройствами
description: Эта статья содержит сведения о начале работы с API-интерфейсами PointOfService для UWP.
ms.date: 06/13/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 1b4ff9443c40cf44e171bf898b627de3e2819034
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8896448"
---
# <a name="getting-started-with-point-of-service"></a>Начало работы с POS-устройствами

## <a name="pointofservice-basics"></a>Основы PointOfService

Этот раздел содержит темы, которые являются общими для всех категорий POS-устройств.

|Тема |Описание |
|------|------------|
| [Объявление возможностей](pos-basics-capability.md)      | Узнайте, как добавить возможность **pointOfService** в манифест приложения.  Эта возможность требуется для использования пространства имен Windows.Devices.PointOfService.  |
| [Перечисление устройств](pos-basics-enumerating.md)        | Узнайте, как определить средство выбора устройств, использующееся для запроса устройств, доступных системе, а также, как использовать это средство выбора для перечисления POS-устройств.  |
| [Создание объекта устройства](pos-basics-deviceobject.md)  | Узнайте, как создать объект устройства PointOfService, который предоставит вам доступ к нередактируемым свойствам периферийного устройства и возможность утвердить это устройство для исключительного использования. |
| [Утверждение и включить ](pos-basics-claim.md)  | Узнайте, как зарезервировать периферийное устройство PointOfService для исключительного использования и включать для операций ввода-вывода.  |
| [Общий доступ к периферийным устройствам для других пользователей](pos-basics-sharing.md) | Узнайте, как совместного доступа сети или периферийных устройств Bluetooth подключены другие компьютеры в среде, где компьютерах полагаться на периферийных устройств, а не выделенный периферийные устройства, подключенные к каждому компьютеру.
| [PointOfService-комплексного](pos-get-started.md)  | Это пример начала до конца для взаимодействия с использованием приведенных выше примерах периферийные устройства PointOfService. |
|

## <a name="see-also"></a>См. также

| Статья   | Описание |
|:--------|:------------|
| [Распространение приложения](../publish/distribute-lob-apps-to-enterprises.md) | Узнайте о доступных для распространения приложения для корпоративных клиентов. |
| [Жизненный цикл приложения](../launch-resume/app-lifecycle.md) | Сведения о жизненном цикле приложения UWP и что происходит, когда Windows запускает приостанавливает и возобновляет работу приложения. |
| [Ресурсы приложения](../app-resources/index.md) | Узнайте, как создавать, упаковывать и использовать строки, изображения и файловые ресурсы вашего приложения. |
| [Привязка данных](../data-binding/index.md) | Узнайте, как использовать привязку данных для отображения данных в пользовательском Интерфейсе приложения. |
| [Перечисление устройств](enumerate-devices.md) | Узнайте методы использования advanced перечисления найти периферийных устройств.|
| [Версия адаптивных приложений](../debug-test-perf/version-adaptive-apps.md) | Бережливого как спроектировать приложение так, чтобы он выполнялся в нескольких версиях Windows 10.|
|


## <a name="sample-code"></a>Пример кода
+ [Пример сканера штрихкодов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)
+ [Образец кассового аппарата]( https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CashDrawer)
+ [Пример строкового дисплея](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LineDisplay)
+ [Пример считывателя магнитных карт](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MagneticStripeReader)
+ [Пример POSPrinter](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/PosPrinter)

