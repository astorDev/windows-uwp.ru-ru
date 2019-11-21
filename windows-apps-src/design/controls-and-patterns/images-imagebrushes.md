---
Description: Узнайте, как интегрировать изображения в приложение, в том числе как использовать API двух основных классов XAML — Image и ImageBrush.
title: Изображения и кисти изображений
ms.assetid: CEA8780C-71A3-4168-A6E8-6361CDFB2FAF
label: Images and image brushes
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a07fc5c669d3c9cd374a4a031fba2ee6ef6d5756
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74257279"
---
# <a name="images-and-image-brushes"></a>Изображения и кисти изображений

Чтобы отобразить изображение, вы можете использовать объект **Image** или **ImageBrush**. Объект Image отрисовывает изображение, а объект ImageBrush закрашивает изображением другой объект. 

> **Важные API**: [класс Image](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image), [свойство Source](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image.source), [класс ImageBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush), [свойство ImageSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imagebrush.imagesource)

## <a name="are-these-the-right-elements"></a>Эти элементы правильные?
Используйте элемент **Image** для отображения отдельного изображения в приложении.

Используйте **ImageBrush** для применения изображения к другому объекту. К способам использования ImageBrush относятся декоративные эффекты для текста или фоны для элементов управления или контейнеров макета.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/Image">открыть приложение и увидеть Image в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="create-an-image"></a>Создание изображения

### <a name="image"></a>Изображение
В этом примере показано, как создать изображение с помощью объекта [Image](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image).


```XAML
<Image Width="200" Source="sunset.jpg" />
```

Ниже показан отрисованный объект Image.

![Пример элемента Image](images/Image_Licorice.jpg)

