---
author: Jwmsft
Description: "Использование перевернутого списка для добавления новых элементов в конце."
title: "Перевернутые списки"
label: Inverted lists
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: 5f50e490caa5d1d88c2f8315dc47e15b0ae22a05
ms.openlocfilehash: de90c49dfc961241c1da028c8f7278e370a810bd

---
# <a name="inverted-lists"></a>Перевернутые списки

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

Вы можете использовать представление списка для отображения беседы в чате с визуально выделенными элементами для представления отправителя/получателя.  Использование разных цветов и горизонтального выравнивания для разделения сообщений отправителя и получателя помогает пользователю быстро ориентироваться в беседе.
 
Как правило, вам потребуется представить список таким образом, чтобы он раскрывался снизу вверх, а не наоборот.  При получении нового сообщения и его добавлении в конец списка предыдущие сообщения сдвигаются вверх, чтобы освободить место, обращая внимание пользователя на последнее сообщение.  Но если пользователь прокрутил вверх, чтобы просмотреть предыдущие ответы, получение нового сообщения не должно вызывать визуального смещения, дабы не нарушать концентрацию пользователя.

![приложение-чат с перевернутым списком](images/listview-inverted.png)

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**Класс ListView**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listview.aspx)</li>
<li>[**Класс ItemsStackPanel**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemsstackpanel.aspx)</li>
<li>[**Свойство ItemsUpdatingScrollMode**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemsstackpanel.itemsupdatingscrollmode.aspx)</li>
</ul>
</div>


## <a name="create-an-inverted-list"></a>Создание перевернутого списка

Для создания перевернутого списка используйте представление списка, в качестве панели элементов которого используется класс [**ItemsStackPanel**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemsstackpanel.aspx). В классе ItemsStackPanel задайте свойству [**ItemsUpdatingScrollMode**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemsstackpanel.itemsupdatingscrollmode.aspx) значение [**KeepLastItemInView**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemsupdatingscrollmode.aspx).

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

## <a name="dos-and-donts"></a>Рекомендации

- Выравнивайте сообщения отправителя и получателя по противоположным сторонам, чтобы сделать ход беседы понятным для пользователей.
- Убирайте существующие сообщения с помощью анимации для отображения последнего сообщения, если пользователь уже достиг конца беседы в ожидании следующего сообщения.
- Не нарушайте концентрации пользователя перемещением элементов, если он не находится в конце беседы.



<!--HONumber=Dec16_HO2-->


