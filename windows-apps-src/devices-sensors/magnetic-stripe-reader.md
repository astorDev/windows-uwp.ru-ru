---
author: mukin
title: "Считыватель магнитных карт"
description: "Эта статья содержит сведения о семействе устройств для POS-терминалов: считывателей магнитных карт"
ms.author: wdg-dev-content
ms.date: 02/21/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: a11fe7a63c0444ac986e7bfe0d50472249e5196e
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="magnetic-stripe-reader"></a>Считыватель магнитных карт

Предоставляет разработчикам приложений доступ к [считывателям магнитных карт](https://docs.microsoft.com/en-us/uwp/api/windows.devices.pointofservice.magneticstripereader) для получения данных с карт, имеющих магнитные ленты, например кредитных/дебетовых, лояльности, доступа ит.д.

## <a name="requirements"></a>Требования
Приложения, которым нужно это пространство имен, требуют добавления "pointOfService" [DeviceCapability](https://msdn.microsoft.com/library/4353c4fd-f038-4986-81ed-d2ec0c6235ef) в манифест пакета приложения.

## <a name="device-support"></a>Поддержка устройств
### <a name="usb"></a>USB
### <a name="supported-vendor-specific"></a>Поддержка конкретного поставщика
Windows предоставляет поддержку для перечисленных ниже считывателей магнитных карт от Magtek и IDTech на основе их идентификатора поставщика и кода продукта (VID/PID).

| Изготовитель |     Модели |    Номер детали |
|--------------|-----------|--------------|
| IDTech | SecureMag (VID:0ACD PID:2010) | IDRE-3x5xxxx |
| |    MiniMag (VID:0ACD PID:0500) |    IDMB-3x5xxxx |
| Magtek | MagneSafe (VID:0801 PID:0011) |    210730xx |
| |    Dynamag (VID:0801 PID:0002) |    210401xx |

### <a name="custom-vendor-specific"></a>Настройка конкретного поставщика
Windows поддерживает внедрение дополнительных драйверов от определенных поставщиков для поддержки дополнительных считывателей магнитных карт. Чтобы узнать о доступности, обращайтесь к изготовителю считывателя магнитных карт.

## <a name="examples"></a>Примеры
См. образец считывателя магнитных карт в примере внедрения.
+    [Образец считывателя магнитных карт](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MagneticStripeReader)
