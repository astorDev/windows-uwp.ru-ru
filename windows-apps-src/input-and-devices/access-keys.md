---
author: Karl-Bridge-Microsoft
Description: "Реализуйте доступ с клавиатуры с помощью навигации клавишей TAB и клавиш доступа, чтобы пользователи могли переходить по элементам интерфейса с помощью клавиатуры."
title: "Клавиши доступа"
ms.assetid: C2F3F3CE-737F-4652-98B7-5278A462F9D3
label: Access keys
template: detail.hbs
keywords: "Клавиши доступа, клавиатура, специальные возможности"
translationtype: Human Translation
ms.sourcegitcommit: 2b6b1d7b1755aad4d75a29413d989c6e8112128a
ms.openlocfilehash: dfe89e4d4fd089dde6b7b307325b8fe43de82c10

---

# <a name="access-keys"></a>Клавиши доступа

Пользователи, которым сложно работать с мышью, например люди с нарушениями опорно-двигательного аппарата, часто применяют клавиатуру для перемещения и взаимодействия с приложением.  Платформа XAML позволяет предоставить доступ к элементам интерфейса с помощью клавиши TAB и клавиш доступа.

- Навигация клавишей TAB — это базовая возможность доступа с помощью клавиатуры (включенная по умолчанию), которая позволяет пользователям перемещать фокус между элементами интерфейса с помощью клавиши TAB и стрелок на клавиатуре.
- Клавиши доступа — это дополнительная возможность доступа (которую можно реализовать в вашем приложении) для быстрого доступа к командам приложения с помощью сочетания клавиши-модификатор (ALT) и одной или нескольких буквенно-цифровых клавиш (обычно это буква, связанная с командой). Среди распространенных клавиш доступа _ALT+F_ для открытия меню "Файл" и _ALT+AL_ для выравнивать по левому краю.  

