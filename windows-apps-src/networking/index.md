---
author: DelfCo
ms.assetid: 7bb9fd81-8ab5-4f8d-a854-ce285b0669a4
description: "Технологии для получения доступа к сетевым и веб-службам."
title: "Сеть и веб-службы"
ms.author: bobdel
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 597f78a4048a681dc75b610048a70f7161d0369c
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="networking-and-web-services"></a>Сеть и веб-службы

\[ Обновлено для приложений UWP в Windows10. Статьи, касающиеся Windows8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Для разработчиков, использующих универсальную платформу Windows (UWP), доступны следующие технологии сетевых и веб-служб.

| Раздел                                                                                   | Описание                                                                      |
|-----------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| [Основы работы с сетями](networking-basics.md)                                               | Ниже приведены действия, которые необходимо выполнить для любого приложения, подключаемого к сети.                     |
| [Какую сетевую технологию выбрать?](which-networking-technology.md)                          | Краткий обзор доступных сетевых технологий для разработчиков приложений UWP с рекомендациями о том, как выбрать технологии, наиболее подходящие для определенного приложения.               |
| [Сетевые подключения в фоновом режиме](network-communications-in-the-background.md) | Приложения используют фоновые задачи и два основных механизма для поддержания связи, когда они не находятся на переднем плане: брокер сокетов и триггеры канала управления.                  |
| [Сокеты](sockets.md)                                                                   | Для связи с другими устройствами разработчики приложений UWP могут использовать как [Windows.Networking.Sockets](https://msdn.microsoft.com/library/windows/apps/xaml/windows.networking.sockets.aspx), так и [Winsock](https://msdn.microsoft.com/library/windows/desktop/ms737523). В этом разделе представлено подробное руководство по использованию пространства имен Windows.Networking.Sockets для выполнения сетевых операций. |
| [WebSockets](websockets.md)                                                             | Протокол WebSockets определяет механизм быстрого и безопасного двустороннего взаимодействия между клиентом и сервером через HTTP(S).                 |
| [HttpClient](httpclient.md)                                                             | Для отправки и получения данных по протоколам HTTP 2.0 и HTTP 1.1. используйте API пространства имен [Windows.Web.Http](https://msdn.microsoft.com/library/windows/apps/dn279692).             |
| [Каналы RSS и Atom](web-feeds.md)                                                          | Получайте или создавайте самое свежее и популярное веб-содержимое с помощью сводных веб-каналов, созданных по стандартам RSS и Atom с помощью компонентов в пространстве имен [Windows.Web.Syndication](https://msdn.microsoft.com/library/windows/apps/br243632).                   |
| [Фоновая передача данных](background-transfers.md)                                         | Используйте фоновую передачу данных API, чтобы надежно копировать файлы по сети.           |
