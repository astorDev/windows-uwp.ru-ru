---
author: mijacobs
Description: Content transition animations let you change the content of an area of the screen while keeping the container or background constant. New content fades in. If there is existing content to be replaced, that content fades out.
title: Руководство по анимациям перехода содержимого
ms.assetid: 0188FDB4-E183-466f-8A03-EE3FF5C474B1
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows10, uwp
pm-contact: stmoy
design-contact: conrwi
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 7dc34d15ed7f623b564101f4731beb72bb4bfbcc
ms.sourcegitcommit: 0ab8f6fac53a6811f977ddc24de039c46c9db0ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/15/2018
ms.locfileid: "1652597"
---
# <a name="content-transition-animations"></a>Анимации переходов содержимого



Анимация переходов содержимого используется для изменения содержимого области экрана, при котором фон или контейнер остаются неизменными. Появляется новое содержимое. Если существует содержимое, которое необходимо заменить, оно исчезает.

> **Важные API-интерфейсы**: [**класс ContentThemeTransition (XAML)**](https://msdn.microsoft.com/library/windows/apps/br243104)

## <a name="dos-and-donts"></a>Рекомендации


-   Используйте анимационный эффект открытия, когда необходимо ввести набор новых элементов в пустой контейнер. Например, после начальной загрузки приложения часть содержимого приложения может быть недоступна для немедленного отображения. Когда эта часть содержимого будет готова для отображения, используйте анимацию перехода содержимого, чтобы отобразить это появившееся с опозданием содержимое.
-   Используйте переходы содержимого, чтобы заменить один набор содержимого другим набором, который уже помещен в этот же контейнер в представлении.
-   При добавлении нового содержимого переместите (снизу вверх) содержимое в представление в соответствии с общим интерфейсом страницы или направлением чтения.
-   Представляйте новое содержимое в логичном порядке: например, отображайте самую важную его часть в последнюю очередь.
-   Если вам необходимо обновить содержимое нескольких контейнеров, активируйте все анимации перехода одновременно без сдвига и задержки.
-   Не используйте анимации перехода содержимого при изменении всей страницы. В этом случае используйте анимации перехода страницы.
-   Не используйте анимации перехода содержимого, если содержимое лишь обновляется. Анимации перехода содержимого предназначены для отображения перемещения. Если требуется выполнить обновление, используйте анимации исчезания.



## <a name="related-articles"></a>Еще по теме

**Для разработчиков (XAML)**
* [Обзор анимаций](https://msdn.microsoft.com/library/windows/apps/mt187350)
* [Анимация переходов содержимого](https://msdn.microsoft.com/library/windows/apps/xaml/jj649426)
* [Краткое руководство: анимация пользовательского интерфейса с помощью анимаций библиотеки](https://msdn.microsoft.com/library/windows/apps/xaml/hh452703)
* [**Класс ContentThemeTransition**](https://msdn.microsoft.com/library/windows/apps/br243104)

 

 




