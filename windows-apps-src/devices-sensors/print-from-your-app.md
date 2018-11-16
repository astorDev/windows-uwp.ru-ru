---
author: PatrickFarley
ms.assetid: 9A0F1852-A76B-4F43-ACFC-2CC56AAD1C03
title: Печать из приложения
description: Узнайте, как печатать документы из универсального приложения для Windows. В этом разделе также показано, как печатать отдельные страницы.
ms.author: pafarley
ms.date: 01/29/2018
ms.topic: article
keywords: Windows 10, uwp, печати
ms.localizationpriority: medium
ms.openlocfilehash: b35d11e9dcf1e79296e0eeaff85c975c24d65920
ms.sourcegitcommit: e38b334edb82bf2b1474ba686990f4299b8f59c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6848879"
---
# <a name="print-from-your-app"></a>Печать из приложения



**Важные API**

-   [**Windows.Graphics.Printing**](https://msdn.microsoft.com/library/windows/apps/BR226489)
-   [**Windows.UI.Xaml.Printing**](https://msdn.microsoft.com/library/windows/apps/BR243325)
-   [**PrintDocument**](https://msdn.microsoft.com/library/windows/apps/BR243314)

Узнайте, как печатать документы из универсального приложения для Windows. В этом разделе также показано, как печатать отдельные страницы. Сведения о дополнительных изменениях в пользовательском интерфейсе предварительного просмотра см. в разделе [Настройка пользовательского интерфейса предварительного просмотра](customize-the-print-preview-ui.md).

> [!TIP]
> Большинство примеров в этой статье основано на примере печати. Чтобы увидеть полный код, скачайте [пример печати с использованием универсальной платформы Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkId=619984) из [репозитория Windows-universal-samples](http://go.microsoft.com/fwlink/p/?LinkId=619979) на GitHub.

## <a name="register-for-printing"></a>Регистрация для печати

Первый шаг добавления возможностей печати в ваше приложение — регистрация контракта "Печать". Ваше приложение должно выполнять это на каждом экране, на котором вы хотите предоставить пользователю возможность печати. Для печати можно зарегистрировать только экран, отображаемый для пользователя. Если один экран приложения зарегистрирован для печати, при уходе с этого экрана необходимо отменить его регистрацию для печати. Если экран заменяется другим экраном, при открытии следующего экрана для него необходимо зарегистрировать новый контракт "Печать".

> [!TIP]
> Если вам нужно поддерживать в приложении вывод на печать более одной страницы, вы можете вставить этот код печати в общий вспомогательный класс и позволить страницам приложения использовать его повторно. Пример того, как это сделать, см. в классе `PrintHelper` в [примере печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984).

Сначала объявите [**PrintManager**](https://msdn.microsoft.com/library/windows/apps/BR226426) и [**PrintDocument**](https://msdn.microsoft.com/library/windows/apps/BR243314). Тип **PrintManager** содержится в пространстве имен [**Windows.Graphics.Printing**](https://msdn.microsoft.com/library/windows/apps/BR226489) наряду с типами для поддержки других возможностей печати Windows. Тип **PrintDocument** содержится в пространстве имен [**Windows.UI.Xaml.Printing**](https://msdn.microsoft.com/library/windows/apps/BR243325) наряду с другими типами, поддерживающими подготовку содержимого на XAML для печати. Вы можете упростить создание кода печати, добавив на страницу операторы **using** или **Imports**.

```csharp
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
```

Класс [**PrintDocument**](https://msdn.microsoft.com/library/windows/apps/BR243314) используется для обработки большинства взаимодействий между приложением и [**PrintManager**](https://msdn.microsoft.com/library/windows/apps/BR226426), но предоставляет несколько собственных обратных вызовов. Во время регистрации создайте экземпляры **PrintManager** и **PrintDocument** и зарегистрируйте обработчики для их событий печати.

В [примере печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984) регистрация выполняется с помощью метода `RegisterForPrinting`.

```csharp
public virtual void RegisterForPrinting()
{
   printDocument = new PrintDocument();
   printDocumentSource = printDocument.DocumentSource;
   printDocument.Paginate += CreatePrintPreviewPages;
   printDocument.GetPreviewPage += GetPrintPreviewPage;
   printDocument.AddPages += AddPrintPages;

   PrintManager printMan = PrintManager.GetForCurrentView();
   printMan.PrintTaskRequested += PrintTaskRequested;
}
```

Когда пользователь переходит на страницу, которая поддерживает печать, она инициирует регистрацию в рамках метода `OnNavigatedTo`.

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
   // Initialize common helper class and register for printing
   printHelper = new PrintHelper(this);
   printHelper.RegisterForPrinting();

   // Initialize print content for this scenario
   printHelper.PreparePrintContent(new PageToPrint());

   // Tell the user how to print
   MainPage.Current.NotifyUser("Print contract registered with customization, use the Print button to print.", NotifyType.StatusMessage);
}
```

Когда пользователь покинет страницу, отключите обработчики событий печати. В многостраничном приложении с неотключенной печатью возникает исключение, если пользователь покидает страницу, а затем возвращается к ней.

```csharp
protected override void OnNavigatedFrom(NavigationEventArgs e)
{
   if (printHelper != null)
   {
         printHelper.UnregisterForPrinting();
   }
}
```
## <a name="create-a-print-button"></a>Создание кнопки печати

Добавьте кнопку печати в нужное расположение на экране своего приложения. Убедитесь, что она не пересекается с содержимым, которое вы хотите напечатать.

```xml
<Button x:Name="InvokePrintingButton" Content="Print" Click="OnPrintButtonClick"/>
```

Далее добавьте обработчик событий в код приложения для обработки события нажатия кнопки. Используйте метод [**ShowPrintUIAsync**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing.printmanager.showprintuiasync), чтобы начать печать из приложения. **ShowPrintUIAsync** — асинхронный метод, который отображает соответствующее окно печати. Рекомендуется сначала вызвать метод [**IsSupported**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.Printing.PrintManager.IsSupported), чтобы проверить, что приложение выполняется на устройстве, которое поддерживает печать (и принять меры, если это не так). Если по какой-либо причине выполнить печать в это время невозможно, **ShowPrintUIAsync** создаст исключение. Рекомендуется перехватывать такие исключения, позволяя пользователю узнать, когда выполнение печати невозможно.

```csharp
async private void OnPrintButtonClick(object sender, RoutedEventArgs e)
{
    if (Windows.Graphics.Printing.PrintManager.IsSupported())
    {
        try
        {
            // Show print UI
            await Windows.Graphics.Printing.PrintManager.ShowPrintUIAsync();

        }
        catch
        {
            // Printing cannot proceed at this time
            ContentDialog noPrintingDialog = new ContentDialog()
            {
                Title = "Printing error",
                Content = "\nSorry, printing can' t proceed at this time.", PrimaryButtonText = "OK"
            };
            await noPrintingDialog.ShowAsync();
        }
    }
    else
    {
        // Printing is not supported on this device
        ContentDialog noPrintingDialog = new ContentDialog()
        {
            Title = "Printing not supported",
            Content = "\nSorry, printing is not supported on this device.",PrimaryButtonText = "OK"
        };
        await noPrintingDialog.ShowAsync();
    }
}
```

В этом примере окно печати отображается в обработчике событий для нажатия кнопки. Если метод вызывает исключение (поскольку выполнение печати в это время невозможно), то элемент управления [**ContentDialog**](https://msdn.microsoft.com/library/windows/apps/Dn633972) информирует пользователя о ситуации.

## <a name="format-your-apps-content"></a>Форматирование содержимого приложения

При вызове **ShowPrintUIAsync** порождается событие [**PrintTaskRequested**](https://msdn.microsoft.com/library/windows/apps/br206597). Обработчик событий **PrintTaskRequested**, показанный на данном этапе, создает класс [**PrintTask**](https://msdn.microsoft.com/library/windows/apps/BR226436), вызывая метод [**PrintTaskRequest.CreatePrintTask**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing.printtaskrequest.createprinttask.aspx), и передает заголовок страницы для печати и имя делегата [**PrintTaskSourceRequestedHandler**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing.printtask.source). Обратите внимание, что в этом примере делегат **PrintTaskSourceRequestedHandler** определен как встроенный. Делегат **PrintTaskSourceRequestedHandler** предоставляет отформатированное содержимое для печати и описан далее.

В этом примере также определяется обработчик завершения для перехвата ошибок. Мы рекомендуем обрабатывать события завершения, так как в этом случае ваше приложение сможет сообщать пользователю о возникновении ошибок и предлагать возможные решения. Аналогично ваше приложение может использовать событие завершения, чтобы указать пользователю дальнейшие шаги, которые он может предпринять после успешного завершения печати.

```csharp
protected virtual void PrintTaskRequested(PrintManager sender, PrintTaskRequestedEventArgs e)
{
   PrintTask printTask = null;
   printTask = e.Request.CreatePrintTask("C# Printing SDK Sample", sourceRequested =>
   {
         // Print Task event handler is invoked when the print job is completed.
         printTask.Completed += async (s, args) =>
         {
            // Notify the user when the print operation fails.
            if (args.Completion == PrintTaskCompletion.Failed)
            {
               await scenarioPage.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                     MainPage.Current.NotifyUser("Failed to print.", NotifyType.ErrorMessage);
               });
            }
         };

         sourceRequested.SetSource(printDocumentSource);
   });
}
```

После создания задания печати событие [**PrintManager**](https://msdn.microsoft.com/library/windows/apps/BR226426) запрашивает коллекцию печатаемых страниц для отображения в пользовательском интерфейсе предварительного просмотра и создает событие [**Paginate**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.printing.printdocument.paginate). Это соотносится с методом **Paginate** интерфейса **IPrintPreviewPageCollection**. В этот момент будет вызываться созданный при регистрации обработчик событий.

> [!IMPORTANT]
> При изменении пользователем параметров печати будет повторно вызван обработчик событий разделения на страницы, что позволит вам переформатировать содержимое. Для лучшего взаимодействия с пользователем рекомендуется проверять параметры, прежде чем переформатировать содержимое, и избегать повторной инициализации разбитого на страницы содержимого, если в этом нет необходимости.

В обработчике событий [**Paginate**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.printing.printdocument.paginate) (метод `CreatePrintPreviewPages` в [примере печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984)) создайте страницы для отображения в пользовательском интерфейсе предварительного просмотра и отправки на принтер. Код, используемый для подготовки содержимого вашего приложения к печати, зависит от вашего приложения и печатаемого содержимого. Чтобы увидеть, как приложение форматирует содержимое для печати, см. исходный код в [примере печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984).

```csharp
protected virtual void CreatePrintPreviewPages(object sender, PaginateEventArgs e)
{
   // Clear the cache of preview pages
   printPreviewPages.Clear();

   // Clear the print canvas of preview pages
   PrintCanvas.Children.Clear();

   // This variable keeps track of the last RichTextBlockOverflow element that was added to a page which will be printed
   RichTextBlockOverflow lastRTBOOnPage;

   // Get the PrintTaskOptions
   PrintTaskOptions printingOptions = ((PrintTaskOptions)e.PrintTaskOptions);

   // Get the page description to deterimine how big the page is
   PrintPageDescription pageDescription = printingOptions.GetPageDescription(0);

   // We know there is at least one page to be printed. passing null as the first parameter to
   // AddOnePrintPreviewPage tells the function to add the first page.
   lastRTBOOnPage = AddOnePrintPreviewPage(null, pageDescription);

   // We know there are more pages to be added as long as the last RichTextBoxOverflow added to a print preview
   // page has extra content
   while (lastRTBOOnPage.HasOverflowContent && lastRTBOOnPage.Visibility == Windows.UI.Xaml.Visibility.Visible)
   {
         lastRTBOOnPage = AddOnePrintPreviewPage(lastRTBOOnPage, pageDescription);
   }

   if (PreviewPagesCreated != null)
   {
         PreviewPagesCreated.Invoke(printPreviewPages, null);
   }

   PrintDocument printDoc = (PrintDocument)sender;

   // Report the number of preview pages created
   printDoc.SetPreviewPageCount(printPreviewPages.Count, PreviewPageCountType.Intermediate);
}
```

Если в окне предварительного просмотра требуется отобразить отдельную страницу, [**PrintManager**](https://msdn.microsoft.com/library/windows/apps/BR226426) создает событие [**GetPreviewPage**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.printing.printdocument.getpreviewpage). Это соотносится с методом **MakePage** интерфейса **IPrintPreviewPageCollection**. Созданный при регистрации обработчик событий будет вызываться в это время.

В обработчике событий [**GetPreviewPage**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.printing.printdocument.getpreviewpage) (метод `GetPrintPreviewPage` в [примере печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984)), установите соответствующую страницу для печати.

```csharp
protected virtual void GetPrintPreviewPage(object sender, GetPreviewPageEventArgs e)
{
   PrintDocument printDoc = (PrintDocument)sender;
   printDoc.SetPreviewPage(e.PageNumber, printPreviewPages[e.PageNumber - 1]);
}
```

Наконец, когда пользователь нажимает кнопку печати, класс [**PrintManager**](https://msdn.microsoft.com/library/windows/apps/BR226426) запрашивает конечную коллекцию страниц для отправки на принтер, вызывая метод **MakeDocument** интерфейса **IDocumentPageSource**. В XAML это создает событие [**AddPages**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.printing.printdocument.addpages). Созданный при регистрации обработчик событий будет вызываться в это время.

В обработчике событий [**AddPages**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.printing.printdocument.addpages) (метод `AddPrintPages` в [примере печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984)) добавьте в объект [**PrintDocument**](https://msdn.microsoft.com/library/windows/apps/BR243314) страницы из коллекции страниц для отправки на принтер. Если пользователь указывает конкретные страницы или диапазон страниц для печати, используйте здесь эту информацию, чтобы на принтер отправлялись только те страницы, которые указаны.

```csharp
protected virtual void AddPrintPages(object sender, AddPagesEventArgs e)
{
   // Loop over all of the preview pages and add each one to  add each page to be printied
   for (int i = 0; i < printPreviewPages.Count; i++)
   {
         // We should have all pages ready at this point...
         printDocument.AddPage(printPreviewPages[i]);
   }

   PrintDocument printDoc = (PrintDocument)sender;

   // Indicate that all of the print pages have been provided
   printDoc.AddPagesComplete();
}
```

## <a name="prepare-print-options"></a>Подготовка параметров печати

Затем подготовьте параметры печати. В качестве примера в этом разделе описывается указание параметра диапазона страниц для печати определенных страниц. Сведения о дополнительных параметрах см. в разделе [Настройка пользовательского интерфейса предварительного просмотра](customize-the-print-preview-ui.md).

На этом этапе создается новый параметр печати, определяется список значений, поддерживаемых параметром, а затем добавляется параметр к пользовательскому интерфейсу предварительного просмотра. Параметр диапазона страниц имеет следующие настройки.

| Имя параметра          | Действие |
|----------------------|--------|
| **Печатать все**        | Печать всех страниц в документе.|
| **Напечатать выделенный фрагмент**  | Печатать только содержимое, выбранное пользователем.|
| **Диапазон печати**      | Отображение поля ввода, в которое пользователь может ввести страницы для печати.|

Для начала измените обработчик событий [**PrintTaskRequested**](https://msdn.microsoft.com/library/windows/apps/br206597), добавив код для получения объекта [**PrintTaskOptionDetails**](https://msdn.microsoft.com/library/windows/apps/Hh701256).

```csharp
PrintTaskOptionDetails printDetailedOptions = PrintTaskOptionDetails.GetFromPrintTaskOptions(printTask.Options);
```

Очистите список параметров, отображающихся в пользовательском интерфейсе предварительного просмотра печати, и добавьте параметры, которые вы хотите показывать, когда пользователь начинает печать из приложения.

> [!NOTE]
> Эти параметры появляются в пользовательском интерфейсе предварительного просмотра в порядке их добавления— первый параметр отображается вверху окна.

```csharp
IList<string> displayedOptions = printDetailedOptions.DisplayedOptions;

displayedOptions.Clear();
displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.Copies);
displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.Orientation);
displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.ColorMode);
```

Создайте новый параметр печати и инициализируйте список значений параметров.

```csharp
// Create a new list option
PrintCustomItemListOptionDetails pageFormat = printDetailedOptions.CreateItemListOption("PageRange", "Page Range");
pageFormat.AddItem("PrintAll", "Print all");
pageFormat.AddItem("PrintSelection", "Print Selection");
pageFormat.AddItem("PrintRange", "Print Range");
```

Добавьте ваш пользовательский параметр печати и назначьте обработчик событий. Пользовательский параметр добавляется последним, так что он появляется в нижней части списка параметров. Однако его можно поместить в любое место списка; необязательно добавлять пользовательские параметры печати последними.

```csharp
// Add the custom option to the option list
displayedOptions.Add("PageRange");

