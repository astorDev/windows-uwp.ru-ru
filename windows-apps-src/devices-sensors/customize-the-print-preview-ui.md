---
ms.assetid: 88132B6F-FB50-4B03-BC21-233988746230
title: Настройка пользовательского интерфейса предварительного просмотра
description: В этом разделе описывается настройка параметров печати в пользовательском интерфейсе предварительного просмотра.
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, печать
ms.localizationpriority: medium
ms.openlocfilehash: 000985d5f9dac5363a1ea2fb002c2be40e2777dd
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258657"
---
# <a name="customize-the-print-preview-ui"></a>Настройка пользовательского интерфейса предварительного просмотра



**Важные API**

-   [**Windows. Graphics. Printing**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing)
-   [**Windows. UI. XAML. печать**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Printing)
-   [**принтманажер**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing.PrintManager)

В этом разделе описывается настройка параметров печати в пользовательском интерфейсе предварительного просмотра. Дополнительные сведения о печати см. в разделе [Печать из приложения](print-from-your-app.md).

**Совет**  большинство примеров в этом разделе основаны на примере печати. Чтобы увидеть полный код, скачайте [пример печати с использованием универсальной платформы Windows (UWP)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing) из [репозитория Windows-universal-samples](https://github.com/Microsoft/Windows-universal-samples) на GitHub.

 

## <a name="customize-print-options"></a>Настройка параметров печати

По умолчанию в пользовательском интерфейсе предварительного просмотра отображаются параметры печати [**ColorMode**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.colormode), [**Copies**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.copies) и [**Orientation**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.orientation). Кроме них существует несколько других распространенных параметров принтера, которые можно добавить в пользовательский интерфейс предварительного просмотра:

-   [**Вязывания**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.binding)
-   [**Параметры сортировки**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.collation)
-   [**Устройства**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.duplex)
-   [**холепунч**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.holepunch)
-   [**инпутбин**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.inputbin)
-   [**медиасизе**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.mediasize)
-   [**Мультимедиа**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.mediatype)
-   [**Чистки**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.nup)
-   [**принткуалити**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.printquality)
-   [**Основой**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.staple)

Эти параметры определены в классе [**StandardPrintTaskOptions**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing.StandardPrintTaskOptions). Вы можете добавить или удалить параметры из списка, отображаемого в пользовательском интерфейсе предварительного просмотра. Можно также менять порядок отображения параметров и задавать значения по умолчанию, которые будут показаны пользователю.

Но изменения, внесенные этим способом, повлияют только на пользовательский интерфейс предварительного просмотра. Пользователь всегда сможет получить доступ ко всем поддерживаемым принтером параметрам посредством пункта **Дополнительные параметры** в пользовательском интерфейсе предварительного просмотра.

**Примечание**  несмотря на то, что приложение может указывать любые параметры печати для отображения, в пользовательском интерфейсе предварительного просмотра отображаются только те, которые поддерживаются выбранным принтером. Параметры, не поддерживаемые выбранным принтером, не будут отображаться в пользовательском интерфейсе печати.

 

### <a name="define-the-options-to-display"></a>Определение параметров для отображения

При загрузке экрана приложения выполняется регистрация для контракта «Печать». Часть этой регистрации включает в себя определение обработчика событий [**PrintTaskRequested**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperationWithProgress_TResult_TProgress_#Windows_Foundation_IAsyncOperationWithProgress_2_Progress). Код для настройки параметров, отображаемых в пользовательском интерфейсе предварительного просмотра, добавляется в обработчик событий **PrintTaskRequested**.

Измените обработчик событий [**PrintTaskRequested**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperationWithProgress_TResult_TProgress_#Windows_Foundation_IAsyncOperationWithProgress_2_Progress), чтобы включить инструкции [**printTask.options**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.printtask.options), предназначенные для настройки параметров печати, которые должны отображаться в пользовательском интерфейсе предварительного просмотра. Переопределите обработчик событий **PrintTaskRequested** в дополнительном классе экрана, на котором требуется отображать пользовательский список параметров печати, чтобы включить в него код для определения этих параметров.

``` csharp
protected override void PrintTaskRequested(PrintManager sender, PrintTaskRequestedEventArgs e)
{
   PrintTask printTask = null;
   printTask = e.Request.CreatePrintTask("C# Printing SDK Sample", sourceRequestedArgs =>
   {
         IList<string> displayedOptions = printTask.Options.DisplayedOptions;

         // Choose the printer options to be shown.
         // The order in which the options are appended determines the order in which they appear in the UI
         displayedOptions.Clear();
         displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.Copies);
         displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.Orientation);
         displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.MediaSize);
         displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.Collation);
         displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.Duplex);

         // Preset the default value of the printer option
         printTask.Options.MediaSize = PrintMediaSize.NorthAmericaLegal;

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

         sourceRequestedArgs.SetSource(printDocumentSource);
   });
}
```

**Важно** .  вызов [**дисплайедоптионс. Clear**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.printtaskoptions.displayedoptions)() удаляет все параметры печати из пользовательского интерфейса предварительного просмотра, включая ссылку " **другие параметры** ". Не забудьте добавить параметры, которые требуется показывать в пользовательском интерфейсе предварительного просмотра.

### <a name="specify-default-options"></a>Определение параметров по умолчанию

Можно также задать значения по умолчанию для параметров в пользовательском интерфейсе предварительного просмотра. В следующей строке кода из предыдущего примера устанавливается значение по умолчанию для параметра [**MediaSize**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.standardprinttaskoptions.mediasize):

``` csharp
         // Preset the default value of the printer option
         printTask.Options.MediaSize = PrintMediaSize.NorthAmericaLegal;
```         

## <a name="add-new-print-options"></a>Добавление новых параметров печати

В этом разделе показано, как создать новый параметр печати, определить список значений, поддерживаемых параметром, а затем добавить параметр к пользовательскому интерфейсу предварительного просмотра. Добавьте новый параметр печати в обработчик событий [**PrintTaskRequested**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperationWithProgress_TResult_TProgress_#Windows_Foundation_IAsyncOperationWithProgress_2_Progress), как и в предыдущем разделе.

Сначала получите объект [**PrintTaskOptionDetails**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing.OptionDetails.PrintTaskOptionDetails). Он используется для добавления нового параметра печати к пользовательскому интерфейсу предварительного просмотра. Затем очистите список параметров, отображающихся в пользовательском интерфейсе предварительного просмотра, и добавьте параметры, которые вы хотите показывать пользователю при печати из приложения. После этого создайте новый параметр печати и инициализируйте список значений параметров. Наконец, добавьте новый параметр и назначьте обработчик события **OptionChanged**.

``` csharp
protected override void PrintTaskRequested(PrintManager sender, PrintTaskRequestedEventArgs e)
{
   PrintTask printTask = null;
   printTask = e.Request.CreatePrintTask("C# Printing SDK Sample", sourceRequestedArgs =>
   {
         PrintTaskOptionDetails printDetailedOptions = PrintTaskOptionDetails.GetFromPrintTaskOptions(printTask.Options);
         IList<string> displayedOptions = printDetailedOptions.DisplayedOptions;

         // Choose the printer options to be shown.
         // The order in which the options are appended determines the order in which they appear in the UI
         displayedOptions.Clear();

         displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.Copies);
         displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.Orientation);
         displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.ColorMode);

         // Create a new list option
         PrintCustomItemListOptionDetails pageFormat = printDetailedOptions.CreateItemListOption("PageContent", "Pictures");
         pageFormat.AddItem("PicturesText", "Pictures and text");
         pageFormat.AddItem("PicturesOnly", "Pictures only");
         pageFormat.AddItem("TextOnly", "Text only");

         // Add the custom option to the option list
         displayedOptions.Add("PageContent");

         printDetailedOptions.OptionChanged += printDetailedOptions_OptionChanged;

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

         sourceRequestedArgs.SetSource(printDocumentSource);
   });
}
```

Эти параметры появляются в пользовательском интерфейсе предварительного просмотра в порядке их добавления — первый параметр отображается вверху окна. В данном примере настраиваемый параметр добавляется последним, поэтому он отображается в нижней части списка параметров. Однако его можно поместить в любое место списка; необязательно добавлять настраиваемые параметры печати последними.

Когда пользователь изменяет выбранную настройку в вашем настраиваемом параметре, нужно обновить изображение предварительного просмотра. Вызовите метод [**InvalidatePreview**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.printing.printdocument.invalidatepreview) для перерисовки изображения в пользовательском интерфейсе предварительного просмотра, как показано ниже.

``` csharp
async void printDetailedOptions_OptionChanged(PrintTaskOptionDetails sender, PrintTaskOptionChangedEventArgs args)
{
   // Listen for PageContent changes
   string optionId = args.OptionId as string;
   if (string.IsNullOrEmpty(optionId))
   {
         return;
   }

   if (optionId == "PageContent")
   {
         await scenarioPage.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
         {
            printDocument.InvalidatePreview();
         });
   }
}
```

## <a name="related-topics"></a>Статьи по теме

* [Рекомендации по проектированию для печати](https://docs.microsoft.com/windows/uwp/devices-sensors/printing-and-scanning)
* [Сборка видео 2015: Разработка приложений, которые печатаются в Windows 10](https://channel9.msdn.com/Events/Build/2015/2-94)
* [Пример печати UWP](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing)
