---
Description: Список рекомендаций, которые следует избегать, если нужно создать доступное приложение Windows.
ms.assetid: 024A9B70-9821-45BB-93F1-61C0B2ECF53E
title: Рекомендации по специальным возможностям, которых следует избегать
label: Accessibility practices to avoid
template: detail.hbs
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 75dad7eb676bd2d2a9d95fa57122085329e5e144
ms.sourcegitcommit: 87fd0ec1e706a460832b67f936a3014f0877a88c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83233943"
---
# <a name="accessibility-practices-to-avoid"></a>Рекомендации по специальным возможностям, которых следует избегать

Если вы хотите создать доступное приложение Windows, ознакомьтесь со списком методов, которые следует избегать: 

* **Не создавайте собственные элементы пользовательского интерфейса, если можно использовать стандартные элементы управления Windows** или элементы управления, для которых уже реализована поддержка модели автоматизации пользовательского интерфейса Майкрософт. Стандартные элементы управления Windows по умолчанию поддерживают специальные возможности. При их использовании обычно требуется добавить лишь нескольких атрибутов специальных возможностей, зависящих от конкретного приложения. И напротив, реализация поддержки [**AutomationPeer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationPeer) для настоящего пользовательского элемента управления несколько сложнее (см. раздел [Настраиваемые одноранговые элементы автоматизации](custom-automation-peers.md)).
* **Не включайте статический текст или другие неинтерактивные элементы в последовательность табуляции** (например, установив свойство [**TabIndex**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.tabindex) для неинтерактивного элемента). Наличие неинтерактивных элементов в последовательности табуляции противоречит правилам работы специальных возможностей клавиатуры, так как в результате снижается эффективность навигации с помощью клавиатуры. Во многих специальных возможностях применяются последовательность перехода и способность фокусировать элементы в составе логики представления интерфейса приложения пользователям, использующим специальные возможности. Наличие элементов, содержащих только текст, в последовательности табуляции путает пользователей, привыкших к тому, что последовательность табуляции поддерживается лишь для интерактивных элементов (кнопок, флажков, полей текстового ввода, полей со списками, списков и т. д.).
* **Старайтесь не применять абсолютное размещение элементов пользовательского интерфейса** (например, в элементе [**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas)), так как порядок представления часто отличается от порядка объявления дочерних элементов (который фактически является логическим). По возможности располагайте элементы пользовательского интерфейса в порядке документа или в логическом порядке, чтобы средства чтения экрана считывали их в правильном порядке. Если визуальный порядок элементов пользовательского интерфейса отличается от порядка документа или логического порядка, укажите правильный порядок считывания с помощью явных значений индекса перехода по клавише TAB (задайте [**TabIndex**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.tabindex)).
* **Не используйте цвет в качестве единственного способа передачи информации.** Пользователи, которые не различают цвета, не смогут получить информацию, которая передана только цветом (пример: цветные индикаторы состояния). Включите другие визуальные подсказки, предпочтительно текст, чтобы обеспечить всесторонний доступ к информации.
* **Не обновляйте автоматически весь холст приложения**, если только это не является действительно необходимым для работы приложения. Если вам необходимо автоматически обновлять содержимое страницы, обновляйте только определенные области страницы. При использовании специальных возможностей обычно нужно предполагать, что обновленный холст приложения — это совершенно новая структура, даже если фактические изменения были минимальными. Для пользователя специальных возможностей это означает, что теперь любое представление документа или описание обновленного приложения должно быть создано заново и снова представлено пользователю.
  
  Преднамеренное перемещение по страницам, инициированное пользователем, является достаточным основанием для обновления структуры приложения. Тем не менее убедитесь, что элемент пользовательского интерфейса, который инициирует навигацию, правильно определен или назван, а также указывает на то, что его вызов приведет к изменению контекста и перезагрузке страницы.

  > [!NOTE]
  > При обновлении содержимого области рекомендуется задать для свойства специальных возможностей [**AccessibilityProperties.LiveSetting**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.automationproperties.livesettingproperty) соответствующего элемента нестандартное значение: **Polite** или **Assertive**. Некоторые вспомогательные технологии позволяют связать этот параметр с концепцией динамических областей, сформулированной в рамках стандарта W3C для доступных полнофункциональных интернет-приложений (ARIA), и таким образом информировать пользователя об изменении области содержимого.

* **Не используйте элементы пользовательского интерфейса, которые мигают чаще трех раз в секунду.** У некоторых людей мигающие элементы могут вызвать эпилептический припадок. Лучше всего избегать использования мигающих элементов пользовательского интерфейса.
* **Избегайте автоматического изменения контекста пользователя или активации функций.** Изменение контекста или активация должны происходить только в случае, когда пользователь непосредственно задействует элемент пользовательского интерфейса с фокусом. Изменения контекста пользователя включают изменение фокуса, отображение нового содержимого и перемещение на другую страницу. Изменение контекста без участия пользователя может дезориентировать людей с ограниченными возможностями. Исключениями из этого требования являются: отображение подменю, подтверждение форм, отображение текста справки в другом элементе управления и изменение контекста в ответ на асинхронное событие.

<span id="related_topics"/>

## <a name="related-topics"></a>Связанные темы  
* [Специальные возможности](accessibility.md)
* [Специальные возможности в Магазине](accessibility-in-the-store.md)
* [Контрольный список специальных возможностей](accessibility-checklist.md)
