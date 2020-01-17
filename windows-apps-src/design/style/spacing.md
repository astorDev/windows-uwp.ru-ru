---
title: Интервалы и размеры
description: Новые стили элементов управления Fluent Standard и Compact обеспечивают удобное взаимодействие с пользователем независимо от устройства и метода ввода.
keywords: UWP, Windows 10, элементы управления, размер, плотность, standard, compact
ms.date: 04/19/2019
ms.topic: article
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 08853a201eff063b49dcc69d5827c9b54e324b75
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684872"
---
# <a name="control-size-and-density"></a>Размер и плотность элемента управления

Используйте сочетание размера и плотности элемента управления, чтобы оптимизировать приложение UWP и обеспечить взаимодействие с пользователем, которое лучше всего подходит для функций приложения и его требований к взаимодействию.

По умолчанию приложения UWP отображаются с помощью макета с низкой плотностью (или `Standard`). Тем не менее, начиная с WinUI 2.1 также поддерживается макет с высокой плотностью (или `Compact`), предназначенный для информативного пользовательского интерфейса и аналогичных специализированных сценариев. Его можно указать с помощью ресурса базового стиля (см. примеры ниже).

Функциональные возможности и поведение не изменилось и остается одинаковым для обоих вариантов размера и плотности. Размер шрифта по умолчанию обновлен до 14px для всех элементов управления, чтобы обеспечить поддержку этих двух макетов. Этот размер шрифта поддерживается в различных регионах и на разнообразных устройствах и гарантирует, что приложение останется сбалансированным и удобным для пользователей.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">XAML Controls Gallery</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/Compact Sizing">открыть это приложение и увидеть компактный размер в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="fluent-standard-sizing"></a>Размеры Fluent Standard

*Размеры Fluent Standard* были разработаны, чтобы обеспечить баланс между плотностью информации и удобством пользователя. Фактически все элементы на экране пропорциональны эталонному размеру 40x40 эффективных пикселей (epx), что позволяет выравнивать элементы пользовательского интерфейса по сетке и правильно их масштабировать на уровне системы.

**Размеры Standard предназначены для сенсорного ввода и ввода с помощью указателя.**

> [!NOTE]
>Дополнительные сведения об эффективных пикселях и масштабировании см. в разделе [Введение в проектирование приложений UWP](../basics/design-and-ui-intro.md#effective-pixels-and-scaling).
>
> Дополнительные сведения о масштабировании уровня системы см. в разделе [Выравнивание, поле и заполнение](../layout/alignment-margin-padding.md).

Для обновления Windows 10 за октябрь 2018 года (версии 1809) размер Standard, используемый по умолчанию для всех элементов управления универсальной платформы Windows, был уменьшен, чтобы повысить удобство работы во всех сценариях использования.

На следующем рисунке показана часть изменений макета элемента управления, которые появились в обновлении Windows 10 за октябрь 2018 год. В частности, поля между заголовком и верхним краем элемента управления были уменьшены с 8epx до 4epx, а сетка размером в 44epx была изменена на сетку размером 40epx.

![Пример макета элемента управления Standard](images/standarddensity.png)

*Пример макета элемента управления Standard*

На следующем рисунке показаны изменения, внесенные в размеры элементов управления в обновлении Windows 10 за октябрь 2018 год. В частности, выравнивание по сетке размером в 40epx.

![Пример элемента управления Standard](images/standarddensitycommanding.png)

## <a name="fluent-compact-sizing"></a>Размеры Fluent Compact

Размеры Compact обеспечивают информативные группы элементов управления высокой плотности, которые могут использоваться для следующего:

- просмотр больших объемов содержимого;
- разворачивание видимого содержимого на странице;
- навигация и взаимодействие с элементами управления и содержимым.

**Размеры Compact предназначены для ввода с помощью указателя.**

### <a name="examples"></a>Примеры

Размеры Compact реализуются с помощью специального словаря ресурсов, который может быть указан в приложении на уровне страницы или в определенном макете. Словарь ресурсов доступен в пакете Nuget [WinUI](https://docs.microsoft.com/uwp/toolkits/winui/).

В следующих примерах показано, как стиль `Compact` можно применить к странице и отдельному элементу управления "Сетка".

#### <a name="page-level"></a>Уровень страницы

```xaml
<Page.Resources>
    <ResourceDictionary Source="ms-appx:///Microsoft.UI.Xaml/DensityStyles/Compact.xaml" />
</Page.Resources>
```

#### <a name="grid-level"></a>Уровень сетки

```xaml
<Grid>
    <Grid.Resources>
        <ResourceDictionary Source="ms-appx:///Microsoft.UI.Xaml/DensityStyles/Compact.xaml" />
    </Grid.Resources>
</Grid>
```

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Похожие статьи

- [Рекомендации для целевых объектов сенсорного ввода](../input/guidelines-for-targeting.md)
- [Ссылки на ресурсы ResourceDictionary и XAML](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/resourcedictionary-and-xaml-resource-references)
- [Словарь ресурсов](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resourcedictionary)
- [Стили XAML](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/xaml-styles) 
