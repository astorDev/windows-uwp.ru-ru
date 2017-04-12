---
author: msatranjr
title: "Создание событий в компонентах среды выполнения Windows"
ms.assetid: 3F7744E8-8A3C-4203-A1CE-B18584E89000
description: 
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: abd46f788c8947da4c8704baf0f0ea802fff38ef
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="raising-events-in-windows-runtime-components"></a>Создание событий в компонентах среды выполнения Windows


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Если ваш компонент среды выполнения Windows вызывает событие типа делегата, определенного пользователем, в фоновом (рабочем) потоке и вы хотите, чтобы среда JavaScript могла получить его, можно реализовать и вызвать событие одним из следующих способов.

-   Вариант 1: создать событие с помощью [Windows.UI.Core.CoreDispatcher](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.aspx), чтобы маршалировать его в контекст потока JavaScript. Хотя обычно это оптимальный вариант, в некоторых случаях он не обеспечивает максимальную производительность.
-   Вариант 2: использовать [Windows.Foundation.EventHandler ](https://msdn.microsoft.com/library/windows/apps/br206577.aspx)&lt;Object&gt;, но с потерей сведений о типе события. Если вариант 1 не приемлем или его производительности недостаточно, то это хороший метод, если потеря сведений о типе не критична для вас.
-   Вариант 3: создать собственный прокси и заглушку для компонента. Этот вариант сложнее всего реализовать, но он сохраняет сведения о типе и может обеспечивать более высокую, чем вариант 1, производительность для ресурсоемких сценариев.

Если создать событие в фоновом потоке без использования одного из этих вариантов, клиент JavaScript просто не получит его.

## <a name="background"></a>Общие сведения


Все компоненты и приложения среды выполнения Windows — это COM-объекты независимо от того, какой язык используется для их создания. В API Windows большая часть компонентов представляет собой динамичные COM-объекты, способные одинаково хорошо взаимодействовать с объектами в фоновом потоке и в потоке пользовательского интерфейса. Если COM-объект не может быть динамичным, ему требуются вспомогательные объекты, прокси и заглушки, для связи с другими COM-объектами в потоке пользовательского интерфейса и фоновом потоке. (С точки зрения модели COM такую связь называют связью между подразделениями потока.)

Большинство объектов в API Windows динамичные или содержат встроенные прокси и заглушки. Однако прокси и заглушки невозможно создать для универсальных типов, таких как Windows.Foundation.[TypedEventHandler&lt;TSender, TResult&gt;](https://msdn.microsoft.com/library/windows/apps/br225997.aspx), поскольку они не представляют собой полные типы, если не указать аргумент типа. Отсутствие прокси и заглушек становится проблемой только для клиентов JavaScript, но если вы хотите, чтобы ваш компонент можно было использовать из JavaScript так же, как из C++ или языка .NET, необходимо использовать один из следующих трех вариантов.

## <a name="option-1-raise-the-event-through-the-coredispatcher"></a>Вариант 1: создание события с помощью CoreDispatcher


Вы можете отправлять события любого типа делегата, определяемого пользователем, с помощью [Windows.UI.Core.CoreDispatcher](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.aspx), и JavaScript получит их. Если вы не знаете точно, какой вариант использовать, сначала попробуйте этот. Если задержка между срабатыванием события и его обработкой становится проблемой, попробуйте один из других вариантов.

В следующем примере показано, как с помощью CoreDispatcher создать строго типизированное событие. Обратите внимание, что аргумент типа — Toast, а не Object.

```csharp
public event EventHandler<Toast> ToastCompletedEvent;
private void OnToastCompleted(Toast args)
{
    var completedEvent = ToastCompletedEvent;
    if (completedEvent != null)
    {
        completedEvent(this, args);
    }
}

public void MakeToastWithDispatcher(string message)
{
    Toast toast = new Toast(message);
    // Assume you have a CoreDispatcher at class scope.
    // Initialize it here, then use it from the background thread.
    var window = Windows.UI.Core.CoreWindow.GetForCurrentThread();
    m_dispatcher = window.Dispatcher;

    Task.Run( () =>
    {
        if (ToastCompletedEvent != null)
        {
            m_dispatcher.RunAsync(CoreDispatcherPriority.Normal,
            new DispatchedHandler(() =>
            {
                this.OnToastCompleted(toast);
            })); // end m_dispatcher.RunAsync
         }
     }); // end Task.Run
}
```

## <a name="option-2-use-eventhandlerltobjectgt-but-lose-type-information"></a>Вариант 2: использование EventHandler&lt;Object&gt;, но с потерей сведений о типе 


Другой способ отправлять события из фонового потока — использовать [Windows.Foundation.EventHandler](https://msdn.microsoft.com/library/windows/apps/br206577.aspx)&lt;Object&gt; в качестве типа события. Windows предоставляет этот экземпляр универсального типа, а также прокси и заглушку для него. Недостаток состоит в том, что сведения о типе аргументов и отправителя события теряются. Клиенты C++ и .NET должны знать, в какой тип следует преобразовать экземпляр при получении события. Эти сведения берутся из документации. Клиентам JavaScript не нужна информация об исходном типе. Они находят свойства аргументов на основе их имен в метаданных.

В этом примере показано, как использовать Windows.Foundation.EventHandler&lt;Object&gt; на языке C#:

```csharp
public sealed Class1
{
// Declare the event
public event EventHandler<Object> ToastCompletedEvent;

    // Raise the event
    public async void MakeToast(string message)
    {
        Toast toast = new Toast(message);
        // Fire the event from a background thread to allow this thread to continue
        Task.Run(() =>
        {
            if (ToastCompletedEvent != null)
            {
                OnToastCompleted(toast);
            }
        });
    }

    private void OnToastCompleted(Toast args)
    {
        var completedEvent = ToastCompletedEvent;
        if (completedEvent != null)
        {
            completedEvent(this, args);
        }
    }
}
```

Это событие используется в части JavaScript следующим образом:

```javascript
toastCompletedEventHandler: function (event) {
   var toastType = event.toast.toastType;
   document.getElementById("toasterOutput").innerHTML = "<p>Made " + toastType + " toast</p>";
}
```

## <a name="option-3-create-your-own-proxy-and-stub"></a>Вариант 3: создание собственных прокси и заглушки


Для повышения производительности с определяемыми пользователем типами событий, которые полностью сохраняют сведения о типе, необходимо создать собственные объекты прокси и заглушки и внедрить их в пакет приложения. Обычно этот вариант следует использовать только в тех редких случаях, когда ни один из других вариантов не пригоден. Кроме того, нет гарантии, что этот метод обеспечит лучшую производительность, чем остальные. Фактическая производительность зависит от множества факторов. Используйте профилировщик Visual Studio или другие средства профилирования для измерения фактической производительности в приложении и определите, действительно ли событие — это "узкое место".

Далее в этой статье показано, как использовать C# для создания простого компонента среды выполнения Windows, а затем использовать C++ для создания библиотеки DLL для прокси и заглушки, которые позволят JavaScript использовать событие Windows.Foundation.TypedEventHandler&lt;TSender, TResult&gt;, вызываемое компонентом в асинхронной операции. (Вы также можете использовать C++ или Visual Basic для создания компонента. Действия, относящиеся к созданию прокси и заглушек, аналогичны.) Это пошаговое руководство основано на примере создания внутрипроцессного компонента среды выполнения Windows (C++/CX) и объясняет его назначение.

Пошаговое руководство содержит следующие компоненты:

-   Здесь вы создаете два базовых класса среды выполнения Windows. Один класс предоставляет событие типа [Windows.Foundation.TypedEventHandler&lt;TSender, TResult&gt;](https://msdn.microsoft.com/library/windows/apps/br225997.aspx), а другой класс — это тип, возвращаемый среде JavaScript в качестве аргумента TValue. Эти классы не могут взаимодействовать с JavaScript, если не выполнить последующие действия.
-   Это приложение активирует объект основного класса, вызывает метод и обрабатывает событие, вызываемое компонентом среды выполнения Windows.
-   Это необходимо для средств, создающих классы прокси и заглушки.
-   Затем вы используете файл IDL для генерации исходного кода C для прокси и заглушки.
-   Зарегистрируйте объекты прокси и заглушки, чтобы среда выполнения COM могла их найти, и добавьте ссылку на соответствующую библиотеку DLL в проект приложения.

## <a name="to-create-the-windows-runtime-component"></a>Создание компонента среды выполнения Windows

В Visual Studio в строке меню выберите **Файл &gt;Создатьпроект**. В диалоговом окне **Создание проекта** разверните узел **JavaScript &gt; Универсальное приложение для Windows** и выберите **Пустое приложение**. Назовите проект ToasterApplication и нажмите кнопку **ОК** .

Добавьте компонент среды выполнения Windows на C# в решение: в обозревателе решений откройте контекстное меню для нужного решения и выберите **Добавить &gt; Новый проект**. Разверните узел **Visual C# &gt; Магазин Windows** и выберите пункт **Компонент среды выполнения Windows**. Назовите проект ToasterComponent и нажмите кнопку **ОК**. ToasterComponent будет корневым пространством имен для компонентов, которые вы создадите позднее.

В обозревателе решений откройте контекстное меню решения и выберите пункт **Свойства**. В диалоговом окне **Страницы свойств** выберите элемент **Свойства конфигурации** в левой области, а затем в верхней части диалогового окна задайте для параметра **Конфигурация** значение **Отладка**, а для параметра **Платформа** выберите "x86", "x64" или "ARM". Нажмите кнопку **ОК**.

**Важно!** Значение "Любой ЦП" не подойдет, поскольку оно недопустимо для библиотеки DLL с собственным кодом Win32, которую вы добавите в решение позже.

В обозревателе решений переименуйте файл class1.cs на ToasterComponent.cs, чтобы он соответствовал имени проекта. Visual Studio автоматически переименует класс в файле.

Добавьте в CS-файл директиву using для пространства имен Windows.Foundation, чтобы добавить TypedEventHandler в область действия.

Если вам необходимы прокси и заглушки, компонент должен использовать интерфейсы, чтобы предоставлять доступ к открытым членам. В файле ToasterComponent.cs укажите интерфейс для тостера и интерфейс для всплывающего уведомления, которое создает тостер.

**Примечание.** Для C# этот шаг можно пропустить. Вместо этого сначала создайте класс, а затем откройте его контекстное меню и выберите **Рефакторинг &gt; Извлечь интерфейс**. В созданном коде вручную сделайте интерфейсы общедоступными.

```csharp
    public interface IToaster
        {
            void MakeToast(String message);
            event TypedEventHandler<Toaster, Toast> ToastCompletedEvent;

        }
        public interface IToast
        {
            String ToastType { get; }
        }
```

Интерфейс IToast содержит строку, которую можно извлечь для описания типа всплывающего уведомления. Интерфейс IToaster содержит метод для создания всплывающего уведомления и событие для уведомления об этом. Поскольку это событие возвращает определенный элемент (т. е. тип) всплывающего уведомления, его называют типизированным событием.

Затем нам понадобятся открытые классы, реализующие эти интерфейсы, которые будут доступны из приложения JavaScript, созданного позже.

```csharp
    public sealed class Toast : IToast
        {
            private string _toastType;

            public string ToastType
            {
                get
                {
                    return _toastType;
                }
            }
            internal Toast(String toastType)
            {
                _toastType = toastType;
            }

        }
        public sealed class Toaster : IToaster
        {
            public event TypedEventHandler<Toaster, Toast> ToastCompletedEvent;

            private void OnToastCompleted(Toast args)
            {
                var completedEvent = ToastCompletedEvent;
                if (completedEvent != null)
                {
                    completedEvent(this, args);
                }
            }

            public void MakeToast(string message)
            {
                Toast toast = new Toast(message);
                // Fire the event from a thread-pool thread to enable this thread to continue
                Windows.System.Threading.ThreadPool.RunAsync(
                (IAsyncAction action) =>
                {
                    if (ToastCompletedEvent != null)
                    {
                        OnToastCompleted(toast);
                    }
                });
           }
        }
```

В предыдущем фрагменте кода мы создаем всплывающее уведомление, а затем выполняем операции с пулом потоков для вызова уведомления. Хотя среда IDE может порекомендовать применить ключевое слово await в асинхронном вызове, в данном случае это необязательно, поскольку метод не выполняет никаких действий, которые зависят от результатов операции.

**Примечание.** Асинхронный вызов в предыдущем коде использует метод ThreadPool.RunAsync только для демонстрации простого способа активации события в фоновом потоке. Вы можете написать этот метод, как показано в следующем примере, и он будет работать, так как планировщик заданий .NET автоматически маршалирует асинхронные вызовы и вызовы await в поток пользовательского интерфейса.
  
````csharp
    public async void MakeToast(string message)
    {
        Toast toast = new Toast(message)
        await Task.Delay(new Random().Next(1000));
        OnToastCompleted(toast);
    }
```

If you build the project now, it should build cleanly.

## To program the JavaScript app

Now we can add a button to the JavaScript app to cause it to use the class we just defined to make toast. Before we do this, we must add a reference to the ToasterComponent project we just created. In Solution Explorer, open the shortcut menu for the ToasterApplication project, choose **Add &gt; References**, and then choose the **Add New Reference** button. In the Add Reference dialog box, in the left pane under Solution, select the component project, and then in the middle pane, select ToasterComponent. Choose the **OK** button.

In Solution Explorer, open the shortcut menu for the ToasterApplication project and then choose **Set as Startup Project**.

At the end of the default.js file, add a namespace to contain the functions to call the component and be called back by it. The namespace will have two functions, one to make toast and one to handle the toast-complete event. The implementation of makeToast creates a Toaster object, registers the event handler, and makes the toast. So far, the event handler doesn’t do much, as shown here:

```javascript
    WinJS.Namespace.define("ToasterApplication"), {
       makeToast: function () {

          var toaster = new ToasterComponent.Toaster();
          //toaster.addEventListener("ontoastcompletedevent", ToasterApplication.toastCompletedEventHandler);
          toaster.ontoastcompletedevent = ToasterApplication.toastCompletedEventHandler;
          toaster.makeToast("Peanut Butter");
       },

       toastCompletedEventHandler: function(event) {
           // The sender of the event (the delegate's first type parameter)
           // is mapped to event.target. The second argument of the delegate
           // is contained in event, which means in this case event is a
           // Toast class, with a toastType string.
           var toastType = event.toastType;

           document.getElementById('toastOutput').innerHTML = "<p>Made " + toastType + " toast</p>";
        },
    });
```

The makeToast function must be hooked up to a button. Update default.html to include a button and some space to output the result of making toast:

```html
    <body>
        <h1>Click the button to make toast</h1>
        <button onclick="ToasterApplication.makeToast()">Make Toast!</button>
        <div id="toasterOutput">
            <p>No Toast Yet...</p>
        </div>
    </body>
```

If we weren’t using a TypedEventHandler, we would now be able to run the app on the local machine and click the button to make toast. But in our app, nothing happens. To find out why, let’s debug the managed code that fires the ToastCompletedEvent. Stop the project, and then on the menu bar, choose **Debug &gt; Toaster Application properties**. Change **Debugger Type** to **Managed Only**. Again on the menu bar, choose **Debug &gt; Exceptions**, and then select **Common Language Runtime Exceptions**.

Now run the app and click the make-toast button. The debugger catches an invalid cast exception. Although it’s not obvious from its message, this exception is occurring because proxies are missing for that interface.

![missing proxy](./images/debuggererrormissingproxy.png)

The first step in creating a proxy and stub for a component is to add a unique ID or GUID to the interfaces. However, the GUID format to use differs depending on whether you're coding in C#, Visual Basic, or another .NET language, or in C++.

## To generate GUIDs for the component's interfaces (C# and other .NET languages)

On the menu bar, choose Tools &gt; Create GUID. In the dialog box, select 5. \[Guid(“xxxxxxxx-xxxx...xxxx)\]. Choose the New GUID button and then choose the Copy button.

![guid generator tool](./images/guidgeneratortool.png)

Go back to the interface definition, and then paste the new GUID just before the IToaster interface, as shown in the following example. (Don't use the GUID in the example. Every unique interface should have its own GUID.)

```cpp
[Guid("FC198F74-A808-4E2A-9255-264746965B9F")]
        public interface IToaster...
```

Add a using directive for the System.Runtime.InteropServices namespace.

Repeat these steps for the IToast interface.

## To generate GUIDs for the component's interfaces (C++)

On the menu bar, choose Tools &gt; Create GUID. In the dialog box, select 3. static const struct GUID = {...}. Choose the New GUID button and then choose the Copy button.

Paste the GUID just before the IToaster interface definition. After you paste, the GUID should resemble the following example. (Don't use the GUID in the example. Every unique interface should have its own GUID.)
```cpp
// {F8D30778-9EAF-409C-BCCD-C8B24442B09B}
    static const GUID <<name>> = { 0xf8d30778, 0x9eaf, 0x409c, { 0xbc, 0xcd, 0xc8, 0xb2, 0x44, 0x42, 0xb0, 0x9b } };
```
Add a using directive for Windows.Foundation.Metadata to bring GuidAttribute into scope.

Now manually convert the const GUID to a GuidAttribute so that it's formatted as shown in the following example. Notice that the curly braces are replaced with brackets and parentheses, and the trailing semicolon is removed.
```cpp
// {E976784C-AADE-4EA4-A4C0-B0C2FD1307C3}
    [GuidAttribute(0xe976784c, 0xaade, 0x4ea4, 0xa4, 0xc0, 0xb0, 0xc2, 0xfd, 0x13, 0x7, 0xc3)]
    public interface IToaster
    {...
```
Repeat these steps for the IToast interface.

Now that the interfaces have unique IDs, we can create an IDL file by feeding the .winmd file into the winmdidl command-line tool, and then generate the C source code for the proxy and stub by feeding that IDL file into the MIDL command-line tool. Visual Studio do this for us if we create post-build events as shown in the following steps.

## To generate the proxy and stub source code

To add a custom post-build event, in Solution Explorer, open the shortcut menu for the ToasterComponent project and then choose Properties. In the left pane of the property pages, select Build Events, and then choose the Edit Post-build button. Add the following commands to the post-build command line. (The batch file must be called first to set the environment variables to find the winmdidl tool.)
```cpp
call "$(DevEnvDir)..\..\vc\vcvarsall.bat" $(PlatformName)
winmdidl /outdir:output "$(TargetPath)"
midl /metadata_dir "%WindowsSdkDir%References\CommonConfiguration\Neutral" /iid "$(ProjectDir)$(TargetName)_i.c" /env win32 /h "$(ProjectDir)$(TargetName).h" /winmd "Output\$(TargetName).winmd" /W1 /char signed /nologo /winrt /dlldata "$(ProjectDir)dlldata.c" /proxy "$(ProjectDir)$(TargetName)_p.c" "Output\$(TargetName).idl"
```
**Important**  For an ARM or x64 project configuration, change the MIDL /env parameter to x64 or arm32.

To make sure the IDL file is regenerated every time the .winmd file is changed, change **Run the post-build event** to **When the build updates the project output.**
The Build Events property page should resemble this:
![build events](./images/buildevents.png)

Rebuild the solution to generate and compile the IDL.

You can verify that MIDL correctly compiled the solution by looking for ToasterComponent.h, ToasterComponent_i.c, ToasterComponent_p.c, and dlldata.c in the ToasterComponent project directory.

## To compile the proxy and stub code into a DLL

Now that you have the required files, you can compile them to produce a DLL, which is a C++ file. To make this as easy as possible, add a new project to support building the proxies. Open the shortcut menu for the ToasterApplication solution and then choose **Add > New Project**. In the left pane of the **New Project** dialog box, expand **Visual C++ &gt; Windows &gt; Univeral Windows**, and then in the middle pane, select **DLL (Windows Store apps)**. (Notice that this is NOT a C++ Windows Runtime Component project.) Name the project Proxies and then choose the **OK** button. These files will be updated by the post-build events when something changes in the C# class.

By default, the Proxies project generates header .h files and C++ .cpp files. Because the DLL is built from the files produced from MIDL, the .h and .cpp files are not required. In Solution Explorer, open the shortcut menu for them, choose **Remove**, and then confirm the deletion.

Now that the project is empty, you can add back the MIDL-generated files. Open the shortcut menu for the Proxies project, and then choose **Add > Existing Item.** In the dialog box, navigate to the ToasterComponent project directory and select these files: ToasterComponent.h, ToasterComponent_i.c, ToasterComponent_p.c, and dlldata.c files. Choose the **Add** button.

In the Proxies project, create a .def file to define the DLL exports described in dlldata.c. Open the shortcut menu for the project, and then choose **Add > New Item**. In the left pane of the dialog box, select Code and then in the middle pane, select Module-Definition File. Name the file proxies.def and then choose the **Add** button. Open this .def file and modify it to include the EXPORTS that are defined in dlldata.c:

```cpp
EXPORTS
    DllCanUnloadNow         PRIVATE
    DllGetClassObject       PRIVATE
```

If you build the project now, it will fail. To correctly compile this project, you have to change how the project is compiled and linked. In Solution Explorer, open the shortcut menu for the Proxies project and then choose **Properties**. Change the property pages as follows.

In the left pane, select **C/C++ > Preprocessor**, and then in the right pane, select **Preprocessor Definitions**, choose the down-arrow button, and then select **Edit**. Add these definitions in the box:

```cpp
WIN32;_WINDOWS
```
Under **C/C++ > Precompiled Headers**, change **Precompiled Header** to **Not Using Precompiled Headers**, and then choose the **Apply** button.

Under **Linker > General**, change **Ignore Import Library** to **Ye**s, and then choose the **Apply** button.

Under **Linker > Input**, select **Additional Dependencies**, choose the down-arrow button, and then select **Edit**. Add this text in the box:

```cpp
rpcrt4.lib;runtimeobject.lib
```

Do not paste these libs directly into the list row. Use the **Edit** box to ensure that MSBuild in Visual Studio will maintain the correct additional dependencies.

When you have made these changes, choose the **OK** button in the **Property Pages** dialog box.

Next, take a dependency on the ToasterComponent project. This ensures that the Toaster will build before the proxy project builds. This is required because the Toaster project is responsible for generating the files to build the proxy.

Open the shortcut menu for the Proxies project and then choose Project Dependencies. Select the check boxes to indicate that the Proxies project depends on the ToasterComponent project, to ensure that Visual Studio builds them in the correct order.

Verify that the solution builds correctly by choosing **Build > Rebuild Solution** on the Visual Studio menu bar.


## To register the proxy and stub

In the ToasterApplication project, open the shortcut menu for package.appxmanifest and then choose **Open With**. In the Open With dialog box, select **XML Text Editor** and then choose the **OK** button. We're going to paste in some XML that provides a windows.activatableClass.proxyStub extension registration and which are based on the GUIDs in the proxy. To find the GUIDs to use in the .appxmanifest file, open ToasterComponent_i.c. Find entries that resemble the ones in the following example. Also notice the definitions for IToast, IToaster, and a third interface—a typed event handler that has two parameters: a Toaster and Toast. This matches the event that's defined in the Toaster class. Notice that the GUIDs for IToast and IToaster match the GUIDs that are defined on the interfaces in the C# file. Because the typed event handler interface is autogenerated, the GUID for this interface is also autogenerated.

```cpp
MIDL_DEFINE_GUID(IID, IID___FITypedEventHandler_2_ToasterComponent__CToaster_ToasterComponent__CToast,0x1ecafeff,0x1ee1,0x504a,0x9a,0xf5,0xa6,0x8c,0x6f,0xb2,0xb4,0x7d);


MIDL_DEFINE_GUID(IID, IID___x_ToasterComponent_CIToast,0xF8D30778,0x9EAF,0x409C,0xBC,0xCD,0xC8,0xB2,0x44,0x42,0xB0,0x9B);


MIDL_DEFINE_GUID(IID, IID___x_ToasterComponent_CIToaster,0xE976784C,0xAADE,0x4EA4,0xA4,0xC0,0xB0,0xC2,0xFD,0x13,0x07,0xC3);
```

Now we copy the GUIDs, paste them in package.appxmanifest in a node that we add and name Extensions, and then reformat them. The manifest entry resembles the following example—but again, remember to use your own GUIDs. Notice that the ClassId GUID in the XML is the same as ITypedEventHandler2. This is because that GUID is the first one that's listed in ToasterComponent_i.c. The GUIDs here are case-insensitive. Instead of manually reformatting the GUIDs for IToast and IToaster, you can go back into the interface definitions and get the GuidAttribute value, which has the correct format. In C++, there is a correctly-formatted GUID in the comment. In any case, you must manually reformat the GUID that's used for both the ClassId and the event handler.

```cpp
      <Extensions> <!--Use your own GUIDs!!!-->
        <Extension Category="windows.activatableClass.proxyStub">
          <ProxyStub ClassId="1ecafeff-1ee1-504a-9af5-a68c6fb2b47d">
            <Path>Proxies.dll</Path>
            <Interface Name="IToast" InterfaceId="F8D30778-9EAF-409C-BCCD-C8B24442B09B"/>
            <Interface Name="IToaster"  InterfaceId="E976784C-AADE-4EA4-A4C0-B0C2FD1307C3"/>  
            <Interface Name="ITypedEventHandler_2_ToasterComponent__CToaster_ToasterComponent__CToast" InterfaceId="1ecafeff-1ee1-504a-9af5-a68c6fb2b47d"/>
          </ProxyStub>      
        </Extension>
      </Extensions>
```

Paste the Extensions XML node as a direct child of the Package node, and a peer of, for example, the Resources node.

Before moving on, it’s important to ensure that:

-   The ProxyStub ClassId is set to the first GUID in the ToasterComponent\_i.c file. Use the first GUID that's defined in this file for the classId. (This might be the same as the GUID for ITypedEventHandler2.)
-   The Path is the package relative path of the proxy binary. (In this walkthrough, proxies.dll is in the same folder as ToasterApplication.winmd.)
-   The GUIDs are in the correct format. (This is easy to get wrong.)
-   The interface IDs in the manifest match the IIDs in ToasterComponent\_i.c file.
-   The interface names are unique in the manifest. Because these are not used by the system, you can choose the values. It is a good practice to choose interface names that clearly match interfaces that you have defined. For generated interfaces, the names should be indicative of the generated interfaces. You can use the ToasterComponent\_i.c file to help you generate interface names.

If you try to run the solution now, you will get an error that proxies.dll is not part of the payload. Open the shortcut menu for the **References** folder in the ToasterApplication project and then choose **Add Reference**. Select the check box next to the Proxies project. Also, make sure that the check box next to ToasterComponent is also selected. Choose the **OK** button.

The project should now build. Run the project and verify that you can make toast.

## Related topics

* [Creating Windows Runtime Components in C++](creating-windows-runtime-components-in-cpp.md)
