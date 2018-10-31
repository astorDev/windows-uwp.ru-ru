---
author: eliotcowley
title: Данные реестра для игровых устройств управления
description: Узнайте о данных, которые можно добавить в реестр компьютера, чтобы игровые устройства управления можно было использовать в играх UWP.
ms.assetid: 2DD0B384-8776-4599-9E52-4FC0AA682735
ms.author: wdg-dev-content
ms.date: 06/25/2018
ms.topic: article
keywords: windows 10, uwp, игры, ввод, реестр, настройка
ms.localizationpriority: medium
ms.openlocfilehash: 4bbd4074c52514b9cb66fd6f2dd189421f61d5ee
ms.sourcegitcommit: ca96031debe1e76d4501621a7680079244ef1c60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5833452"
---
# <a name="registry-data-for-game-controllers"></a>Данные реестра для игровых устройств управления

> [!NOTE]
> Данный раздел предназначен для производителей игровых устройств, совместимых с Windows 10, и не применим для большинства разработчиков.

[Пространство имен Windows.Gaming.Input](https://docs.microsoft.com/uwp/api/windows.gaming.input) позволяет независимым поставщикам оборудования (IHV) добавлять данные в реестр компьютера, чтобы их устройства отображались как элементы [Gamepad](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepad), [RacingWheel](https://docs.microsoft.com/uwp/api/windows.gaming.input.racingwheel), [ArcadeStick](https://docs.microsoft.com/uwp/api/windows.gaming.input.arcadestick), [FlightStick](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.input.flightstick) и [UINavigationController](https://docs.microsoft.com/uwp/api/windows.gaming.input.uinavigationcontroller) соответствующим образом. Все независимые поставщики оборудования должны добавить эти данные для совместимых контроллеров. Таким образом все игры UWP (и любые классические игры, которые используют API WinRT) смогут поддерживать ваше игровое устройство управления.

## <a name="mapping-scheme"></a>Схема сопоставления

Сопоставления для устройств с идентификатором поставщика (VID) **VVVV**, кодом продукта (PID) **PPPP**, страницей использования **UUUU** и идентификатором использования **XXXX** будут извлечены из этого расположения в реестре:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\VVVVPPPPUUUUXXXX`

В таблице ниже приведены ожидаемые значения в корневой папке устройства:

<table>
    <tr>
        <th>Имя</th>
        <th>Тип</th>
        <th>Обязательно?</th>
        <th>Сведения</th>
    </tr>
    <tr>
        <td>Disabled</td>
        <td>DWORD</td>
        <td>Нет</td>
        <td>
            <p>Указывает, что данное устройство должна быть отключена.</p>
            <ul>
                <li><b>0</b>: устройство не отключено.</li>
                <li><b>1</b>: устройство отключено.</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td>Описание</td>
        <td>REG_SZ <td>Нет</td>
        <td>Краткое описание устройства.</td>
    </tr>
</table>

Установщик устройства должен добавить эти данные в реестр (с помощью программы установки или [INF-файла](https://docs.microsoft.com/windows-hardware/drivers/install/inf-files)).

Подразделы корневой папки устройства подробно описаны в следующих разделах.

### <a name="gamepad"></a>Gamepad

В таблице ниже приведены обязательные и необязательные подразделы в разделе **Gamepad**:

<table>
    <tr>
        <th>Подраздел</th>
        <th>Обязательно?</th>
        <th>Сведения</th>
    </tr>
    <tr>
        <td>Menu</td>
        <td>Да</td>
        <td rowspan="18" style="vertical-align: middle;">См. раздел <a href="#button-mapping">Сопоставление кнопок</a>.</td>
    </tr>
    <tr>
        <td>View</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>A</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>B</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>X</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Y</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>LeftShoulder</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>RightShoulder</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>LeftThumbstickButton</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>RightThumbstickButton</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>DPadUp</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>DPadDown</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>DPadLeft</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>DPadRight</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Paddle1</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Paddle2</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Paddle3</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Paddle4</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>LeftTrigger</td>
        <td>Да</td>
        <td rowspan="6" style="vertical-align: middle;">См. разделе <a href="#axis-mapping">Сопоставление осей</a></td>
    </tr>
    <tr>
        <td>RightTrigger</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>LeftThumbstickX</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>LeftThumbstickY</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>RightThumbstickX</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>RightThumbstickY</td>
        <td>Да</td>
    </tr>
</table>

> [!NOTE]
> Если вы добавляете игровое устройство как поддерживаемый **Gamepad**, настоятельно рекомендуется также добавить его как поддерживаемый **UINavigationController**.

### <a name="racingwheel"></a>RacingWheel

В таблице ниже приведены обязательные и необязательные подразделы в разделе **RacingWheel**:

<table>
    <tr>
        <th>Подраздел</th>
        <th>Обязательно?</th>
        <th>Сведения</th>
    </tr>
    <tr>
        <td>PreviousGear</td>
        <td>Да</td>
        <td rowspan="30" style="vertical-align: middle;">См. раздел <a href="#button-mapping">Сопоставление кнопок</a>.</td>
    </tr>
    <tr>
        <td>NextGear</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>DPadUp</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>DPadDown</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>DPadLeft</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>DPadRight</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button1</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button2</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button3</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button4</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button5</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button6</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button7</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button8</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button9</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button10</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button11</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button12</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button13</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button14</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button15</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Button16</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>FirstGear</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>SecondGear</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>ThirdGear</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>FourthGear</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>FifthGear</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>SixthGear</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>SeventhGear</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>ReverseGear</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Wheel</td>
        <td>Да</td>
        <td rowspan="5" style="vertical-align: middle;">См. разделе <a href="#axis-mapping">Сопоставление осей</a></td>
    </tr>
    <tr>
        <td>Throttle</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Brake</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Clutch</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Handbrake</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>MaxWheelAngle</td>
        <td>Да</td>
        <td>См. раздел <a href="#properties-mapping">Сопоставление свойств</a></td>
    </tr>
</table>

### <a name="arcadestick"></a>ArcadeStick

В таблице ниже приведены обязательные и необязательные подразделы в разделе **ArcadeStick**:

<table>
    <tr>
        <th>Подраздел</th>
        <th>Обязательно?</th>
        <th>Сведения</th>
    </tr>
    <tr>
        <td>Action1</td>
        <td>Да</td>
        <td rowspan="12" style="vertical-align: middle;">См. раздел <a href="#button-mapping">Сопоставление кнопок</a>.</td>
    </tr>
    <tr>
        <td>Action2</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Action3</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Action4</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Action5</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Action6</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Special1</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Special2</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>StickUp</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>StickDown</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>StickLeft</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>StickRight</td>
        <td>Да</td>
    </tr>
</table>

### <a name="flightstick"></a>FlightStick

В таблице ниже приведены обязательные и необязательные подразделы в разделе **FlightStick**:

<table>
    <tr>
        <th>Подраздел</th>
        <th>Обязательно?</th>
        <th>Сведения</th>
    </tr>
    <tr>
        <td>FirePrimary</td>
        <td>Да</td>
        <td rowspan="2" style="vertical-align: middle;">См. раздел <a href="#button-mapping">Сопоставление кнопок</a>.</td>
    </tr>
    <tr>
        <td>FireSecondary</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Roll</td>
        <td>Да</td>
        <td rowspan="4" style="vertical-align: middle;">См. разделе <a href="#axis-mapping">Сопоставление осей</a></td>
    </tr>
    <tr>
        <td>Pitch</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Yaw</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Throttle</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>HatSwitch</td>
        <td>Да</td>
        <td>См. раздел <a href="#switch-mapping">Сопоставление переключателей</a></td>
    </tr>
</table>

### <a name="uinavigation"></a>UINavigation

В таблице ниже приведены обязательные и необязательные подразделы в разделе **UINavigation**:

<table>
    <tr>
        <th>Подраздел</th>
        <th>Обязательно?</th>
        <th>Сведения</th>
    </tr>
    <tr>
        <td>Menu</td>
        <td>Да</td>
        <td rowspan="24" style="vertical-align: middle;">См. раздел <a href="#button-mapping">Сопоставление кнопок</a>.</td>
    </tr>
    <tr>
        <td>View</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Accept</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Cancel</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>PrimaryUp</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>PrimaryDown</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>PrimaryLeft</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>PrimaryRight</td>
        <td>Да</td>
    </tr>
    <tr>
        <td>Context1</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Context2</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Context3</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>Context4</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>PageUp</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>PageDown</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>PageLeft</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>PageRight</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>ScrollUp</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>ScrollDown</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>ScrollLeft</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>ScrollRight</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>SecondaryUp</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>SecondaryDown</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>SecondaryLeft</td>
        <td>Нет</td>
    </tr>
    <tr>
        <td>SecondaryRight</td>
        <td>Нет</td>
    </tr>
</table>

Дополнительные сведения о контроллерах навигации пользовательского интерфейса и указанных выше командах см. в разделе [Контроллер навигации в пользовательском интерфейсе](https://docs.microsoft.com/windows/uwp/gaming/ui-navigation-controller).

## <a name="keys"></a>Разделы

Далее описывается содержимое каждого подраздела в разделах **Gamepad**, **RacingWheel**, **ArcadeStick**, **FlightStick** и **UINavigation**.

### <a name="button-mapping"></a>Сопоставление кнопок

В таблице ниже приведены значения, которые необходимы для сопоставления кнопки. Например, если при нажатии **DPadUp** на игровом устройстве управления сопоставление **DPadUp** должно содержать значение **ButtonIndex** (значение **Source**— **Button**). Если **DPadUp** необходимо сопоставить с положением переключателя, то сопоставление **DPadUp** должно содержать значения **SwitchIndex** и **SwitchPosition** (значение **Source**— **Switch**).

<table>
    <tr>
        <th>Источник</th>
        <th>Значение</th>
        <th>Тип значения</th>
        <th>Обязательно?</th>
        <th>Сведения о значении</th>
    </tr>
    <tr>
        <td>Button</td>
        <td>ButtonIndex</td>
        <td>DWORD</td>
        <td>Да</td>
        <td>Индекс в массиве кнопок <b>RawGameController</b>.</td>
    </tr>
    <tr>
        <td rowspan="4" style="vertical-align: middle;">Axis</td>
        <td>AxisIndex</td>
        <td>DWORD</td>
        <td>Да</td>
        <td>Индекс в массиве осей <b>RawGameController</b>.</td>
    </tr>
    <tr>
        <td>Invert</td>
        <td>DWORD</td>
        <td>Нет</td>
        <td>Указывает, что значение оси следует инвертировать перед применением коэффициентов <b>ThresholdPercent</b> и <b>DebouncePercent</b>.</td>
    </tr>
    <tr>
        <td>ThresholdPercent</td>
        <td>DWORD</td>
        <td>Да</td>
        <td>Указывает положение оси, в котором сопоставленное значение кнопки переходит между состоянием "нажато" и "отпущено". Допустимый диапазон значений— от 0 до 100. Кнопка считается нажатой, если значение оси больше или равно этому значению.</td>
    </tr>
    <tr>
        <td>DebouncePercent</td>
        <td>DWORD</td>
        <td>Да</td>
        <td>
            <p>Определяет размер окна вокруг значения <b>ThresholdPercent</b>, которое используется для устранения ложного срабатывания обнаруженного состояния кнопки. Допустимый диапазон значений— от 0 до 100. Переход между состояниями кнопки возможен, только если значение оси выходит за верхнюю или нижнюю границу окна между моментом. Например, если <b>ThresholdPercent</b> имеет значение 50, а <b>DebouncePercent</b>— 10, то границами окна между моментом будет 45% и 55% от полных значений диапазона оси. Кнопка не может перейти в нажатое состояние, пока значение оси не достигнет 55% или не превысит это значение, и не может перейти в отпущенное состояние, пока значение оси не достигнет 45% или меньшего значения.</p>
            <p>Вычисленные границы окна между моментом находятся в диапазоне от 0% до 100%. Например, пороговое значение 5% и окно между моментом 20% приведут к тому, что границы окна между моментом станут равны 0% и 15%. Состояние кнопки для значений оси 0% и 100% всегда отображаются как отпущенное и нажатое соответственно независимо от порогового значения и значения между моментом.</p>
        </td>
    </tr>
    <tr>
        <td rowspan="3" style="vertical-align: middle;">Switch</td>
        <td>SwitchIndex</td>
        <td>DWORD</td>
        <td>Да</td>
        <td>Индекс в массиве переключателей <b>RawGameController</b>.</td>
    </tr>
    <tr>
        <td>SwitchPosition</td>
        <td>REG_SZ</td>
        <td>Да</td>
        <td>
            <p>Указывает положение переключателя, в котором сопоставленная кнопка сообщит о нажатии. Значением положений может быть одна из этих строк:</p>
            <ul>
                <li>Up</li> 
                <li>UpRight</li>
                <li>Right</li>
                <li>DownRight</li>
                <li>Down</li>
                <li>DownLeft</li>
                <li>Left</li>
                <li>UpLeft</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td>IncludeAdjacent</td>
        <td>DWORD</td>
        <td>Нет</td>
        <td>Указывает, что в смежных положениях переключателя сопоставленная кнопка также сообщит о нажатии.</td>
    </tr>
</table>

### <a name="axis-mapping"></a>Сопоставление осей

В таблице ниже приведены значения, которые необходимы для сопоставления оси.

<table>
    <tr>
        <th>Источник</th>
        <th>Значение</th>
        <th>Тип значения</th>
        <th>Обязательно?</th>
        <th>Сведения о значении</th>
    </tr>
    <tr>
        <td rowspan="2" style="vertical-align: middle;">Button</td>
        <td>MaxValueButtonIndex</td>
        <td>DWORD</td>
        <td>Да</td>
        <td>
            <p>Индекс в массиве кнопок <b>RawGameController</b>, который преобразуется в сопоставленное однонаправленное значение оси.</p>
            <table>
                <tr>
                    <th>MaxButton</th>
                    <th>AxisValue</th>
                </tr>
                <tr>
                    <td>FALSE</td>
                    <td>0,0</td>
                </tr>
                <tr>
                    <td>TRUE</td>
                    <td>1,0</td>
                </tr>
            </table>
        </td>
    </tr>
    <tr>
        <td>MinValueButtonIndex</td>
        <td>DWORD</td>
        <td>Нет</td>
        <td>
            <p>Указывает, что сопоставленная ось двунаправленная. Значения <b>MaxButton</b> и <b>MinButton</b> объединяются в одну двунаправленную ось, как показано ниже.</p>
            <table>
                <tr>
                    <th>MinButton</th>
                    <th>MaxButton</th>
                    <th>AxisValue</th>
                </tr>
                <tr>
                    <td>FALSE</td>
                    <td>FALSE</td>
                    <td>0,5</td>
                </tr>
                <tr>
                    <td>FALSE</td>
                    <td>TRUE</td>
                    <td>1,0</td>
                </tr>
                <tr>
                    <td>TRUE</td>
                    <td>FALSE</td>
                    <td>0,0</td>
                </tr>
                <tr>
                    <td>TRUE</td>
                    <td>TRUE</td>
                    <td>0,5</td>
                </tr>
            </table>
        </td>
    </tr>
    <tr>
        <td rowspan="2" style="vertical-align: middle;">Axis</td>
        <td>AxisIndex</td>
        <td>DWORD</td>
        <td>Да</td>
        <td>Индекс в массиве осей <b>RawGameController</b>.</td>
    </tr>
    <tr>
        <td>Invert</td>
        <td>DWORD</td>
        <td>Нет</td>
        <td>Указывает, что сопоставленное значение оси следует инвертировать перед возвратом.</td>
    </tr>
    <tr>
        <td rowspan="3" style="vertical-align: middle;">Switch</td>
        <td>SwitchIndex</td>
        <td>DWORD</td>
        <td>Да</td>
        <td>Индекс в массиве переключателей <b>RawGameController</b>.
    </tr>
    <tr>
        <td>MaxValueSwitchPosition</td>
        <td>REG_SZ</td>
        <td>Да</td>
        <td>
            <p>Одна из следующих строк:</p>
            <ul>
                <li>Up</li>
                <li>UpRight</li>
                <li>Right</li>
                <li>DownRight</li>
                <li>Down</li>
                <li>DownLeft</li>
                <li>Left</li>
                <li>UpLeft</li>
            </ul>
            <p>Указывает положение переключателя, в котором сопоставленное значение оси отображается как равное 1,0. Противоположное направление <b>MaxValueSwitchPosition</b> считается равным 0,0. Например, если значение <b>MaxValueSwitchPosition</b> равно <b>Up</b>, значение оси преобразуется, как показано ниже:</p>
            <table>
                <tr>
                    <th>Положение переключателя</th>
                    <th>AxisValue</th>
                </tr>
                <tr>
                    <td>Up</td>
                    <td>1,0</td>
                </tr>
                <tr>
                    <td>Center</td>
                    <td>0,5</td>
                </tr>
                <tr>
                    <td>Down</td>
                    <td>0,0</td>
                </tr>
            </table>
        </td>
    </tr>
    <tr>
        <td>IncludeAdjacent</td>
        <td>DWORD</td>
        <td>Нет</td>
        <td>
            <p>Указывает, что в смежных положениях переключателя сопоставленная ось также сообщит значение 1,0. В примере выше, если установлено значение <b>IncludeAdjacent</b>, преобразование оси выполняется следующим образом:</p>
            <table>
                <tr>
                    <th>Положение переключателя</th>
                    <th>AxisValue</th>
                </tr>
                <tr>
                    <td>Up</td>
                    <td>1,0</td>
                </tr>
                <tr>
                    <td>UpRight</td>
                    <td>1,0</td>
                </tr>
                <tr>
                    <td>UpLeft</td>
                    <td>1,0</td>
                </tr>
                <tr>
                    <td>Center</td>
                    <td>0,5</td>
                </tr>
                <tr>
                    <td>Down</td>
                    <td>0,0</td>
                </tr>
                <tr>
                    <td>DownRight</td>
                    <td>0,0</td>
                </tr>
                <tr>
                    <td>DownLeft</td>
                    <td>0,0</td>
                </tr>
            </table>
        </td>
    </tr>
</table>

### <a name="switch-mapping"></a>Сопоставление переключателей

Положения переключателя могут быть сопоставлены с набором кнопок в массиве кнопок **RawGameController** или с индексом в массиве переключателей. Переключения положений не могут быть сопоставлены с осями.

<table>
    <tr>
        <th>Источник</th>
        <th>Значение</th>
        <th>Тип значения</th>
        <th>Сведения о значении</th>
    </tr>
    <tr>
        <td rowspan="10" style="vertical-align: middle;">Button</td>
        <td>ButtonCount</td>
        <td>DWORD</td>
        <td>2, 4 или 8</td>
    </tr>
    <tr>
        <td>SwitchKind</td>
        <td>REG_SZ</td>
        <td><b>TwoWay</b>, <b>FourWay</b> или <b>EightWay</b>
    </tr>
    <tr>
        <td>UpButtonIndex</td>
        <td>DWORD</td>
        <td rowspan="8" style="vertical-align: middle;">См. раздел <a href="#buttonindex-values">Значения *ButtonIndex</a></td>
    </tr>
    <tr>
        <td>DownButtonIndex</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td>LeftButtonIndex</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td>RightButtonIndex</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td>UpRightButtonIndex</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td>DownRightButtonIndex</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td>DownLeftButtonIndex</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td>UpLeftButtonIndex</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td rowspan="9" style="vertical-align: middle;">Axis</td>
        <td>SwitchKind</td>
        <td>REG_SZ</td>
        <td><b>TwoWay</b>, <b>FourWay</b> или <b>EightWay</b></td>
    </tr>
    <tr>
        <td>XAxisIndex</td>
        <td>DWORD</td>
        <td rowspan="2" style="vertical-align: middle;"><b>YAxisIndex</b> всегда присутствует. <b>XAxisIndex</b> присутствует, только если <b>SwitchKind</b> имеет значение <b>FourWay</b> или <b>EightWay</b>.</td>
    </tr>
    <tr>
        <td>YAxisIndex</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td>XDeadZonePercent</td>
        <td>DWORD</td>
        <td rowspan="2" style="vertical-align: middle;">Указывает размер мертвой зоны вокруг центрального положения осей.</td>
    </tr>
    <tr>
        <td>YDeadZonePercent</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td>XDebouncePercent</td>
        <td>DWORD</td>
        <td rowspan="2" style="vertical-align: middle;">Определите размер окон вокруг верхнего и нижнего ограничения мертвой зоны, которые используются для устранения ложного срабатывания переданного состояния переключателя.</td>
    </tr>
    <tr>
        <td>YDebouncePercent</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td>XInvert</td>
        <td>DWORD</td>
        <td rowspan="2" style="vertical-align: middle;">Указывает, что соответствующие значения оси следует инвертировать перед применением вычислений мертвой зоны и окна между моментом.</td>
    </tr>
    <tr>
        <td>YInvert</td>
        <td>DWORD</td>
    </tr>
    <tr>
        <td rowspan="3" style="vertical-align: middle;">Switch</td>
        <td>SwitchIndex</td>
        <td>DWORD</td>
        <td>Индекс в массиве переключателей <b>RawGameController</b>.
    </tr>
    <tr>
        <td>Invert</td>
        <td>DWORD</td>
        <td>Указывает, что переключатель сообщает о положении против часовой стрелки, а не по часовой стрелке.</td>
    </tr>
    <tr>
        <td>PositionBias</td>
        <td>DWORD</td>
        <td>
            <p>Смещает начальную точку передачи сведений о положении на указанную величину. <b>PositionBias</b> всегда учитывается по часовой стрелке от исходной начальной точки и применяется перед изменением порядка значений на обратный.</p>
            <p>Например, переключатель, который сообщает о положениях начиная с <b>DownRight</b> против часовой стрелки, можно нормализовать, указав флаг <b>Invert</b> и присвоив <b>PositionBias</b> значение 5:</p>
            <table>
                <tr>
                    <th>Положение</th>
                    <th>Переданное значение</th>
                    <th>После установки флагов PositionBias и Invert</th>
                </tr>
                <tr>
                    <td>DownRight</td>
                    <td>0</td>
                    <td>3</td>
                </tr>
                <tr>
                    <td>Right</td>
                    <td>1</td>
                    <td>2</td>
                </tr>
                <tr>
                    <td>UpRight</td>
                    <td>2</td>
                    <td>1</td>
                </tr>
                <tr>
                    <td>Up</td>
                    <td>3</td>
                    <td>0</td>
                </tr>
                <tr>
                    <td>UpLeft</td>
                    <td>4</td>
                    <td>7</td>
                </tr>
                <tr>
                    <td>Left</td>
                    <td>5</td>
                    <td>6</td>
                </tr>
                <tr>
                    <td>DownLeft</td>
                    <td>6</td>
                    <td>5</td>
                </tr>
                <tr>
                    <td>Down</td>
                    <td>7</td>
                    <td>4</td>
                </tr>
            </table>
    </tr>
</table>

#### <a name="buttonindex-values"></a>Значения *ButtonIndex

Индекс значений \*ButtonIndex в массиве кнопок **RawGameController**:

<table>
    <tr>
        <th>ButtonCount</th>
        <th>SwitchKind</th>
        <th>RequiredMappings</th>
    </tr>
    <tr>
        <td>2</td>
        <td>TwoWay</td>
        <td>
            <ul>
                <li>UpButtonIndex</li>
                <li>DownButtonIndex</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>FourWay</td>
        <td>
            <ul>
                <li>UpButtonIndex</li>
                <li>DownButtonIndex</li>
                <li>LeftButtonIndex</li>
                <li>RightButtonIndex</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>EightWay</td>
        <td>
            <ul>
                <li>UpButtonIndex</li>
                <li>DownButtonIndex</li>
                <li>LeftButtonIndex</li>
                <li>RightButtonIndex</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td>8</td>
        <td>EightWay</td>
        <td>
            <ul>
                <li>UpButtonIndex</li>
                <li>DownButtonIndex</li>
                <li>LeftButtonIndex</li>
                <li>RightButtonIndex</li>
                <li>UpRightButtonIndex</li>
                <li>DownRightButtonIndex</li>
                <li>DownLeftButtonIndex</li>
                <li>UpLeftButtonIndex</li>
            </ul>
        </td>
    </tr>
</table>

### <a name="properties-mapping"></a>Сопоставление свойств

Это статическое сопоставление значений для различных типов сопоставления.

<table>
    <tr>
        <th>Сопоставление</th>
        <th>Значение</th>
        <th>Тип значения</th>
        <th>Сведения о значении</th>
    </tr>
    <tr>
        <td>RacingWheel</td>
        <td>MaxWheelAngle</td>
        <td>DWORD</td>
        <td>Указывает максимальный физический угол колесика, поддерживаемый в одном направлении. Например, для колесика с возможностью поворота от –90градусов до 90градусов здесь будет указано значение 90.</td>
    </tr>
</table>

## <a name="labels"></a>Метки

Метки должны находиться в разделе **Labels** в корневом каталоге устройства. В разделе **Labels** может быть три подраздела: **Buttons**, **Axes** и **Switches**.

### <a name="button-labels"></a>Метки кнопок

Раздел **Buttons** сопоставляет каждую позицию кнопки в массиве кнопок **RawGameController** со строкой. Каждая строка внутренне сопоставляется с соответствующим значением перечисления [GameControllerButtonLabel](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamecontrollerbuttonlabel). Например, если на геймпаде 10 кнопок и **RawGameController** анализирует кнопки и представляет их в отчете о кнопках в следующем порядке:

```cpp
Menu,               // Index 0
View,               // Index 1
LeftStickButton,    // Index 2
RightStickButton,   // Index 3
LetterA,            // Index 4
LetterB,            // Index 5
LetterX,            // Index 6
LetterY,            // Index 7
LeftBumper,         // Index 8
RightBumper         // Index 9
```

Метки в разделе **Buttons** должны отображаться в следующем порядке:

<table>
    <tr>
        <th>Имя</th>
        <th>Значение (тип: REG_SZ)</th>
    </tr>
    <tr>
        <td>Button0</td>
        <td>Menu</td>
    </tr>
    <tr>
        <td>Button1</td>
        <td>View</td>
    </tr>
    <tr>
        <td>Button2</td>
        <td>LeftStickButton</td>
    </tr>
    <tr>
        <td>Button3</td>
        <td>RightStickButton</td>
    </tr>
    <tr>
        <td>Button4</td>
        <td>LetterA</td>
    </tr>
    <tr>
        <td>Button5</td>
        <td>LetterB</td>
    </tr>
    <tr>
        <td>Button6</td>
        <td>LetterX</td>
    </tr>
    <tr>
        <td>Button7</td>
        <td>LetterY</td>
    </tr>
    <tr>
        <td>Button8</td>
        <td>LeftBumper</td>
    </tr>
    <tr>
        <td>Button9</td>
        <td>RightBumper</td>
    </tr>
</table>

### <a name="axis-labels"></a>Метки осей

Раздел **Axes** сопоставляет каждую позицию оси в массиве осей **RawGameController** с одной из меток в перечислении [перечисления GameControllerButtonLabel](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.input.gamecontrollerbuttonlabel), как и метки кнопок. См. пример в разделе [Метки кнопок](#button-labels).

### <a name="switch-labels"></a>Метки переключателей

Раздел **Switches** сопоставляет положения переключателей с метками. Для значений используется следующее соглашение об именовании: чтобы получить метку положения переключателя с индексом *x* в массиве переключателей **RawGameController**, добавьте эти значения в подраздел **Switches**: 

* SwitchxUp 
* SwitchxUpRight 
* SwitchxRight
* SwitchxDownRight
* SwitchxDown
* SwitchxDownLeft
* SwitchxUpLeft
* SwitchxLeft

В следующей таблице привет пример набора меток для положения 4-позиционного переключателя, которые отображаются с индексом 0 в **RawGameController**: 

<table>
    <tr>
        <th>Имя</th>
        <th>Значение (тип: REG_SZ)</th>
    </tr>
    <tr>
        <td>Switch0Up</td>
        <td>XboxUp</td>
    </tr>
    <tr>
        <td>Switch0Right</td>
        <td>XboxRight</td>
    </tr>
    <tr>
        <td>Switch0Down</td>
        <td>XboxDown</td>
    </tr>
    <tr>
        <td>Switch0Left</td>
        <td>XboxLeft</td>
    </tr>
</table>

<!--### Label strings

* XboxBack
* XboxStart
* XboxMenu
* XboxView
* XboxUp
* XboxDown
* XboxLeft
* XboxRight
* XboxA
* XboxB
* XboxX
* XboxY
* XboxLeftBumper
* XboxLeftTrigger
* XboxLeftStickButton
* XboxRightBumper
* XboxRightTrigger
* XboxRightStickButton
* XboxPaddle1
* XboxPaddle2
* XboxPaddle3
* XboxPaddle4
* Mode
* Select
* Menu
* View
* Back
* Start
* Options
* Share
* Up
* Down
* Left
* Right
* LetterA
* LetterB
* LetterC
* LetterL
* LetterR
* LetterX
* LetterY
* LetterZ
* Cross
* Circle
* Square
* Triangle
* LeftBumper
* LeftTrigger
* LeftStickButton
* Left1
* Left2
* Left3
* RightBumper
* RightTrigger
* RightStickButton
* Right1
* Right2
* Right3
* Paddle1
* Paddle2
* Paddle3
* Paddle4
* Plus
* Minus
* DownLeftArrow
* DialLeft
* DialRight
* Suspension-->

## <a name="example-registry-file"></a>Пример файла реестра

Вот приведен пример сопоставлений и значений в файле реестра для универсального элемента управления **RacingWheel**:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004]
"Description" = "Example Wheel Device"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\Labels\Buttons]
"Button0" = "LetterA"
"Button1" = "LetterB"
"Button2" = "LetterX"
"Button3" = "LetterY"
"Button6" = "Menu"
"Button7" = "View"
"Button8" = "RightStickButton"
"Button9" = "LeftStickButton"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\Labels\Switches]
"Switch0Down" = "Down"
"Switch0Left" = "Left"
"Switch0Right" = "Right"
"Switch0Up" = "Up"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel]
"MaxWheelAngle" = dword:000001c2

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Brake]
"AxisIndex" = dword:00000002
"Invert" = dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Button1]
"ButtonIndex" = dword:00000000

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Button2]
"ButtonIndex" = dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Button3]
"ButtonIndex" = dword:00000002

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Button4]
"ButtonIndex" = dword:00000003

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Button5]
"ButtonIndex" = dword:00000009

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Button6]
"ButtonIndex" = dword:00000008

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Button7]
"ButtonIndex" = dword:00000007

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Button8]
"ButtonIndex" = dword:00000006

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Clutch]
"AxisIndex" = dword:00000003
"Invert" = dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\DPadDown]
"IncludeAdjacent" = dword:00000001
"SwitchIndex" = dword:00000000
"SwitchPosition" = "Down"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\DPadLeft]
"IncludeAdjacent" = dword:00000001
"SwitchIndex" = dword:00000000
"SwitchPosition" = "Left"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\DPadRight]
"IncludeAdjacent" = dword:00000001
"SwitchIndex" = dword:00000000
"SwitchPosition" = "Right"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\DPadUp]
"IncludeAdjacent" = dword:00000001
"SwitchIndex" = dword:00000000
"SwitchPosition" = "Up"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\FifthGear]
"ButtonIndex" = dword:00000010

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\FirstGear]
"ButtonIndex" = dword:0000000c

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\FourthGear]
"ButtonIndex" = dword:0000000f

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\NextGear]
"ButtonIndex" = dword:00000004

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\PreviousGear]
"ButtonIndex" = dword:00000005

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\ReverseGear]
"ButtonIndex" = dword:0000000b

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\SecondGear]
"ButtonIndex" = dword:0000000d

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\SixthGear]
"ButtonIndex" = dword:00000011

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\ThirdGear]
"ButtonIndex" = dword:0000000e

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Throttle]
"AxisIndex" = dword:00000001
"Invert" = dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GameInput\Devices\1234567800010004\RacingWheel\Wheel]
"AxisIndex" = dword:00000000
"Invert" = dword:00000000
```

## <a name="see-also"></a>См. также:

* [Пространство имен Windows.Gaming.Input](https://docs.microsoft.com/uwp/api/windows.gaming.input)
* [Пространство имен Windows.Gaming.Input.Custom](https://docs.microsoft.com/uwp/api/windows.gaming.input.custom)
* [INF-файлы](https://docs.microsoft.com/windows-hardware/drivers/install/inf-files)