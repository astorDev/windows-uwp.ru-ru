---
author: andrewleader
Description: Special tile templates are unique templates that are either animated, or just allow you to do things that aren't possible with adaptive tiles.
title: Специальные шаблоны плиток
ms.assetid: 1322C9BA-D5B2-45E2-B813-865884A467FF
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 5e408509f4cebbc89587237c6e0dc67bc88b1558
ms.sourcegitcommit: 63cef0a7805f1594984da4d4ff2f76894f12d942
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "4394039"
---
# <a name="special-tile-templates"></a>Специальные шаблоны плиток
 

Специальные шаблоны плиток — это уникальные шаблоны, которые могут быть анимированными или предоставлять возможности, не поддерживаемые адаптивными плитками. Каждый специальный шаблон плитки был специально создан для Windows10, за исключением пиктографического шаблона плитки, классического специального шаблона, который был обновлен для Windows10. Эта статья посвящена трем специальным шаблонам плитки: Iconic (Пиктографический шаблон), Photos (Фотографии) и People (Люди).

## <a name="iconic-tile-template"></a>Пиктографический шаблон плитки


C помощью пиктографического шаблона (также известного как шаблон «IconWithBadge») можно отображать небольшое изображение в центре плитки. Windows 10 поддерживает этот шаблон на телефонах, планшетах и настольных компьютерах.

![Маленькие и средние плитки программы для работы с электронной почтой](images/iconic-template-mail-2sizes.png)

### <a name="how-to-create-an-iconic-tile"></a>Создание пиктографической плитки

Далее приведены все необходимые инструкции по созданию пиктографической плитки для Windows10. Если рассматривать обобщенно, вам необходимо иметь в наличии ресурс пиктографических изображений, затем отправить уведомление на плитку, используя пиктографический шаблон, и наконец отправить уведомление на индикаторе событий, содержащее число, которое будет отображаться на плитке.

![Процесс создания пиктографической плитки](images/iconic-template-dev-flow.png)

**Шаг 1. Создание ресурсов изображений в формате PNG**

Создайте ресурсы значков для плитки и добавьте их к остальным ресурсам проекта. Создайте значок размером не менее чем 200x200 пикселей, который можно использовать как для маленьких плиток, так и для средних на телефоне и настольном компьютере. Чтобы обеспечить наилучшее удобство взаимодействия пользователя с интерфейсом, создайте значок для каждого размера. Заполнение для этих ресурсов не требуется. На рисунке ниже представлены особенности изменения размера значка.

Сохраните ресурсы значков в формате PNG, применив эффект прозрачности. В Windows Phone любой непрозрачный пиксель отображается в белом цвете (RGB 255, 255, 255). Для обеспечения совместимости и удобства при создании значков для настольного компьютера также используйте белый цвет.

Windows 10 на планшетах, ноутбуках и настольных компьютерах поддерживает только квадратные ресурсы значков. Телефон поддерживает как квадратные ресурсы, так и ресурсы, высота которых превышает их ширину (соотношение ширины к высоте 2:3), что упрощает работу с такими изображениями, как значок телефона.

![Изменение размера значка с маленькими и средними плитками на телефоне и настольном компьютере](images/iconic-template-sizing-info.png)

![размеры ресурсов с индикатором событий и без него](images/assetguidance24.png)

Если ресурс квадратный, происходит его автоматическое выравнивание по центру в контейнере:

![размеры квадратного ресурса с индикатором событий и без него](images/assetguidance25.png)

Если ресурс не квадратный, происходит его автоматическое выравнивание по горизонтали/вертикали и привязка по ширине/высоте контейнера:

![размеры неквадратного ресурса с индикатором событий и без него](images/assetguidance26a.png)

![размеры неквадратного ресурса с индикатором событий и без него](images/assetguidance26b.png)

**Шаг 2. Создание базовой плитки**

Пиктографический шаблон можно использовать для работы как с основными, так и со вспомогательными плитками. Если вы используете этот шаблон со вспомогательной плиткой, сначала необходимо создать вспомогательную плитку или использовать уже закрепленную вспомогательную плитку. Основные плитки закрепляются по умолчанию и на них всегда можно отправлять уведомления.

**Шаг 3. Отправка уведомления на плитку**

Несмотря на то, что ход выполнения этого шага зависит от способа отправки уведомления — локально или по инициативе сервера — отправляемые вами полезные данные XML остаются прежними. Чтобы отправить локальное уведомление на плитке, создайте [**TileUpdater**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileUpdater) для плитки (основной или вспомогательной), а затем отправьте уведомление на плитку, для которой используется пиктографический шаблон плитки, как показано ниже. В идеальном случае также необходимо добавить привязки для плиток маленьких и больших размеров с помощью [шаблонов адаптивных плиток](create-adaptive-tiles.md).

