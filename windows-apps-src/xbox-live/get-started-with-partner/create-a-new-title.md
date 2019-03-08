---
title: Создайте новый заголовок
description: Узнайте, как создать новый заголовок для Xbox Live, с помощью центра партнеров.
ms.assetid: b8bd69e6-887a-4b1f-a42d-8affdbec0234
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 1aa2447a2044bec9b2013b30c05e45342b763fc3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57656569"
---
# <a name="create-a-new-title-for-xbox-live"></a>Создать новый заголовок для Xbox Live

## <a name="introduction"></a>Введение

До написания любого кода, необходимо настроить новый заголовок на портал конфигурации службы.  Дополнительные сведения о конфигурации службы в [конфигурации-службы Xbox Live](../xbox-live-service-configuration.md)

В этой статье поможет выполнить этот процесс с помощью следующих допущениях

1. Вы разрабатываете заголовок универсальной платформы Windows (UWP).  Заголовки универсальной платформы Windows выполните на Xbox One, настольных ПК Windows 10 и мобильных устройств
2. При настройке заголовка в [центра партнеров](https://partner.microsoft.com/dashboard).
3. Вы используете Visual Studio с помощью пользовательской подсистемы игр или Unity.
4. Компьютер разработки под управлением Windows 10.

При условии, что указанные выше условия значение true, в оставшейся части этой статьи показано, как все необходимое для получения title, настроенный в центре партнеров, новый проект, созданный и Xbox Live входа в код написан и протестирован.

> [!NOTE]
> Если вы работаете в Xbox Live Creators Program, выше допущения вам и необходимо следовать указаниям в этой статье.

## <a name="partner-center-setup"></a>Программа установки центра партнеров

Требуется заголовок в Xbox Live включена, созданные в [центра партнеров](https://partner.microsoft.com/dashboard) из предварительных требований для любой рабочей функции Xbox Live.

### <a name="create-a-microsoft-account"></a>Создайте учетную запись Майкрософт
Если у вас нет учетной записи Майкрософт (также известный как MSA), необходимо сначала создать по одному [ https://go.microsoft.com/fwlink/p/?LinkID=254486 ](https://go.microsoft.com/fwlink/p/?LinkID=254486).  Если у вас учетная запись Office 365, используйте Outlook.com или нет учетной записи Xbox Live — у вас уже есть MSA.

### <a name="register-as-an-app-developer"></a>Зарегистрировать как разработчик приложения
Необходимо будет зарегистрировать как разработчик приложения, прежде чем вы можете создать новый заголовок в центре партнеров.

Чтобы зарегистрировать go для https://developer.microsoft.com/en-us/store/register и пройдите процесс регистрации.

### <a name="create-a-new-uwp-title"></a>Создайте новый заголовок универсальной платформы Windows
Далее необходимо заголовок универсальной платформы Windows, определенные в центре партнеров.  Сделать, перейдя к панели мониторинга

![](../images/getting_started/first_xbltitle_dashboard.png)

<p>
</p>
<br>
<p>
</p>

Затем создайте новый заголовок.  Необходимо зарезервировать имя.

![](../images/getting_started/first_xbltitle_newapp.png)

Чтобы затем перейти к *Обзор приложения* страницы для вашего приложения.  — Основная страница, где вы будете настраиваться Xbox Live в группе служб -> меню Xbox Live, показано ниже.

![](../images/getting_started/first_xbltitle_leftnav.png)

<div id="createxblaccount"></div>

## <a name="create-an-xbox-live-account"></a>Создание учетной записи Xbox Live
Необходимо будет Xbox Live учетная запись для входа на Xbox Live.  Если вы еще не создан, используемого для входа в систему для консоли Xbox One, или в приложение Xbox в Windows 10, затем вы можете воспользоваться.

В противном случае следует открывать приложение Xbox на своем Компьютере и войдите с учетной записью Майкрософт.  Он затем включается для использования с Xbox Live.

Приложение Xbox можно найти, перейдя в *меню "Пуск"* и введя в «Xbox», как показано ниже.

![](../images/getting_started/first_xbltitle_xboxapp.png)

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у вас есть новый заголовок создан, теперь вы можете настроить Xbox Live включен заголовок Game Engine, Visual Studio или среды сборки по выбору.

См. в разделе [Пошаговое руководство по интеграции, Xbox Live](partners-step-by-step-guide.md)
