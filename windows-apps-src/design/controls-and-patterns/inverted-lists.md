---
Description: Использование перевернутого списка для добавления новых элементов в конце.
title: Перевернутые списки
label: Inverted lists
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 52c1d63d-69c1-48d6-a234-6f39296e4bfd
pm-contact: predavid
design-contact: kimsea
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: c552109b243688c2618425adce797c4d208eac31
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "66364778"
---
# <a name="inverted-lists"></a>Перевернутые списки

 

Вы можете использовать представление списка для отображения беседы в чате с визуально выделенными элементами для представления отправителя/получателя.  Использование разных цветов и горизонтального выравнивания для разделения сообщений отправителя и получателя помогает пользователю быстро ориентироваться в беседе.

> **Важные API**:  [класс ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview), [класс ItemsStackPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel), [свойство ItemsUpdatingScrollMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel.itemsupdatingscrollmode).
 
Как правило, вам потребуется представить список таким образом, чтобы он раскрывался снизу вверх, а не наоборот.  При получении нового сообщения и его добавлении в конец списка предыдущие сообщения сдвигаются вверх, чтобы освободить место, обращая внимание пользователя на последнее сообщение.  Но если пользователь прокрутил вверх, чтобы просмотреть предыдущие ответы, получение нового сообщения не должно вызывать визуального смещения, дабы не нарушать концентрацию пользователя.

![Приложение-чат с перевернутым списком](images/listview-inverted.png)

## <a name="create-an-inverted-list"></a>Создание перевернутого списка

Для создания перевернутого списка используйте представление списка, в качестве панели элементов которого используется класс [ItemsStackPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel). В классе ItemsStackPanel задайте свойству [ItemsUpdatingScrollMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel.itemsupdatingscrollmode) значение [KeepLastItemInView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsupdatingscrollmode).

> [!IMPORTANT]
> Значение перечисления **KeepLastItemInView** доступно, начиная с выпуска Windows 10 версии 1607. Если ваше приложение работает в более ранних версиях Windows 10, вы не сможете использовать это значение.

В это примере показано, как выравнивать элементы представления списка по нижнему краю и как указать, что при изменении элементов последний элемент должен оставаться на виду.
 
 **XAML**
 ```xaml
<ListView>
    <ListView.ItemsPanel>
        <ItemsPanelTemplate>
            <ItemsStackPanel VerticalAlignment="Bottom"
                             ItemsUpdatingScrollMode="KeepLastItemInView"/>
        </ItemsPanelTemplate>
    </ListView.ItemsPanel>
</ListView>
```

## <a name="dos-and-donts"></a>Что рекомендуется и что не рекомендуется делать

- Выравнивайте сообщения отправителя и получателя по противоположным сторонам, чтобы сделать ход беседы понятным для пользователей.
- Убирайте существующие сообщения с помощью анимации для отображения последнего сообщения, если пользователь уже достиг конца беседы в ожидании следующего сообщения.
- Не нарушайте концентрации пользователя перемещением элементов, если он не находится в конце беседы.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример перевернутого списка в XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlBottomUpList)