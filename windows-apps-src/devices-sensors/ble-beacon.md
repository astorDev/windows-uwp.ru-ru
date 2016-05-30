---
author: DBirtolo
title: Объявления Bluetooth
description: Этот раздел содержит статьи о том, как интегрировать рекламные объявления Bluetooth с низким энергопотреблением (LE) в приложения универсальной платформы Windows (UWP) с помощью пользователя API AdvertisementWatcher и AdvertisementPublisher.
---

# Объявления Bluetooth

\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

** Важные API ** 

-   [**Windows.Devices.Bluetooth.Advertisement**](https://msdn.microsoft.com/library/windows/apps/windows.devices.bluetooth.advertisement.aspx)

В этой статье приводится обзор объявлений Bluetooth (маяков) для приложений универсальной платформы Windows (UWP).  

## Обзор

Существует две основные функции, которые разработчик может выполнять с помощью API Advertisement:

-   [Advertisement Watcher](https://msdn.microsoft.com/library/windows/apps/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher.aspx): прием сообщений близлежащих маяков и их фильтрация на основе полезной нагрузки или близости.  
-   [Advertisement Publisher](https://msdn.microsoft.com/library/windows/apps/windows.devices.bluetooth.advertisement.bluetoothleadvertisementpublisher.aspx): определение полезной нагрузки, чтобы Windows могла показывать рекламу от имени разработчика.  

Полный пример кода включен в [Пример объявления Bluetooth](http://go.microsoft.com/fwlink/p/?LinkId=619990) на Github


<!--HONumber=May16_HO2-->


