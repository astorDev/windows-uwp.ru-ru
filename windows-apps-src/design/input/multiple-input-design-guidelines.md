---
author: Karl-Bridge-Microsoft
Description: Just as people use a combination of voice and gesture when communicating with each other, multiple types and modes of input can also be useful when interacting with an app.
title: Правила разработки приложений с несколькими видами ввода
ms.assetid: 03EB5388-080F-467C-B272-C92BE00F2C69
label: Multiple inputs
template: detail.hbs
ms.author: kbridge
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9eff1f2f0443664b18e12be5025f838816ef760c
ms.sourcegitcommit: 9cfe2df8d200c3172b369adc36a59f8a789407eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2018
ms.locfileid: "1675749"
---
# <a name="multiple-inputs"></a>Несколько типов ввода


Аналогично тому, как люди используют при общении речь и жесты, при взаимодействии с приложением можно успешно сочетать несколько типов и режимов ввода.


Чтобы обеспечить поддержку как можно большего количества пользователей и устройств, мы рекомендуем разрабатывать приложения для работы с как можно большим количеством типов ввода (жестами, речью, сенсорным вводом, сенсорной панелью, мышью и клавиатурой). Это обеспечит максимальную гибкость, удобство и доступность.

Для начала рассмотрим различные сценарии обработки ввода приложением. Старайтесь, чтобы все части приложения были согласованы и не забывайте о том, что элементы управления платформы имеют встроенную поддержку нескольких видов ввода.

-   Могут ли пользователи взаимодействовать с приложением с помощью нескольких устройств ввода?
-   Поддерживаются ли все методы ввода в любое время? С помощью определенных элементов управления? В определенных случаях или условиях?
-   Является ли какой-либо из методов ввода приоритетным?

## <a name="single-or-exclusive-mode-interactions"></a>Взаимодействие в режиме единого (или монопольного) ввода


В режиме единого взаимодействия поддерживаются несколько типов ввода, однако во время выполнения того или иного действия может использоваться только один из них. Например, распознавание речи для выполнения команд, использование жестов для навигации или ввод текста с помощью сенсорного экрана или жестов, в зависимости от параметров близкого взаимодействия.

## <a name="multimodal-interactions"></a>Мультимодальные взаимодействия

Мультимодальные взаимодействия позволяют последовательно использовать несколько методов для выполнения одного действия.

Речь + жест  
Пользователь указывает на продукт, а затем говорит: "Добавить в корзину".

Речь + сенсорный ввод  
Пользователь выбирает фотографию, нажав и удерживая ее, а затем говорит: «Отправить фото».