Пример кода для полезных данных XML:

```xml
<tile>
  <visual>

    <binding template="TileSquare150x150IconWithBadge">
      <image id="1" src="Iconic.png" alt="alt text"/>
    </binding>
    
    <binding template="TileSquare71x71IconWithBadge">
      <image id="1" src="Iconic.png" alt="alt text"/>
    </binding>

  </visual>
</tile>
```

В полезных данных XML пиктографического шаблона плитки используется элемент изображения, указывающий на изображение, созданное в ходе шага 1. Теперь плитка будет отображать индикатор событий рядом со значком; остается только отправить уведомлений на индикаторе событий.

**Шаг 4. Отправка уведомления индикатора событий на плитку**

По аналогии с шагом 3 ход выполнения этого шага зависит от способа отправки уведомления — локально или по инициативе сервера. Тем не менее отправляемые вами полезные данные XML будут оставаться прежними. Чтобы отправить локальное уведомление на индикаторе событий, создайте [**BadgeUpdater**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.BadgeUpdater) для плитки (основной или вспомогательной), а затем отправьте уведомление на индикаторе с необходимым значением (или очистите индикатор событий).

Пример кода для полезных данных XML:

```xml
<badge value="2"/>
```

Индикатор событий на плитке обновится соответствующим образом.

**Шаг. Сбор воедино**

