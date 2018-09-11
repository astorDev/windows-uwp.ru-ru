---
author: PatrickFarley
ms.assetid: 88132B6F-FB50-4B03-BC21-233988746230
title: Настройка пользовательского интерфейса предварительного просмотра
description: В этом разделе описывается настройка параметров печати в пользовательском интерфейсе предварительного просмотра.
ms.author: pafarley
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, печать
ms.localizationpriority: medium
ms.openlocfilehash: fe4086cc87699083304594eb4ccc8e7bb137b19f
ms.sourcegitcommit: 72710baeee8c898b5ab77ceb66d884eaa9db4cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "3851024"
---
# <a name="customize-the-print-preview-ui"></a>Настройка пользовательского интерфейса предварительного просмотра



**Важные API**

-   [**Windows.Graphics.Printing**](https://msdn.microsoft.com/library/windows/apps/BR226489)
-   [**Windows.UI.Xaml.Printing**](https://msdn.microsoft.com/library/windows/apps/BR243325)
-   [**PrintManager**](https://msdn.microsoft.com/library/windows/apps/BR226426)

В этом разделе описывается настройка параметров печати в пользовательском интерфейсе предварительного просмотра. Дополнительные сведения о печати см. в разделе [Печать из приложения](print-from-your-app.md).

**Подсказка**. Большинство примеров в этой статье основаны на примере печати. Чтобы увидеть полный код, скачайте [пример печати в универсальной платформе Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkId=619984) из [репозитория Windows-universal-samples](http://go.microsoft.com/fwlink/p/?LinkId=619979) на GitHub.

 

## <a name="customize-print-options"></a>Настройка параметров печати

По умолчанию в пользовательском интерфейсе предварительного просмотра отображаются параметры печати [**ColorMode**](https://msdn.microsoft.com/library/windows/apps/BR226478), [**Copies**](https://msdn.microsoft.com/library/windows/apps/BR226479) и [**Orientation**](https://msdn.microsoft.com/library/windows/apps/BR226486). Кроме них существует несколько других распространенных параметров принтера, которые можно добавить в пользовательский интерфейс предварительного просмотра:

-   [**Binding**](https://msdn.microsoft.com/library/windows/apps/BR226476)
-   [**Collation**](https://msdn.microsoft.com/library/windows/apps/BR226477)
-   [**Duplex**](https://msdn.microsoft.com/library/windows/apps/BR226480)
-   [**HolePunch**](https://msdn.microsoft.com/library/windows/apps/BR226481)
-   [**InputBin**](https://msdn.microsoft.com/library/windows/apps/BR226482)
-   [**MediaSize**](https://msdn.microsoft.com/library/windows/apps/BR226483)
-   [**MediaType**](https://msdn.microsoft.com/library/windows/apps/BR226484)
-   [**NUp**](https://msdn.microsoft.com/library/windows/apps/BR226485)
-   [**PrintQuality**](https://msdn.microsoft.com/library/windows/apps/BR226487)
-   [**Staple**](https://msdn.microsoft.com/library/windows/apps/BR226488)

Эти параметры определены в классе [**StandardPrintTaskOptions**](https://msdn.microsoft.com/library/windows/apps/BR226475). Вы можете добавить или удалить параметры из списка, отображаемого в пользовательском интерфейсе предварительного просмотра. Можно также менять порядок отображения параметров и задавать значения по умолчанию, которые будут показаны пользователю.

Но изменения, внесенные этим способом, повлияют только на пользовательский интерфейс предварительного просмотра. Пользователь всегда сможет получить доступ ко всем поддерживаемым принтером параметрам посредством пункта **Дополнительные параметры** в пользовательском интерфейсе предварительного просмотра.

**Примечание.** Несмотря на то что в приложении можно указать любые параметры печати для отображения, в пользовательском интерфейсе предварительного просмотра будут показаны только те из них, которые поддерживаются выбранным принтером. Параметры, не поддерживаемые выбранным принтером, не будут отображаться в пользовательском интерфейсе печати.

 

### <a name="define-the-options-to-display"></a>Определение параметров для отображения

При загрузке экрана приложения выполняется регистрация для контракта «Печать». Часть этой регистрации включает в себя определение обработчика событий [**PrintTaskRequested**](https://msdn.microsoft.com/library/windows/apps/br206597). Код для настройки параметров, отображаемых в пользовательском интерфейсе предварительного просмотра, добавляется в обработчик событий **PrintTaskRequested**.

Измените обработчик событий [**PrintTaskRequested**](https://msdn.microsoft.com/library/windows/apps/br206597), чтобы включить инструкции [**printTask.options**](https://msdn.microsoft.com/library/windows/apps/BR226469), предназначенные для настройки параметров печати, которые должны отображаться в пользовательском интерфейсе предварительного просмотра. Переопределите обработчик событий **PrintTaskRequested** в дополнительном классе экрана, на котором требуется отображать пользовательский список параметров печати, чтобы включить в него код для определения этих параметров.

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

**Важно!** Вызов метода [**displayedOptions.clear**](https://msdn.microsoft.com/library/windows/apps/BR226453)() удаляет все параметры печати из пользовательского интерфейса предварительного просмотра, включая ссылку **Дополнительные параметры**. Не забудьте добавить параметры, которые требуется показывать в пользовательском интерфейсе предварительного просмотра.

### <a name="specify-default-options"></a>Определение параметров по умолчанию

Можно также задать значения по умолчанию для параметров в пользовательском интерфейсе предварительного просмотра. В следующей строке кода из предыдущего примера устанавливается значение по умолчанию для параметра [**MediaSize**](https://msdn.microsoft.com/library/windows/apps/BR226483):

``` csharp
         // Preset the default value of the printer option
         printTask.Options.MediaSize = PrintMediaSize.NorthAmericaLegal;
```         

## <a name="add-new-print-options"></a>Добавление новых параметров печати

В этом разделе показано, как создать новый параметр печати, определить список значений, поддерживаемых параметром, а затем добавить параметр к пользовательскому интерфейсу предварительного просмотра. Добавьте новый параметр печати в обработчик событий [**PrintTaskRequested**](https://msdn.microsoft.com/library/windows/apps/br206597), как и в предыдущем разделе.

Сначала получите объект [**PrintTaskOptionDetails**](https://msdn.microsoft.com/library/windows/apps/Hh701256). Он используется для добавления нового параметра печати к пользовательскому интерфейсу предварительного просмотра. Затем очистите список параметров, отображающихся в пользовательском интерфейсе предварительного просмотра, и добавьте параметры, которые вы хотите показывать пользователю при печати из приложения. После этого создайте новый параметр печати и инициализируйте список значений параметров. Наконец, добавьте новый параметр и назначьте обработчик события **OptionChanged**.

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

Эти параметры появляются в пользовательском интерфейсе предварительного просмотра в порядке их добавления— первый параметр отображается вверху окна. В данном примере настраиваемый параметр добавляется последним, поэтому он отображается в нижней части списка параметров. Однако его можно поместить в любое место списка; необязательно добавлять настраиваемые параметры печати последними.

Когда пользователь изменяет выбранную настройку в вашем настраиваемом параметре, нужно обновить изображение предварительного просмотра. Вызовите метод [**InvalidatePreview**](https://msdn.microsoft.com/library/windows/apps/Hh702146) для перерисовки изображения в пользовательском интерфейсе предварительного просмотра, как показано ниже.

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

## <a name="related-topics"></a>Ссылки по теме

* [Руководство по проектированию печати](https://msdn.microsoft.com/library/windows/apps/Hh868178)
* [//Видео c конференции Build 2015: разработка приложений для печати в Windows10](https://channel9.msdn.com/Events/Build/2015/2-94)
* [Пример печати в UWP](http://go.microsoft.com/fwlink/p/?LinkId=619984)
