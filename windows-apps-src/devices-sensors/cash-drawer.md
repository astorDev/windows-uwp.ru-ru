---
author: mukin
title: "Кассовый аппарат"
description: "Эта статья содержит сведения о семействе устройств— кассовых аппаратов для POS-терминалов"
ms.author: wdg-dev-content
ms.date: 02/21/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 
ms.openlocfilehash: 376272356cf720ddd9519f0077e771a1016abb1e
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="cash-drawer"></a>Кассовый аппарат

Разработчики приложений могут взаимодействовать с [кассовыми аппаратами](https://docs.microsoft.com/en-us/uwp/api/windows.devices.pointofservice.cashdrawer).

## <a name="requirements"></a>Требования
Приложения, которым нужно это пространство имен, требуют добавления "pointOfService" [DeviceCapability](https://msdn.microsoft.com/library/4353c4fd-f038-4986-81ed-d2ec0c6235ef) в манифест пакета приложения.

## <a name="device-support"></a>Поддержка устройств
Прямое подключение к кассовому аппарату может быть выполнено по сети или через Bluetooth, в зависимости от возможностей конкретного кассового аппарата. Кроме того, кассовые аппараты без поддержки сети или Bluetooth можно подключить через порт DK на поддерживаемом принтере POS или периферийном устройстве Star Micronics DK-AirCash. В настоящее время не поддерживаются кассовые аппараты, подключенные через USB или последовательный порт.

**Примечание.** Для получения дополнительных сведений о DK-AirCash обращайтесь в Star Micronics.

### <a name="networkbluetooth"></a>Сеть или Bluetooth
| Изготовитель |    Модели |
|--------------|-----------|
| Кассовый аппарат APG |    NetPRO, BluePRO |

## <a name="examples"></a>Примеры
См. образец кассового аппарата в примере реализации.
+    [Образец кассового аппарата](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CashDrawer)
