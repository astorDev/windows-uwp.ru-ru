---
description: Использование компьютера Mac для разработки приложений для Windows.
title: Установка Windows 10 на Mac
ms.assetid: 6D520610-5DE0-476E-A792-AA57E002D309
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 61918f8f81137dad4db9bf627b7c87e0c05ace8d
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8206419"
---
# <a name="setting-up-your-mac-with-windows-10"></a>Настройка Mac с Windows10


Используйте ваш компьютер Mac для разработки приложений для Windows.

## <a name="run-windows-on-your-mac-and-use-visual-studio"></a>Установите Windows на Mac и используйте Visual Studio

Вы готовы приступить к разработке универсальных приложений для Windows, но под рукой нет ПК? Нет проблем— можно работать на Mac! С помощью популярных сторонними решениями, такие как Apple Boot Camp, Oracle VirtualBox, VMware Fusion и Parallels Desktop можно установить Windows10 и Microsoft Visual Studio на компьютере Apple.

**Примечание**понадобится загрузочный образ Windows10 на диске или USB-накопителя. Подписчики MSDN могут скачать образ с веб-сайта скачиваемых файлов для подписчиков MSDN. Если вы не являетесь подписчиком, установщик можно приобрести в [Microsoft Store](http://apps.microsoft.com/windows/app). Его также можно скачать [здесь](http://go.microsoft.com/fwlink/?LinkId=623906), что полезно, если вы уже работаете с Windows и хотите обновиться.

После создания под управлением Windows, можно установить последний выпуск Visual Studio из [загружаемые компоненты для Windows10 разработчиков](https://developer.microsoft.com/en-us/windows/downloads) и приступить к созданию приложений!

**Примечание**Если вы планируете использовать эмуляторы устройств в Visual Studio, **необходимо** установить 64-разрядный (x 64) версию Windows10 Pro или выше. К сожалению, 64-разрядная версия Windows не будет работать на некоторых старых компьютерах Mac. Узнать, совместимо ли ваше оборудование, можно на[этой странице поддержки Apple](http://go.microsoft.com/fwlink/p/?LinkID=397959).

## <a name="apple-boot-camp"></a>Apple Boot Camp

Приложение Boot Camp Assistant заранее установлено на все последние и запуска его пошаговый процесс установки Windows10. Все что вам нужно — копия Windows (из источников, перечисленных выше) и не менее 30ГБ места на диске. После установки вы сможете выбрать загрузку в Mac OSX или Windows 10. Более подробную информацию см. в статье [Страница с инструкциями Apple Boot Camp](http://go.microsoft.com/fwlink/?LinkId=623912).

## <a name="parallels-desktop"></a>Parallels Desktop

Используя Parallels Desktop 11, вы можете запускать приложения для Windows параллельно с существующими приложениями Mac, в том числе Visual Studio и Кортану. Доступна профессиональная версия, в которую входят дополнительные возможности для разработчиков, включая улучшенную отладку и поддержку Docker и Jenkins. Дополнительные сведения и бесплатную пробную версию можно найти на странице [Parallels Desktop](http://go.microsoft.com/fwlink/p/?LinkId=281827).

## <a name="vmware-fusion"></a>VMWare Fusion

VMware Fusion 8 позволяет запускать Visual Studio непосредственно на компьютере Mac. Доступна профессиональная версия, предоставляющая разработчикам несколько дополнительных возможностей, таких как поддержка vSphere. Дополнительные сведения и бесплатную пробную версию можно найти на странице [VMware Fusion](http://go.microsoft.com/fwlink/p/?LinkId=281826).

## <a name="oracle-virtualbox"></a>Oracle VirtualBox

VirtualBox — это бесплатное приложение для запуска на компьютере виртуальных машин, которое поддерживает использование Windows на Mac. Возможностей не слишком много, но цена очень привлекательна. Дополнительные сведения: [VirtualBox](http://go.microsoft.com/fwlink/p/?LinkId=280599).