В этом примере свойство [Source](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image.source) определяет расположение изображения, которое следует отобразить. В свойстве Source можно указать абсолютный URL-адрес (например, http://contoso.com/myPicture.jpg) ) или URL-адрес, который соответствует составу и структуре упаковки вашего приложения. В нашем примере мы помещаем файл изображения "licorice.jpg" в корневую папку проекта и объявляем параметры проекта, которые содержат этот файл.

### <a name="imagebrush"></a>ImageBrush

Объект [ImageBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush) позволяет использовать изображение для рисования области, где применяется объект [Brush](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Brush). Например, вы можете использовать ImageBrush в качестве значения свойства [Fill](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.shape.fill) класса [Ellipse](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) или свойства [Background](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.background) класса [Canvas](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas).

В следующем примере показано, как можно использовать объект ImageBrush для закрашивания фигуры Ellipse.

```XAML
<Ellipse Height="200" Width="300">
   <Ellipse.Fill>
     <ImageBrush ImageSource="sunset.jpg" />
   </Ellipse.Fill>
</Ellipse>
```

Ниже показана фигура Ellipse, закрашенная с помощью объекта ImageBrush.

![Фигура Ellipse, закрашенная с помощью ImageBrush.](images/Image_ImageBrush_Ellipse.jpg)

### <a name="stretch-an-image"></a>Растяжение изображения

Если не указать значение [Width](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.width) или [Height](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.height) для **Image**, то изображение отображается с размерами, задаваемыми **Source**. Определяя значения **Width** и **Height**, вы создаете удерживающую прямоугольную область, в пределах которой отображается изображение. Вы можете выбрать, каким образом изображение будет заполнять удерживающую область, с помощью свойства [Stretch](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image.stretch). Свойство Stretch может принимать следующие значения, определенные в перечислении [Stretch](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Stretch).

-   **Нет**. изображение не растягивается для заполнения отображаемой области. Будьте осторожны при настройке значения Stretch. Если исходное изображение больше заполняемой области, то изображение будет обрезано, а это обычно нежелательно, так как, в отличие от намеренного вырезания [Clip](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.clip), окном просмотра управлять вы не можете.
-   **Uniform**: масштаб изображения изменяется так, чтобы оно соответствовало размеру отображаемой области. но пропорции изображения сохраняются. Это значение используется по умолчанию.
-   **UniformToFill**: масштаб изображения изменяется так, чтобы оно полностью заполняло отображаемую область, но исходные пропорции изображения сохраняются.
-   **Fill**: масштаб изображения изменяется так, чтобы оно соответствовало размеру отображаемой области. Поскольку высота и ширина изображения изменяются независимо друг от друга, его исходные пропорции могут быть нарушены. То есть изображение может быть искажено так, что не сможет полностью заполнить отображаемую область.

![Пример параметров растяжения.](images/Image_Stretch.jpg)

### <a name="crop-an-image"></a>Обрезка изображения

С помощью свойства [Clip](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.clip) можно вырезать область отображаемого изображения. Свойство Clip задается в [Geometry](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Geometry). Вырезание областей непрямоугольной формы в настоящее время не поддерживается.

В следующем примере показано, как можно использовать класс [RectangleGeometry](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RectangleGeometry) для вырезания области изображения. В этом примере мы определяем объект **Image** со значением высоты 200. Объект **RectangleGeometry** определяет прямоугольную область для отображаемого изображения. Для свойства [Rect](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.rectanglegeometry.rect) задано значение "25,25,100,150", которое определяет начало прямоугольника в точке "25,25" с шириной 100 и высотой 150. Отображается только та часть изображения, которая попадает в область прямоугольника.

```xaml
<Image Source="sunset.jpg" Height="200">
    <Image.Clip>
        <RectangleGeometry Rect="25,25,100,150" />
    </Image.Clip>
</Image>
```

Обрезанное изображение на черном фоне.

![Объект Image, обрезанный с помощью RectangleGeometry.](images/Image_Cropped.jpg)

### <a name="apply-an-opacity"></a>Применение непрозрачности

Вы можете применить к изображению свойство [Opacity](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.opacity), что позволяет отрисовать его полупрозрачным. Значения непрозрачности варьируют от 0,0 до 1,0, где 1,0 — полная непрозрачность, а 0,0 — полная прозрачность. В этом примере показано, как можно применить непрозрачность 0,5 к объекту Image.

```xaml
<Image Height="200" Source="sunset.jpg" Opacity="0.5" />
```

Ниже показано отрисованное изображение с непрозрачностью 0,5 и черным фоном, видимым сквозь частичную прозрачность.

![Объект Image с непрозрачностью 0,5.](images/Image_Opacity.jpg)

### <a name="image-file-formats"></a>Форматы файлов изображений

**Image** и **ImageBrush** могут отображать следующие форматы файлов изображений:

-   JPEG
-   PNG
-   BMP
-   GIF
-   TIFF
-   JPEG XR
-   значки (ICO)

Интерфейсы API для [Image](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image), [BitmapImage](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.BitmapImage) и [BitmapSource](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.BitmapSource) не включают в себя специальные методы для кодирования и декодирования форматов мультимедиа. Все операции кодирования и декодирования — встроенные, и только некоторые аспекты кодирования и декодирования могут быть включены в данные для событий загрузки. Если приложение выполняет преобразования изображений или манипуляции с ними, вам следует использовать API, доступные в пространстве имен [Windows.Graphics.Imaging](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging), при выполнении каких-либо действий по кодированию или декодированию изображений. Кроме того, эти API поддерживаются компонентом обработки изображений Windows (WIC) в Windows.

Начиная с Windows 10 версии 1607, элемент **Image** поддерживает анимационные GIF-изображения. При использовании объекта **BitmapImage** в качестве изображения **Source** можно обращаться к API-интерфейсам BitmapImage для управления воспроизведением анимационных GIF-изображений. Дополнительные сведения см. в разделе "Примечания" на странице класса [BitmapImage](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.BitmapImage).

> **Примечание.** &nbsp;&nbsp;Поддержка анимационных GIF-изображений доступна, если приложение скомпилировано для Windows 10 версии 1607 и выполняется в версии 1607 (или в более новой версии). Если приложение скомпилировано для предыдущих версий или выполняется в предыдущих версиях, отображается только первый кадр GIF-изображения, без анимации.

Дополнительные сведения о ресурсах приложения и о том, как запаковать исходные изображения в приложение, см. в разделе [Определение ресурсов приложения](https://docs.microsoft.com/previous-versions/windows/apps/hh965321(v=win.10)).

### <a name="writeablebitmap"></a>WriteableBitmap

[WriteableBitmap](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.WriteableBitmap) предоставляет [BitmapSource](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.BitmapSource), который может быть изменен и который не использует основное декодирование файлов компонента WIC. Можно динамически изменять изображения и заново обрабатывать обновленные изображения. Для определения содержимого буфера **WriteableBitmap** используйте свойство [PixelBuffer](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.writeablebitmap.pixelbuffer), чтобы получить доступ к буферу, и потоковый буфер или буфер для используемого языка, чтоб его наполнить. Пример кода приведен в разделе [WriteableBitmap](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.WriteableBitmap).

### <a name="rendertargetbitmap"></a>RenderTargetBitmap

Класс [RenderTargetBitmap](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.RenderTargetBitmap) позволяет записать дерево пользовательского интерфейса XAML в работающем приложении, а затем представляет источник точечного рисунка. После захвата источник изображения можно применить к другим частям приложения, сохранить в качестве ресурса или данных приложения или использовать для других сценариев. Одним из наиболее полезных сценариев является создание динамического эскиза XAML-страницы для схемы навигации, например предоставление ссылки на изображение из элемента управления [Hub](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub). В **RenderTargetBitmap** существуют некоторые ограничения в отношении содержимого, которое появляется в захваченном изображении. Дополнительные сведения см. в справочнике по API для [RenderTargetBitmap](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.RenderTargetBitmap).

### <a name="image-sources-and-scaling"></a>Исходные изображения и масштабирование

Создайте исходные изображения в нескольких рекомендуемых размерах, чтобы ваше приложение хорошо выглядело при масштабировании операционной системой Windows. Указывая **Source** для **Image**, вы можете использовать соглашение об именовании, которое будет автоматически ссылаться на правильный ресурс для текущего масштабирования. Особенности контекста именования и дополнительные сведения описываются в разделе [Краткое руководство: использование файловых и графических ресурсов](https://docs.microsoft.com/previous-versions/windows/apps/hh965325(v=win.10)).

Дополнительные сведения о проектировании масштабирования см. в документе [Руководство по взаимодействию с пользователями — макеты и масштабирование](https://developer.microsoft.com/windows/apps/design).

### <a name="image-and-imagebrush-in-code"></a>Image и ImageBrush в коде

Обычно для определения элементов Image и ImageBrush используется XAML, а не код. Это происходит потому, что данные элементы часто являются выходными элементами средств разработки в рамках определения пользовательского интерфейса XAML.

При определении Image или ImageBrush с помощью кода используйте конструкторы по умолчанию, а затем задайте соответствующее свойство источника ([Image.Source](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image.source) или [ImageBrush.ImageSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imagebrush.imagesource)). При задании свойства источника с помощью кода потребуется [BitmapImage](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.BitmapImage) (а не универсальный код ресурса (URI)). Если источником является поток, для инициализации значения используйте метод [SetSourceAsync](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.bitmapsource.setsourceasync). Если источником является универсальный код ресурса (URI), который включает в себя содержимое в вашем приложение, использующее схемы **ms-appx** или **ms-resource**, используйте конструктор [BitmapImage](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.bitmapimage), который применяет универсальный код ресурса (URI). Также можно рассмотреть вариант обработки события [ImageOpened](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.bitmapimage.imageopened), если существуют некоторые временные проблемы с получением или декодированием источника изображения, при которых может понадобиться отображать альтернативное содержимое до тех пор, пока источник изображения не станет доступным. Пример кода см. в разделе [Пример использования изображений на основе XAML](https://code.msdn.microsoft.com/windowsapps/0f5d56ae-5e57-48e1-9cd9-993115b027b9).

> [!NOTE]
> Если вы установили изображения с помощью кода, вы можете использовать автоматическую обработку для получения доступа к неквалифицированным ресурсам с текущими квалификаторами масштаба и языка либо вы можете использовать [ResourceManager](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Resources.Core.ResourceManager) и [ResourceMap](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Resources.Core.ResourceMap) с квалификаторами масштаба и языка для непосредственного получения ресурсов. Подробнее см. в разделе [Система управления ресурсами](https://docs.microsoft.com/previous-versions/windows/apps/jj552947(v=win.10)).

## <a name="get-the-sample-code"></a>Получить пример кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Связанные статьи

-   [Звук, видео и камера](https://docs.microsoft.com/windows/uwp/audio-video-camera/index)
-   [Класс Image](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image)
-   [Класс ImageBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush)