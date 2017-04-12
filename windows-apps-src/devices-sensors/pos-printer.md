---
author: mukin
title: "Принтер POS"
description: "Эта статья содержит сведения о семействе устройств— принтерах для POS-терминалов"
ms.author: wdg-dev-content
ms.date: 02/21/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: d8340af651157bb6fae82785812f259c16d0a6c0
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="pos-printer"></a>Принтер POS

Позволяет разработчикам приложений печатать на сетевых и подключенных по Bluetooth [принтерах квитанций](https://docs.microsoft.com/en-us/uwp/api/windows.devices.pointofservice.posprinter) с помощью языка управления принтерами Epson ESC/POS.

## <a name="requirements"></a>Требования
Приложения, которым нужно это пространство имен, требуют добавления "pointOfService" [DeviceCapability](https://msdn.microsoft.com/library/4353c4fd-f038-4986-81ed-d2ec0c6235ef) в манифест пакета приложения.

## <a name="device-support"></a>Поддержка устройств
Windows поддерживает возможность печати на сетевых и подключенных по Bluetooth принтерах квитанций с помощью языка управления принтерами Epson ESC/POS. Дополнительные сведения о ESC/POS см. в разделе [Epson ESC/POS с форматированием](https://docs.microsoft.com/en-us/windows/uwp/devices-sensors/epson-esc-pos-with-formatting).

Хотя классы, перечисления и интерфейсы в API поддерживают принтер квитанций, принтер накладных и принтер журналов, интерфейс драйвера поддерживает только принтер квитанций. Попытка использования принтера накладных или принтера журнала в это время приведет к возврату статуса "не реализовано".

Поддержка в настоящее время ограничивается сетевыми и Bluetooth-моделями, перечисленными в таблицах ниже. Подключенные по USB принтеры в настоящее время не поддерживаются. Проверьте позже, не была ли добавлена дополнительная поддержка.

### <a name="stationary-pos-printers-network-bluetooth"></a>Стационарные принтеры POS (сетевые, Bluetooth)
| Изготовитель |    Модели |
|--------------|-----------|
| Epson |    TM-T88V, TM-T70, TM-T20, TM-U220 |

### <a name="mobile-pos-printers-bluetooth"></a>Мобильные принтеры POS (Bluetooth)
| Изготовитель |    Модели |
|--------------|-----------|
| Epson |    Mobilink P20 (TM-P20), Mobilink P60 (TM-P60), Mobilink P80 (TM-P80) |

## <a name="examples"></a>Примеры
См. образец принтера POS в примере реализации.
+ [Образец принтера POS](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/PosPrinter)