На следующем рисунке представлены особенности связей различных API-интерфейсов и полезных данных с каждым аспектом пиктографического шаблона плитки. [Уведомление на плитке](https://msdn.microsoft.com/library/windows/apps/hh779724) (содержащее эти элементы &lt;binding&gt;) используется для задания пиктографического шаблона и ресурса изображения; [уведомление на индикаторе событий](https://msdn.microsoft.com/library/windows/apps/hh779719) задает числовое значение; свойства плитки используются для управления отображаемым именем плитки, цветом и т. д.

![API и полезные данные, связанные с пиктографическим шаблоном плитки](images/iconic-template-properties-info.png)

## <a name="photos-tile-template"></a>Шаблон плитки Photos (Фотографии)


Шаблон плитки Photos позволяет показывать слайд-шоу из фотографий на живой плитке. Шаблон поддерживается всеми размерами плиток, включая маленькие, и работает одинаково с любым размером плитки. В примере ниже представлены пять кадров средней плитки, для которой использовался шаблон Photos. Шаблон позволяет изменять размер плитки и создавать эффект плавного перехода. Эта анимация чередуется по выбранным фотографиям и воспроизводится бесконечное количество раз.

![Слайд-шоу из изображений, созданное с помощью шаблона плитки Photos](images/photo-tile-template-image01.jpg)

### <a name="how-to-use-the-photos-template"></a>Использование шаблона Photos

Использовать шаблон photos не сложно, если установлена [Библиотека уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/). Несмотря на то что можно использовать необработанные XML-данные, мы настоятельно рекомендуем использовать библиотеку, чтобы вам не пришлось беспокоиться о создании допустимого кода XML или его экранировании в содержимом.

Windows Phone отображает до 9 фотографий в виде слайд-шоу; на планшете, ноутбуке и настольном компьютере отображается до 12 фотографий.

Сведения об отправке уведомления на плитке см. в статье [Отправка уведомлений](index.md).


```xml
<!--
 
To use the Photos template...
 
 - On any adaptive tile binding (like TileMedium or TileWide)
   - Set the hint-presentation attribute to "photos"
   - Add up to 12 images as children of the binding.
    
-->
 
<tile>
  <visual>
     
    <binding template="TileMedium" hint-presentation="photos">
       
      <image src="Assets/1.jpg" />
      <image src="ms-appdata:///local/Images/2.jpg"/>
      <image src="http://msn.com/images/3.jpg"/>
       
      <!--TODO: Can have 12 images total-->
       
    </binding>
     
    <!--TODO: Add bindings for other tile sizes-->
     
  </visual>
</tile>
```

```csharp
/*
 
To use the Photos template...
 
 - On any TileBinding object
   - Set Content property to new instance of TileBindingContentPhotos
   - Add up to 12 images to Images property of TileBindingContentPhotos.
 
*/
 
TileContent content = new TileContent()
{
    Visual = new TileVisual()
    {
        TileMedium = new TileBinding()
        {
            Content = new TileBindingContentPhotos()
            {
                Images =
                {
                    new TileBasicImage() { Source = "Assets/1.jpg" },
                    new TileBasicImage() { Source = "ms-appdata:///local/Images/2.jpg" },
                    new TileBasicImage() { Source = "http://msn.com/images/3.jpg" }
 
                    // TODO: Can have 12 images total
                }
            }
        }
 
        // TODO: Add other tile sizes
    }
};
```

## <a name="people-tile-template"></a>Шаблон плитки People (Люди)


В приложении «Люди» для Windows 10 используется специальный шаблон плитки, который отображает коллекцию изображений по кругу, перемещающихся по горизонтали или по вертикали на плитке. Этот шаблон доступен, начиная со сборки10572 для Windows 10, и использовать его в своих приложениях могут все.

Шаблон плитки People работает с плитками следующих размеров:

**Средняя плитка** (TileMedium)

![Средняя плитка People](images/people-tile-medium.png)

 

**Широкая плитка** (TileWide)

![Широкая плитка People](images/people-tile-wide.png)

 

**Большая плитка (только для настольного компьютера)** (TileLarge)

![Большая плитка People](images/people-tile-large.png)

 

Если вы используете [Библиотеку уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) и хотите начать работать с шаблоном плитки People, необходимо всего лишь создать новый объект *TileBindingContentPeople* для вашего содержимого *TileBinding*. У класса *TileBindingContentPeople* есть свойство Images, в которое добавляются изображения.

При использовании необработанных XML-данных задайте *представлению подсказки (hint-presentation)* значение "people" и добавьте изображения в качестве дочерних элементов элемента привязки.

В следующем примере кода на языке C# предполагается, что вы используете [Библиотеку уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/).

```csharp
TileContent content = new TileContent()
{
    Visual = new TileVisual()
    {
        TileMedium = new TileBinding()
        {
            Content = new TileBindingContentPeople()
            {
                Images =
                {
                    new TileBasicImage() { Source = "Assets/ProfilePics/1.jpg" },
                    new TileBasicImage() { Source = "Assets/ProfilePics/2.jpg" },
                    new TileBasicImage() { Source = "Assets/ProfilePics/3.jpg" },
                    new TileBasicImage() { Source = "Assets/ProfilePics/4.jpg" },
                    new TileBasicImage() { Source = "Assets/ProfilePics/5.jpg" },
                    new TileBasicImage() { Source = "Assets/ProfilePics/6.jpg" },
                    new TileBasicImage() { Source = "Assets/ProfilePics/7.jpg" },
                    new TileBasicImage() { Source = "Assets/ProfilePics/8.jpg" },
                    new TileBasicImage() { Source = "Assets/ProfilePics/9.jpg" }
                }
            }
        }
    }
};
```

```xml
<tile>
  <visual>
 
    <binding template="TileMedium" hint-presentation="people">
      <image src="Assets/ProfilePics/1.jpg"/>
      <image src="Assets/ProfilePics/2.jpg"/>
      <image src="Assets/ProfilePics/3.jpg"/>
      <image src="Assets/ProfilePics/4.jpg"/>
      <image src="Assets/ProfilePics/5.jpg"/>
      <image src="Assets/ProfilePics/6.jpg"/>
      <image src="Assets/ProfilePics/7.jpg"/>
      <image src="Assets/ProfilePics/8.jpg"/>
      <image src="Assets/ProfilePics/9.jpg"/>
    </binding>
 
  </visual>
</tile>
```

Для обеспечения максимального удобства пользователей каждому размеру плитки должно соответствовать следующее число фотографий:

-   Средняя плитка: 9 фотографий
-   Широкая плитка: 15 фотографий
-   Большая плитка: 20 фотографий

При использовании указанного количества фотографий будет оставаться несколько незаполненных кругов и плитка не будет слишком визуально загружена. Вы можете изменить количество фотографий, чтобы получить желаемый вид.

Описание процесса отправки уведомления см. в разделе [Выбор метода доставки уведомлений](choosing-a-notification-delivery-method.md).

## <a name="related-topics"></a>Еще по теме


* [Полный пример кода на GitHub](https://github.com/WindowsNotifications/quickstart-people-tile-template)
* [Библиотека уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/)
* [Плитки, индикаторы событий и уведомления](index.md)
* [Создание адаптивных плиток](create-adaptive-tiles.md)
* [Схема содержимого плитки](../tiles-and-notifications/tile-schema.md)
 

 




