---
author: martinekuan
title: Создание событий в компонентах среды выполнения Windows
ms.assetid: 3F7744E8-8A3C-4203-A1CE-B18584E89000
description: 
---

# Создание событий в компонентах среды выполнения Windows


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


\[Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Microsoft не дает никаких гарантий, прямых или косвенных, в отношении указанной здесь информации.\]

Если ваш компонент среды выполнения Windows вызывает событие типа делегата, определенного пользователем, в фоновом (рабочем) потоке и вы хотите, чтобы среда JavaScript могла получить его, можно реализовать и вызвать событие одним из следующих способов.

-   Вариант 1: создать событие с помощью [Windows.UI.Core.CoreDispatcher](https://msdn.microsoft.com/library/windows/apps/windows.ui.core.coredispatcher.aspx), чтобы маршалировать его в контекст потока JavaScript. Хотя обычно это оптимальный вариант, в некоторых случаях он не обеспечивает максимальную производительность.
-   Вариант 2: использовать [Windows.Foundation.EventHandler ](https://msdn.microsoft.com/library/windows/apps/br206577.aspx)&lt;Object&gt;, но с потерей сведений о типе события. Если вариант 1 не приемлем или его производительности недостаточно, то это хороший метод, если потеря сведений о типе не критична для вас.
-   Вариант 3: создать собственный прокси и заглушку для компонента. Этот вариант сложнее всего реализовать, но он сохраняет сведения о типе и может обеспечивать более высокую, чем вариант 1, производительность для ресурсоемких сценариев.

Если создать событие в фоновом потоке без использования одного из этих вариантов, клиент JavaScript просто не получит его.

## Общие сведения


Все компоненты и приложения среды выполнения Windows — это COM-объекты независимо от того, какой язык используется для их создания. В API Windows большая часть компонентов представляет собой динамичные COM-объекты, способные одинаково хорошо взаимодействовать с объектами в фоновом потоке и в потоке пользовательского интерфейса. Если COM-объект не может быть динамичным, ему требуются вспомогательные объекты, прокси и заглушки, для связи с другими COM-объектами в потоке пользовательского интерфейса и фоновом потоке. (С точки зрения модели COM такую связь называют связью между подразделениями потока.)

Большинство объектов в API Windows динамичные или содержат встроенные прокси и заглушки. Однако прокси и заглушки невозможно создать для универсальных типов, таких как Windows.Foundation.[TypedEventHandler&lt;TSender, TResult&gt;](https://msdn.microsoft.com/library/windows/apps/br225997.aspx), поскольку они не представляют собой полные типы, если не указать аргумент типа. Отсутствие прокси и заглушек становится проблемой только для клиентов JavaScript, но если вы хотите, чтобы ваш компонент можно было использовать из JavaScript так же, как из C++ или языка .NET, необходимо использовать один из следующих трех вариантов.

## Вариант 1: создание события с помощью CoreDispatcher


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

## Вариант 2: использование EventHandler&lt;Object&gt;, но с потерей сведений о типе


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

## Вариант 3: создание собственных прокси и заглушки


Для повышения производительности с определяемыми пользователем типами событий, которые полностью сохраняют сведения о типе, необходимо создать собственные объекты прокси и заглушки и внедрить их в пакет приложения. Обычно этот вариант следует использовать только в тех редких случаях, когда ни один из других вариантов не пригоден. Кроме того, нет гарантии, что этот метод обеспечит лучшую производительность, чем остальные. Фактическая производительность зависит от множества факторов. Используйте профилировщик Visual Studio или другие средства профилирования для измерения фактической производительности в приложении и определите, действительно ли событие — это "узкое место".

Далее в этой статье показано, как использовать C# для создания простого компонента среды выполнения Windows, а затем использовать C++ для создания библиотеки DLL для прокси и заглушки, которые позволят JavaScript использовать событие Windows.Foundation.TypedEventHandler&lt;TSender, TResult&gt;, вызываемое компонентом в асинхронной операции. (Вы также можете использовать C++ или Visual Basic для создания компонента. Действия, относящиеся к созданию прокси и заглушек, аналогичны.) Это пошаговое руководство основано на примере создания внутрипроцессного компонента среды выполнения Windows (C++/CX) и объясняет его назначение.

Пошаговое руководство содержит следующие компоненты:

-   Здесь вы создаете два базовых класса среды выполнения Windows. Один класс предоставляет событие типа [Windows.Foundation.TypedEventHandler&lt;TSender, TResult&gt;](https://msdn.microsoft.com/library/windows/apps/br225997.aspx), а другой класс — это тип, возвращаемый среде JavaScript в качестве аргумента TValue. Эти классы не могут взаимодействовать с JavaScript, если не выполнить последующие действия.
-   Это приложение активирует объект основного класса, вызывает метод и обрабатывает событие, вызываемое компонентом среды выполнения Windows.
-   Это необходимо для средств, создающих классы прокси и заглушки.
-   Затем вы используете файл IDL для генерации исходного кода C для прокси и заглушки.
-   Зарегистрируйте объекты прокси и заглушки, чтобы среда выполнения COM могла их найти, и добавьте ссылку на соответствующую библиотеку DLL в проект приложения.

## Создание компонента среды выполнения Windows

1.  В строке меню Visual Studio выберите **Файл &gt; Создать проект**. В диалоговом окне **Создание проекта** разверните узел **JavaScript &gt; Универсальное приложение для Windows** и выберите **Пустое приложение**. Назовите проект ToasterApplication и нажмите кнопку **ОК** .
2.  Добавьте компонент среды выполнения Windows на C# в решение: в обозревателе решений откройте контекстное меню для нужного решения и выберите **Добавить &gt; Новый проект**. Разверните узел **Visual C# &gt; Магазин Windows** и выберите пункт **Компонент среды выполнения Windows**. Назовите проект ToasterComponent и нажмите кнопку **ОК**. ToasterComponent будет корневым пространством имен для компонентов, которые вы создадите позднее.

    В обозревателе решений откройте контекстное меню решения и выберите пункт **Свойства**. В диалоговом окне **Страницы свойств** выберите элемент **Свойства конфигурации** в левой области, а затем в верхней части диалогового окна задайте для параметра **Конфигурация** значение **Отладка**, а для параметра **Платформа** выберите "x86", "x64" или "ARM". Нажмите кнопку **ОК**.

    > **Важно!** Значение "Любой ЦП" не подойдет, поскольку оно недопустимо для библиотеки DLL с собственным кодом Win32, которую вы добавите в решение позже.

3.  В обозревателе решений переименуйте файл class1.cs на ToasterComponent.cs, чтобы он соответствовал имени проекта. Visual Studio автоматически переименует класс в файле.
4.  Добавьте в CS-файл директиву using для пространства имен Windows.Foundation, чтобы добавить TypedEventHandler в область действия.
5.  Если вам необходимы прокси и заглушки, компонент должен использовать интерфейсы, чтобы предоставлять доступ к открытым членам. В файле ToasterComponent.cs укажите интерфейс для тостера и интерфейс для всплывающего уведомления, которое создает тостер.

    > **Примечание.** Для C# этот шаг можно пропустить. Вместо этого сначала создайте класс, а затем откройте его контекстное меню и выберите **Рефакторинг &gt; Извлечь интерфейс**. В созданном коде вручную сделайте интерфейсы общедоступными.

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

6.  Затем нам понадобятся открытые классы, реализующие эти интерфейсы, которые будут доступны из приложения JavaScript, созданного позже.

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

    > **Примечание.** Асинхронный вызов в предыдущем коде использует метод ThreadPool.RunAsync только для демонстрации простого способа активации события в фоновом потоке. Вы можете написать этот метод, как показано в следующем примере, и он будет работать, так как планировщик заданий .NET автоматически маршалирует асинхронные вызовы и вызовы await в поток пользовательского интерфейса.
  
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

1.  Now we can add a button to the JavaScript app to cause it to use the class we just defined to make toast. Before we do this, we must add a reference to the ToasterComponent project we just created. In Solution Explorer, open the shortcut menu for the ToasterApplication project, choose **Add &gt; References**, and then choose the **Add New Reference** button. In the Add Reference dialog box, in the left pane under Solution, select the component project, and then in the middle pane, select ToasterComponent. Choose the **OK** button.
2.  In Solution Explorer, open the shortcut menu for the ToasterApplication project and then choose **Set as Startup Project**.
3.  At the end of the default.js file, add a namespace to contain the functions to call the component and be called back by it. The namespace will have two functions, one to make toast and one to handle the toast-complete event. The implementation of makeToast creates a Toaster object, registers the event handler, and makes the toast. So far, the event handler doesn’t do much, as shown here:

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

4.  The makeToast function must be hooked up to a button. Update default.html to include a button and some space to output the result of making toast:

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

1.  On the menu bar, choose Tools &gt; Create GUID. In the dialog box, select 5. \[Guid(“xxxxxxxx-xxxx...xxxx)\]. Choose the New GUID button and then choose the Copy button.

    ![guid generator tool](./images/guidgeneratortool.png)

2.  Go back to the interface definition, and then paste the new GUID just before the IToaster interface, as shown in the following example. (Don't use the GUID in the example. Every unique interface should have its own GUID.)

    ```cpp
    [Guid("FC198F74-A808-4E2A-9255-264746965B9F")]
        public interface IToaster...
    ```

3.  Add a using directive for the System.Runtime.InteropServices namespace.
4.  Repeat these steps for the IToast interface.

## To generate GUIDs for the component's interfaces (C++)

1.  On the menu bar, choose Tools &gt; Create GUID. In the dialog box, select 3. static const struct GUID = {...}. Choose the New GUID button and then choose the Copy button.

2.  Paste the GUID just before the IToaster interface definition. After you paste, the GUID should resemble the following example. (Don't use the GUID in the example. Every unique interface should have its own GUID.)

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

2.  Before moving on, it’s important to ensure that:

    -   The ProxyStub ClassId is set to the first GUID in the ToasterComponent\_i.c file. Use the first GUID that's defined in this file for the classId. (This might be the same as the GUID for ITypedEventHandler2.)
    -   The Path is the package relative path of the proxy binary. (In this walkthrough, proxies.dll is in the same folder as ToasterApplication.winmd.)
    -   The GUIDs are in the correct format. (This is easy to get wrong.)
    -   The interface IDs in the manifest match the IIDs in ToasterComponent\_i.c file.
    -   The interface names are unique in the manifest. Because these are not used by the system, you can choose the values. It is a good practice to choose interface names that clearly match interfaces that you have defined. For generated interfaces, the names should be indicative of the generated interfaces. You can use the ToasterComponent\_i.c file to help you generate interface names.

3.  If you try to run the solution now, you will get an error that proxies.dll is not part of the payload. Open the shortcut menu for the **References** folder in the ToasterApplication project and then choose **Add Reference**. Select the check box next to the Proxies project. Also, make sure that the check box next to ToasterComponent is also selected. Choose the **OK** button.

    The project should now build. Run the project and verify that you can make toast.

## Related topics

* [Creating Windows Runtime Components in C++](creating-windows-runtime-components-in-cpp.md)



<!--HONumber=May16_HO2-->