// Create new edit option
PrintCustomTextOptionDetails pageRangeEdit = printDetailedOptions.CreateTextOption("PageRangeEdit", "Range");

// Register the handler for the option change event
printDetailedOptions.OptionChanged += printDetailedOptions_OptionChanged;
```

Метод [**CreateTextOption**](https://msdn.microsoft.com/library/windows/apps/windows.graphics.printing.optiondetails.printtaskoptiondetails.createtextoption) создает текстовое поле **Диапазон**. В это текстовое поле пользователь вводит определенные страницы, которые необходимо распечатать при выборе параметра **Диапазон печати**.

## <a name="handle-print-option-changes"></a>Обработка изменений параметров печати

Обработчик события **OptionChanged** выполняет две процедуры. Во-первых, он показывает и скрывает поле текстового ввода диапазона страниц в зависимости от параметра диапазона печати, выбранного пользователем. Во-вторых, он проверяет, является ли текст, введенный в поле диапазона страниц, допустимым диапазоном страниц для документа.

В этом примере показано, как [пример печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984) обрабатывает события изменений.

```csharp
async void printDetailedOptions_OptionChanged(PrintTaskOptionDetails sender, PrintTaskOptionChangedEventArgs args)
{
   if (args.OptionId == null)
   {
         return;
   }

   string optionId = args.OptionId.ToString();

   // Handle change in Page Range Option
   if (optionId == "PageRange")
   {
         IPrintOptionDetails pageRange = sender.Options[optionId];
         string pageRangeValue = pageRange.Value.ToString();

         selectionMode = false;

         switch (pageRangeValue)
         {
            case "PrintRange":
               // Add PageRangeEdit custom option to the option list
               sender.DisplayedOptions.Add("PageRangeEdit");
               pageRangeEditVisible = true;
               await scenarioPage.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                     ShowContent(null);
               });
               break;
            case "PrintSelection":
               await scenarioPage.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                     Scenario4PageRange page = (Scenario4PageRange)scenarioPage;
                     PageToPrint pageContent = (PageToPrint)page.PrintFrame.Content;
                     ShowContent(pageContent.TextContentBlock.SelectedText);
               });
               RemovePageRangeEdit(sender);
               break;
            default:
               await scenarioPage.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                     ShowContent(null);
               });
               RemovePageRangeEdit(sender);
               break;
         }

         Refresh();
   }
   else if (optionId == "PageRangeEdit")
   {
         IPrintOptionDetails pageRange = sender.Options[optionId];
         // Expected range format (p1,p2...)*, (p3-p9)* ...
         if (!Regex.IsMatch(pageRange.Value.ToString(), @"^\s*\d+\s*(\-\s*\d+\s*)?(\,\s*\d+\s*(\-\s*\d+\s*)?)*$"))
         {
            pageRange.ErrorText = "Invalid Page Range (eg: 1-3, 5)";
         }
         else
         {
            pageRange.ErrorText = string.Empty;
            try
            {
               GetPagesInRange(pageRange.Value.ToString());
               Refresh();
            }
            catch (InvalidPageException ipex)
            {
               pageRange.ErrorText = ipex.Message;
            }
         }
   }
}
```

> [!TIP]
> См. дополнительные сведения по анализу диапазона страниц, которые пользователь вводит в текстовом поле "Диапазон", в методе `GetPagesInRange` в [примере печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984).

## <a name="preview-selected-pages"></a>Предварительный просмотр выбранных страниц

Способ форматирования содержимого вашего приложения для печати зависит от характера приложения и его содержимого. В [примере печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984) используется вспомогательный класс печати, форматирующий содержимое для печати.

При печати подмножества страниц существует несколько способов показать содержимое в предварительном просмотре. Вне зависимости от выбранного вами метода отображения диапазона страниц в предварительном просмотре напечатанное содержимое должно содержать только выбранные страницы.

-   Отобразите в предварительном просмотре все страницы вне зависимости от того, указан диапазон страниц или нет, и покажите пользователю, какие именно страницы будут напечатаны.
-   Отобразите в предварительном просмотре только страницы выбранного пользователем диапазона, обновляя представление, если пользователь изменит диапазон страниц.
-   Отобразите в предварительном просмотре все страницы, но сделайте неактивными те из них, которые не входят в выбранный пользователем диапазон страниц.

## <a name="related-topics"></a>Еще по теме

* [Руководство по проектированию печати](https://msdn.microsoft.com/library/windows/apps/Hh868178)
* [//Видео c конференции Build 2015: разработка приложений для печати в Windows10](https://channel9.msdn.com/Events/Build/2015/2-94)
* [Пример печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984)
