---
author: mcleblanc
description: "Использование компьютера Mac для разработки приложений для Windows."
title: "Установка Windows 10 на Mac"
ms.assetid: 6D520610-5DE0-476E-A792-AA57E002D309
ms.author: markl
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp
ms.openlocfilehash: c73a1d1695e3b8a2eee8f073a5d25586a24a9d24
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="setting-up-your-mac-with-windows-10"></a>Установка Windows 10 на Mac

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Используйте свой компьютер Mac для разработки приложений для Windows.

## <a name="run-windows-on-your-mac-and-use-visual-studio"></a>Установите Windows на Mac и используйте Visual Studio

Вы готовы приступить к разработке универсальных приложений для Windows, но под рукой нет ПК? Нет проблем— можно работать на Mac! Windows 10 и Microsoft Visual Studio можно установить на компьютере Apple с помощью популярных решений сторонних разработчиков: Apple Boot Camp, Oracle VirtualBox, VMware Fusion и Parallels Desktop.

**Примечание.** Вам понадобится загрузочный образ Windows10 на диске или USB-устройстве флэш-памяти. Подписчики MSDN могут скачать образ с веб-сайта скачиваемых файлов для подписчиков MSDN. Если вы не являетесь подписчиком, установщик можно приобрести в [Магазине Windows](http://apps.microsoft.com/windows/app). Его также можно скачать [здесь](http://go.microsoft.com/fwlink/?LinkId=623906), что полезно, если вы уже работаете с Windows и хотите обновиться.

Получив Windows, вы можете установить Visual Studio 2015 со страницы [Загружаемые компоненты для разработчиков Windows 10](https://developer.microsoft.com/en-us/windows/downloads) и приступить к созданию приложений.

**Примечание.** Если вы планируете использовать эмуляторы устройств в Visual Studio, **необходимо** установить 64-разрядную(x64) версию Windows 10 Pro или выше. К сожалению, 64-разрядная версия Windows не будет работать на некоторых старых компьютерах Mac. Узнать, совместимо ли ваше оборудование, можно на[этой странице поддержки Apple](http://go.microsoft.com/fwlink/p/?LinkID=397959).

## <a name="apple-boot-camp"></a>Apple Boot Camp

Приложение Boot Camp Assistant заранее установлено на все последние компьютеры Mac. Оно поможет вам установить Windows 10. Все что вам нужно — копия Windows (из источников, перечисленных выше) и не менее 30ГБ места на диске. После установки вы сможете выбрать загрузку в Mac OSX или Windows 10. Более подробную информацию см. в статье [Страница с инструкциями Apple Boot Camp](http://go.microsoft.com/fwlink/?LinkId=623912).

## <a name="parallels-desktop"></a>Parallels Desktop

Используя Parallels Desktop 11, вы можете запускать приложения для Windows параллельно с существующими приложениями Mac, в том числе Visual Studio и Кортану. Доступна профессиональная версия, в которую входят дополнительные возможности для разработчиков, включая улучшенную отладку и поддержку Docker и Jenkins. Дополнительные сведения и бесплатную пробную версию можно найти на странице [Parallels Desktop](http://go.microsoft.com/fwlink/p/?LinkId=281827).

## <a name="vmware-fusion"></a>VMWare Fusion

VMware Fusion 8 позволяет запускать Visual Studio непосредственно на компьютере Mac. Доступна профессиональная версия, предоставляющая разработчикам несколько дополнительных возможностей, таких как поддержка vSphere. Дополнительные сведения и бесплатную пробную версию можно найти на странице [VMware Fusion](http://go.microsoft.com/fwlink/p/?LinkId=281826).

## <a name="oracle-virtualbox"></a>Oracle VirtualBox

VirtualBox — это бесплатное приложение для запуска на компьютере виртуальных машин, которое поддерживает использование Windows на Mac. Возможностей не слишком много, но цена очень привлекательна. Дополнительные сведения: [VirtualBox](http://go.microsoft.com/fwlink/p/?LinkId=280599).

