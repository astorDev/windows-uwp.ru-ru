---
author: TerryWarwick
title: Начало работы с POS-устройствами
description: Эта статья содержит сведения о начале работы с API-интерфейсами PointOfService для UWP.
ms.author: jken
ms.date: 05/1/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: a0583adbcef9e45dfe0b2e56e03ce7e0451ac5bb
ms.sourcegitcommit: ce45a2bc5ca6794e97d188166172f58590e2e434
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "1983547"
---
# <a name="getting-started-with-point-of-service"></a>Начало работы с POS-устройствами

Этот раздел содержит темы, которые являются общими для всех категорий POS-устройств.

|Тема |Описание |
|------|------------|
| [Объявление возможностей](pos-basics-capability.md)      | Узнайте, как добавить возможность **pointOfService** в манифест приложения.  Эта возможность требуется для использования пространства имен Windows.Devices.PointOfService.  |
| [Перечисление устройств](pos-basics-enumerating.md)        | Узнайте, как определить средство выбора устройств, использующееся для запроса устройств, доступных системе, а также, как использовать это средство выбора для перечисления POS-устройств.  |
| [Создание объекта устройства](pos-basics-deviceobject.md)  | Узнайте, как создать объект устройства PointOfService, который предоставит вам доступ к нередактируемым свойствам периферийного устройства и возможность утвердить это устройство для исключительного использования. |
| [Получение устройства для исключительного использования ](pos-basics-claim.md)  | Узнайте, как зарезервировать периферийное устройство PointOfService для исключительного использования с помощью модели утверждения PointOfService, обеспечив при этом для других приложений на этом же компьютере возможность доступа к периферийному устройству PointOfService в случае возникновения у них необходимости в исключительном использовании.  |
|

## <a name="see-also"></a>Смежные разделы
[Начало работы с Windows.Devices.PointOfService](pos-get-started.md)


## <a name="sample-code"></a>Пример кода
+ [Пример сканера штрихкодов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)
+ [Образец кассового аппарата]( https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CashDrawer)
+ [Пример строкового дисплея](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LineDisplay)
+ [Пример считывателя магнитных карт](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MagneticStripeReader)
+ [Пример POSPrinter](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/PosPrinter)