Дополнительные сведения о навигации с помощью клавиатуры и специальных возможностях см. в разделах [Взаимодействие с помощью клавиатуры](https://msdn.microsoft.com/windows/uwp/input-and-devices/keyboard-interactions) и [Специальные возможности клавиатуры](https://msdn.microsoft.com/windows/uwp/accessibility/keyboard-accessibility). Предполагается, что вы знакомы с понятиями, которые обсуждаются в этих статьях.

## <a name="access-key-overview"></a>Обзор клавиш доступа

Клавиши доступа позволяют пользователям напрямую активировать кнопки или устанавливать фокус с помощью клавиатуры, не нажимая несколько раз клавиши со стрелками и клавишу TAB. Клавиши доступа должны быть легко заметны, поэтому следует обозначить их непосредственно в пользовательском интерфейсе, например с помощью всплывающего индикатора событий над элементом управления с клавишей доступа.

![Пример клавиш доступа и связанных подсказок в Microsoft Word](images/keyboard/accesskeys-keytips.png)

_Рис. 1. Пример клавиш доступа и связанных подсказок в Microsoft Word._

Клавиша доступа — это один или несколько буквенно-цифровых символов, связанных с элементом пользовательского интерфейса. Например, в Microsoft Word _H_ использует для вкладки "Главная", _2_ для кнопки "Отменить" и _JI_для вкладки "Рисование".

**Область клавиши доступа**

Клавиша доступа относится к определенной области. Например, на рисунке 1 клавиши _F_, _H_, _N_ и _JI_ относятся к области страницы.  Когда пользователь нажимает _H_, область изменяется на вкладку "Главная", и отображаются ее клавиши доступа, как показано на рисунке 2. Клавиши доступа _V_, _FP_, _FF_ и _FS_ относятся к области вкладки "Главная".

![Пример клавиш доступа и связанные подсказки для области вкладки "Главная" в Microsoft Word](images/keyboard/accesskeys-keytips-hometab.png)

_Рис. 2. Пример клавиш доступа и связанные подсказки для области вкладки "Главная" в Microsoft Word._

У двух элементов могут быть одинаковые клавиши доступа, если элементы относятся к разным областям. Например, _2_ — это клавиша доступа для кнопки "Отменить" в области страницы (рис. 1), а также для курсива в области вкладки "Главная" (рис. 2). Все клавиши доступа относятся к области по умолчанию, если не задана другая область.

**Последовательность клавиш доступа**

Сочетания клавиш доступа обычно нажимаются один по одной клавише, а не одновременно. (Существует исключение, которое мы рассмотрим в следующем разделе.) Последовательность нажатий клавиш, необходимая для выполнения действия — это _последовательность клавиши доступа_. Пользователь нажимает клавишу ALT, чтобы инициировать последовательность. Клавиша доступа вызывается, когда пользователь нажимает последнюю клавишу в последовательности. Например, чтобы открыть вкладку "Вид" в Word, пользователь нажимает последовательность клавиши доступа _ALT, W_.

Пользователь может вызывать несколько клавиш доступа в последовательности. Например, чтобы открыть форматирование по образцу в документе Word, пользователь нажимает ALT, чтобы инициализировать последовательность, затем нажимает _H_ для перехода в раздел "Главная" и изменения области клавиши доступа, нажмите _F_ и, наконец, _P_. _H_ и _FP_ — это клавиши доступа для вкладки "Главная" и кнопки "Форматирование по образцу" соответственно.

Некоторые элементы завершают последовательность клавиши доступа после их вызова (например, кнопку "Форматирование по образцу"), а некоторые нет (например, вкладка "Главная"). Вызов клавиши доступа может привести к выполнению команды, перемещению фокуса, изменению области клавиши доступа и другому действию, связанному с ним.

## <a name="access-key-user-interaction"></a>Взаимодействие с пользователем с помощью клавиш доступа

Чтобы понять API-интерфейса клавиш доступа, сначала необходимо изучить модель взаимодействия с пользователем. Ниже представлен обзор модели взаимодействия с пользователем с помощью клавиш доступа.

- Когда пользователь нажимает клавишу ALT, начинается последовательность клавиша доступа, даже если фокус находится на элементе управления вводом. После этого пользователь может нажать клавишу доступа для вызова связанного с ней действия. Для этого необходимо визуально обозначить доступные клавиши доступа в пользовательском интерфейсе, например с помощью всплывающих индикаторов событий, которые отображаются при нажатии клавиши ALT.
- Когда пользователь одновременно нажимает клавишу ALT и клавишу доступа, немедленно вызывается клавиша доступа. Это похоже на сочетание клавиш, заданное ALT и _клавишей доступа_. В этом случае визуальная возможность клавиши доступа не отображается. Однако вызов клавиши доступа может привести к изменению ее области. В этом случае инициируется последовательность клавиши доступа, и отображаются визуальные возможности для новой области.
    > [!NOTE]
    > Такая функциональность применима только к клавишам доступа с одним символом. Сочетание ALT+_клавиша доступа_ не поддерживается для клавиш доступа с несколькими символами.    
- Если существуют клавиши доступа с несколькими символами, в которых определенные символы совпадают, то они фильтруются, когда пользователь нажимает общей символ. Например, предположим, что показано три клавиши доступа: _A1_, _A2_ и _C_. Если пользователь нажимает _A_, отображаются только клавиши доступа _A1_ и _A2_, а визуальная возможность для C скрывается.
- Клавиша ESC удаляет один уровень фильтрации. Например, если существуют клавиши доступа _B_, _ABC_, _ACD_ и _ABD_, а пользователь нажимает _A_, будут показаны только _ABC_, _ACD_ и _ABD_. Если пользователь затем нажмет _B_, отображаются только _ABC_ и _ABD_. Если пользователь нажимает клавишу ESC, один уровень фильтрации удаляется, после чего будут показаны клавиши доступа _ABC_, _ACD_ и _ABD_. Если пользователь еще раз нажмет ESC, будет удален другой уровень фильтрации, и активируются все клавиши доступа — _B_, _ABC_, _ACD_ и _ABD_ — и их визуальные возможности.
- Клавиша ESC возвращает пользователя к предыдущей области. Клавиши доступа могут относиться к различным областям, что упрощает навигацию в приложениях с большим количеством команд. Последовательность клавиши доступа всегда начинается в главной области. Все клавиши доступа принадлежат главной области, за исключением тех, для которых в качестве области задан определенный элемент пользовательского интерфейса. Когда пользователь вызывает клавишу доступа элемента-владельца области, платформа XAML автоматически переключает область и добавляет ее к внутреннему стеку навигации клавиш доступа. Клавиша ESC позволяет сделать шаг назад в стеке навигации клавиш доступа.
- Существует несколько способов закрыть последовательность клавиш доступа.
    - Пользователь может нажать ALT, чтобы закрыть текущую последовательность. Помните, что при нажатии клавиши ALT также инициируется последовательность клавиш доступа.
    - Клавиша ESC закрывает последовательность, если клавиша доступа находится в главной области и не скрыта.
        > [!NOTE]
        > Нажатие клавиши ESC также передается на уровень пользовательского интерфейса для обработки.
- Клавиша TAB закрывает последовательность клавиш доступа и возвращается к навигации клавишей TAB.
- Клавиша ВВОД закрывает последовательность клавиш доступа и отправляет сведения о нажатии элементу с фокусом.
- Клавиши со стрелками закрывают последовательность клавиш доступа и отправляют сведения о нажатии элементу с фокусом.
- Событие указателя, например щелчок мыши или касание, закрывает последовательность клавиш доступа.
- По умолчанию при вызове клавиши доступа, последовательность закрывается.  Однако вы можете переопределить такое поведение, задав для свойства [ExitDisplayModeOnAccessKeyInvoked](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.exitdisplaymodeonaccesskeyinvoked.aspx) значение **false**.
- Конфликты клавиш доступа возникают, если детерминистический конечный автомат не реализуем. Конфликты клавиш доступа нежелательны, но могут возникать из-за большого числа команд, проблем с локализацией или созданием клавиш доступа во время выполнения.

 Возможны два вида конфликтов.
 - Если у двух элементов пользовательского интерфейса одинаковые клавиши доступа, принадлежащие одной области. Например, клавиша доступа _A1_ для `button1` и клавиша доступа _A1_ для `button2`, которая принадлежит области по умолчанию. В этом случае система разрешит конфликт, выбрав клавишу доступа первого элемента, добавленного в виртуальное дерево. Остальные клавиши доступа игнорируются.
 - Если существует несколько вариантов в одной области клавиш доступа. Например, _A_ и _A1_. Когда пользователь нажимает _A_, у системы есть два варианта: вызвать клавишу доступа _A_ или использовать символ A клавиши доступа _A1_. В этом случае система обработает только первый вызов клавиши доступа, достигнутый автоматом. В нашем примере с _A_ и _A1_ система вызовет только клавишу доступа _A_.
-   Когда пользователь нажимает недопустимое значение клавиши доступа в последовательности, ничего не происходит. Существуют две категории клавиш, которые считаются допустимыми в последовательности клавиш доступа:
 - специальные клавиши для выхода из последовательности клавиш доступа — это ESC, ALT, клавиши со стрелками, ВВОД и TAB;
 - буквенно-цифровые символы, назначенные клавишам доступа.

## <a name="access-key-apis"></a>API-интерфейсы клавиш доступа

Для поддержки взаимодействия с пользователем с помощью клавиш доступ платформа XAML предоставляет описанные ниже API-интерфейсы.

**AccessKeyManager**

[AccessKeyManager](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.input.accesskeymanager.aspx) — это вспомогательный класс, с помощью которого можно управлять пользовательским интерфейсом, когда клавиши доступа отображаются или скрываются. Событие [IsDisplayModeEnabledChanged](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.input.accesskeymanager.isdisplaymodeenabledchanged.aspx) вызывается каждый раз, когда приложение инициирует последовательность клавиш доступа и выходит из нее. Вы можете запросить свойство [IsDisplayModeEnabled](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.input.accesskeymanager.isdisplaymodeenabled.aspx), чтобы определить, отображаются ли визуальные возможности.  Вы также можете вызвать [ExitDisplayMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.input.accesskeymanager.exitdisplaymode.aspx), чтобы принудительно закрыть последовательность клавиши доступа.

> [!NOTE]
> Визуализация клавиш доступа не реализована, это необходимо сделать самостоятельно.  

**AccessKey**

Свойство [AccessKey](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.accesskey.aspx) позволяет определить клавишу доступа для UIElement или [TextElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.textelement.accesskey.aspx). Если у двух элементов одинаковые клавиши доступа в одной области, обрабатывается только первый элемент, добавленный в виртуальное дерево.

Чтобы платформа XAML могла обработать клавиши доступа, элементы пользовательского интерфейса необходимо реализовать в визуальном дереве. Если в визуальном дереве нет элементов с клавишей доступа, события клавиши доступа не вызываются.

API клавиш доступа не поддерживают символы, для которых требуется два нажатия клавиши. Отдельный символ должен соответствовать клавише на клавиатуре в исходной раскладке.  

**AccessKeyDisplayRequested и AccessKeyDisplayDismissed**

События [AccessKeyDisplayRequested](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.accesskeydisplayrequested.aspx) и [AccessKeyDisplayDismissed](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.accesskeydisplaydismissed.aspx) инициируются, если следует показать или скрыть визуальную возможность клавиши доступа. Эти события не вызываются для элементов, свойству [Visibility](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.visibility.aspx) которых присвоено значение **Collapsed**. Событие AccessKeyDisplayRequested вызывается во время последовательности клавиш доступа каждый раз, когда пользователь нажимает символ, который применяется клавишей доступа. Например, если задана клавиша доступа _AB_, это событие инициируется, когда пользователь нажимает ALT и когда пользователь нажимает _A_. Если нажать _B_, инициируется событие AccessKeyDisplayDismissed.

**AccessKeyInvoked**

Событие [AccessKeyInvoked](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.accesskeyinvoked.aspx) инициируется, когда пользователь достигает последнего символа клавиши доступа. Клавиша доступа может использовать один или несколько символов. Например, для клавиш доступа _A_ и _BC_, когда пользователь нажимает _ALT, A_ или _ALT, B, C_, событие инициируется, а когда пользователь нажимает только _ALT, B_ — нет. Это событие вызывается, когда клавиша нажимается, а не когда она отпускается.

**IsAccessKeyScope**

Свойство [IsAccessKeyScope](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.isaccesskeyscope.aspx) позволяет указать, что UIElement — это корневой элемент области клавиши доступа. Событие AccessKeyDisplayRequested вызывается для этого элемента, но не для его потомков. Когда пользователь вызывает этот элемент, платформа XAML автоматически изменяет область и вызывает событие AccessKeyDisplayRequested для его потомков и событие AccessKeyDisplayDismissed для других элементах пользовательского интерфейса (включая родительский элемент).  Последовательность клавиш доступа не закрывается, если область изменяется.

**AccessKeyScopeOwner**

Чтобы элемент участвовал в области другого элемента (источника), который не является его родителем в визуальном дереве, можно задать свойство [AccessKeyScopeOwner](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.accesskeyscopeowner.aspx). Свойству IsAccessKeyScope элемента, который привязывается к свойству AccessKeyScopeOwner, должно быть задано значение **true**. В противном случае создается исключение.

**ExitDisplayModeOnAccessKeyInvoked**

По умолчанию когда клавиша доступа вызывается и элемент не является владельцем области, последовательность клавиш доступа завершается, и вызывается событие [AccessKeyManager.IsDisplayModeEnabledChanged](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.input.accesskeymanager.isdisplaymodeenabledchanged.aspx). Вы можете задать для свойства [ExitDisplayModeOnAccessKeyInvoked](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.exitdisplaymodeonaccesskeyinvoked.aspx) значение **false**, чтобы переопределить это поведение и не закрывать последовательность после ее вызова. (Это свойство есть в элементах [UIElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.exitdisplaymodeonaccesskeyinvoked.aspx) и [TextElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.textelement.exitdisplaymodeonaccesskeyinvoked.aspx).)

> [!NOTE]
> Если элемент — владелец области (`IsAccessKeyScope="True"`), приложение переходит в новую область клавиш доступа, а событие IsDisplayModeEnabledChanged не вызывается.

**Локализация**

Клавиши доступа можно локализовать на нескольких языках и загружать во время выполнения с помощью API-интерфейсов [ResourceLoader](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.resources.resourceloader.aspx).

## <a name="control-patterns-used-when-an-access-key-is-invoked"></a>Шаблоны элементов управления, которые используются, когда вызывается клавиша доступа

Шаблоны элементов управления — это реализации интерфейса, предоставляющие общие функции элементов управления. Например, в кнопках реализован шаблон элемента управления **Invoke**, и они вызывают событие **Click**. Когда вызывается клавиша доступа, платформа XAML определяет, реализован ли в вызванном элементе шаблон элемента управления, и выполняет его, если это так. Если в элементе несколько шаблонов элементов управления, вызывается только один из них, а остальные игнорируются. Поиск шаблонов элементов управления осуществляется в следующем порядке:

1.  Вызов. Например, кнопка.
2.  Переключение. Например, флажок.
3.  Выбор. Например, переключатель.
4.  Развернуть и свернуть. Например, поле со списком.

Если шаблон элемента управления не найден, вызов клавиши доступа отображается как отсутствие операции, при этом записывается сообщение отладки, которое поможет разобраться с этой ситуацией: "Шаблоны автоматизации для этого компонента не найдены. Реализуйте необходимое поведение в обработчике события AccessKeyInvoked. Если присвоить свойству Handled значение true в обработчике событий, это сообщение будет отключено".

> [!NOTE]
> Чтобы это сообщение отображалось, тип процесса приложения отладчика должен быть задан в параметрах отладки Visual Studio как _Смешанный (управляемый и машинный)_ или _Машинный_.

Если вы не хотите, чтобы клавиша доступа выполняла шаблон элемента управления по умолчанию, или если элемент не использует шаблон элемента управления, следует обработать событие [AccessKeyInvoked](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.accesskeyinvoked.aspx) и реализовать необходимое поведение.
```csharp
private void OnAccessKeyInvoked(UIElement sender, AccessKeyInvokedEventArgs args)
{
    args.Handled = true;
    //Do something
}
```

Дополнительные сведения о шаблонах элементов управления см. в разделе [Обзор шаблонов элементов управления модели автоматизации пользовательского интерфейса](https://msdn.microsoft.com/library/windows/desktop/ee671194.aspx).

## <a name="access-keys-and-narrator"></a>Клавиши доступа и экранный диктор

В среде выполнения Windows есть поставщики автоматизации пользовательского интерфейса, которые предоставляют свойства элементов модели автоматизации пользовательского интерфейса Майкрософт. Эти свойства позволяют клиентским приложениям получать сведения об элементах пользовательского интерфейса. Свойство [AutomationProperties.AccessKey](https://msdn.microsoft.com/library/windows/apps/hh759763) позволяет клиентам, таким как экранный диктор, определять клавишу доступа, связанную с элементом. Экранный диктор читает это свойство каждый раз, когда элемент получает фокус. Если для AutomationProperties.AccessKey значение не задано, платформа XAML возвращает значение свойства [AccessKey](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.accesskey.aspx) из UIElement или TextElement. Вам не нужно настраивать AutomationProperties.AccessKey, если свойству AccessKey уже присвоено значение.

## <a name="example-access-key-for-button"></a>Пример: клавиша доступа для кнопки

В этом примере показано, как создать клавишу доступа для кнопки. В качестве визуальной возможности используются подсказки для реализации всплывающего индикатора событий, который содержит клавишу доступа.

> [!NOTE]
> Подсказка применяется для простоты, но мы рекомендуем создать собственный элемент управления для отображения, используя, например, [всплывающее окно](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.popup.aspx).

Платформа XAML автоматически вызывает обработчик события Click, поэтому вам не нужно обрабатывать событие AccessKeyInvoked. В примере представлены визуальные возможности только для оставшихся символов для вызова клавиш доступа с помощью свойства [AccessKeyDisplayRequestedEventArgs.PressedKeys](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.input.accesskeydisplayrequestedeventargs.pressedkeys.aspx). Например, если отображаются три клавиши доступа _A1_, _A2_ и _C_, и пользователь нажимает _A_, остаются только клавиши доступа _A1_ и _A2_, которые отображаются как _1_ и _2_ вместо _A1_ и _A2_.

```xaml
<StackPanel
        VerticalAlignment="Center"
        HorizontalAlignment="Center"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Button Content="Press"
                AccessKey="PB"
                AccessKeyDisplayDismissed="OnAccessKeyDisplayDismissed"
                AccessKeyDisplayRequested="OnAccessKeyDisplayRequested"
                Click="DoSomething" />
        <TextBlock Text="" x:Name="textBlock" />
    </StackPanel>
```

```csharp
 public sealed partial class ButtonSample : Page
    {
        public ButtonSample()
        {
            this.InitializeComponent();
        }

        private void DoSomething(object sender, RoutedEventArgs args)
        {
            textBlock.Text = "Access Key is working!";
        }

        private void OnAccessKeyDisplayRequested(UIElement sender, AccessKeyDisplayRequestedEventArgs args)
        {
            var tooltip = ToolTipService.GetToolTip(sender) as ToolTip;

            if (tooltip == null)
            {
                tooltip = new ToolTip();
                tooltip.Background = new SolidColorBrush(Windows.UI.Colors.Black);
                tooltip.Foreground = new SolidColorBrush(Windows.UI.Colors.White);
                tooltip.Padding = new Thickness(4, 4, 4, 4);
                tooltip.VerticalOffset = -20;
                tooltip.Placement = PlacementMode.Bottom;
                ToolTipService.SetToolTip(sender, tooltip);
            }

            if (string.IsNullOrEmpty(args.PressedKeys))
            {
                tooltip.Content = sender.AccessKey;
            }
            else
            {
                tooltip.Content = sender.AccessKey.Remove(0, args.PressedKeys.Length);
            }

            tooltip.IsOpen = true;
        }
        private void OnAccessKeyDisplayDismissed(UIElement sender, AccessKeyDisplayDismissedEventArgs args)
        {
            var tooltip = ToolTipService.GetToolTip(sender) as ToolTip;
            if (tooltip != null)
            {
                tooltip.IsOpen = false;
                //Fix to avoid show tooltip with mouse
                ToolTipService.SetToolTip(sender, null);
            }
        }
    }
```

## <a name="example-scoped-access-keys"></a>Пример: клавиши доступа с областью действия

В этом примере показано, как создать клавиши доступа с областью действия. Свойство IsAccessKeyScope элемент PivotItem отключает отображение клавиш доступа дочерних элементов PivotItem, когда пользователь нажимает ALT. Эти клавиши доступа отображаются, только если пользователь вызывает PivotItem, поскольку платформа XAML автоматически переключает область. Эта платформа также скрывает клавиши доступа других областей.

В этом примере также показано, как обрабатывать событие AccessKeyInvoked. В элементе PivotItem не реализован шаблон элемент управления, поэтому платформа XAML не вызывает какие-либо действия по умолчанию. В этой реализации показано, как выбрать элемент PivotItem, вызванный с помощью клавиши доступа.

Наконец, в примере показано событие IsDisplayModeChanged, в котором можно выполнить определенное действие при изменении режима отображения. В этом примере элемент управления Pivot свернут, пока пользователь не нажмет ALT. Когда пользователь закончит взаимодействовать с элементом управления Pivot, он снова будет свернут. Вы можете использовать свойство IsDisplayModeEnabled, чтобы узнать, включен ли режим отображения клавиш доступа.

```xaml   
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Pivot x:Name="MyPivot" VerticalAlignment="Center" HorizontalAlignment="Center" >
            <Pivot.Items>
                <PivotItem
                    x:Name="PivotItem1"
                    AccessKey="A"
                    AccessKeyInvoked="OnAccessKeyInvoked"
                    AccessKeyDisplayDismissed="OnAccessKeyDisplayDismissed"
                    AccessKeyDisplayRequested="OnAccessKeyDisplayRequested"
                    IsAccessKeyScope="True">
                    <PivotItem.Header>
                        <TextBlock Text="A Options"/>
                    </PivotItem.Header>
                    <StackPanel Orientation="Horizontal" >
                        <Button Content="ButtonAA" AccessKey="A"
                                AccessKeyDisplayDismissed="OnAccessKeyDisplayDismissed"
                                AccessKeyDisplayRequested="OnAccessKeyDisplayRequested" />
                        <Button Content="ButtonAD1" AccessKey="D1"
                                AccessKeyDisplayDismissed="OnAccessKeyDisplayDismissed"
                                AccessKeyDisplayRequested="OnAccessKeyDisplayRequested"  />
                        <Button Content="ButtonAD2" AccessKey="D2"
                                AccessKeyDisplayDismissed="OnAccessKeyDisplayDismissed"
                                AccessKeyDisplayRequested="OnAccessKeyDisplayRequested"/>
                    </StackPanel>
                </PivotItem>
                <PivotItem
                    x:Name="PivotItem2"
                    AccessKeyInvoked="OnAccessKeyInvoked"
                    AccessKeyDisplayDismissed="OnAccessKeyDisplayDismissed"
                    AccessKeyDisplayRequested="OnAccessKeyDisplayRequested"
                    AccessKey="B"
                    IsAccessKeyScope="true">
                    <PivotItem.Header>
                        <TextBlock Text="B Options"/>
                    </PivotItem.Header>
                    <StackPanel Orientation="Horizontal">
                        <Button AccessKey="B" Content="ButtonBB"
                                AccessKeyDisplayDismissed="OnAccessKeyDisplayDismissed"
                                AccessKeyDisplayRequested="OnAccessKeyDisplayRequested"  />
                        <Button AccessKey="F1" Content="ButtonBF1"
                                AccessKeyDisplayDismissed="OnAccessKeyDisplayDismissed"
                                AccessKeyDisplayRequested="OnAccessKeyDisplayRequested"  />
                        <Button AccessKey="F2" Content="ButtonBF2"  
                                AccessKeyDisplayDismissed="OnAccessKeyDisplayDismissed"
                                AccessKeyDisplayRequested="OnAccessKeyDisplayRequested"/>
                    </StackPanel>
                </PivotItem>
            </Pivot.Items>
        </Pivot>
    </Grid>
```

```csharp
public sealed partial class ScopedAccessKeys : Page
    {
        public ScopedAccessKeys()
        {
            this.InitializeComponent();
            AccessKeyManager.IsDisplayModeEnabledChanged += OnDisplayModeEnabledChanged;
            this.Loaded += OnLoaded;
        }

        void OnLoaded(object sender, object e)
        {
            //To let the framework discover the access keys, the elements should be realized
            //on the visual tree. If there are no elements in the visual
            //tree with access key, the framework won't raise the events.
            //In this sample, if you define the Pivot as collapsed on the constructor, the Pivot
            //will have a lazy loading and the access keys won't be enabled.
            //For this reason, we make it visible when creating the object
            //and we collapse it when we load the page.
            MyPivot.Visibility = Visibility.Collapsed;
        }

        void OnAccessKeyInvoked(UIElement sender, AccessKeyInvokedEventArgs args)
        {
            args.Handled = true;
            MyPivot.SelectedItem = sender as PivotItem;
        }
        void OnDisplayModeEnabledChanged(object sender, object e)
        {
            if (AccessKeyManager.IsDisplayModeEnabled)
            {
                MyPivot.Visibility = Visibility.Visible;
            }
            else
            {
                MyPivot.Visibility = Visibility.Collapsed;

            }
        }

        DependencyObject AdjustTarget(UIElement sender)
        {
            DependencyObject target = sender;
            if (sender is PivotItem)
            {
                PivotItem pivotItem = target as PivotItem;
                target = (sender as PivotItem).Header as TextBlock;
            }
            return target;
        }

        void OnAccessKeyDisplayRequested(UIElement sender, AccessKeyDisplayRequestedEventArgs args)
        {
            DependencyObject target = AdjustTarget(sender);
            var tooltip = ToolTipService.GetToolTip(target) as ToolTip;

            if (tooltip == null)
            {
                tooltip = new ToolTip();
                tooltip.Background = new SolidColorBrush(Windows.UI.Colors.Black);
                tooltip.Foreground = new SolidColorBrush(Windows.UI.Colors.White);
                tooltip.Padding = new Thickness(4, 4, 4, 4);
                tooltip.VerticalOffset = -20;
                tooltip.Placement = PlacementMode.Bottom;
                ToolTipService.SetToolTip(target, tooltip);
            }

            if (string.IsNullOrEmpty(args.PressedKeys))
            {
                tooltip.Content = sender.AccessKey;
            }
            else
            {
                tooltip.Content = sender.AccessKey.Remove(0, args.PressedKeys.Length);
            }

            tooltip.IsOpen = true;
        }
        void OnAccessKeyDisplayDismissed(UIElement sender, AccessKeyDisplayDismissedEventArgs args)
        {
            DependencyObject target = AdjustTarget(sender);

            var tooltip = ToolTipService.GetToolTip(target) as ToolTip;
            if (tooltip != null)
            {
                tooltip.IsOpen = false;
                //Fix to avoid show tooltip with mouse
                ToolTipService.SetToolTip(target, null);
            }
        }
    }
```



<!--HONumber=Dec16_HO1-->


