---
title: Создание игры UWP на 2D-платформе MonoGame
description: Простая игра UWP для Microsoft Store, написанная на C# и MonoGame
author: muhsinking
ms.author: mukin
ms.date: 03/06/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 5d5f7af2-41a9-4749-ad16-4503c64bb80c
ms.localizationpriority: medium
ms.openlocfilehash: d38465ce02e0aedf854094ede75fc33701b226a6
ms.sourcegitcommit: 4b97117d3aff38db89d560502a3c372f12bb6ed5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "5440778"
---
# <a name="create-a-uwp-game-in-monogame-2d"></a>Создание игры UWP на 2D-платформе MonoGame

## <a name="a-simple-2d-uwp-game-for-the-microsoft-store-written-in-c-and-monogame"></a>Простая двухмерная игра UWP для Microsoft Store, написанная на C# и MonoGame


![Лист спрайтов с ходящим динозавром](images/JS2D_0.png)

## <a name="introduction"></a>Введение

MonoGame— это упрощенная платформа для разработки игр. В этом руководстве объясняются основы разработки игр в MonoGame, в том числе как загружать содержимое, рисовать спрайты и анимировать их, а также настраивать взаимодействие с пользователем. Также рассматриваются некоторые более сложные задачи, такие как обнаружение столкновений и масштабирование для экранов с высоким DPI. Работа с этим руководством занимает 30–60 минут.

## <a name="prerequisites"></a>Что вам понадобится
+   Windows10 и Microsoft Visual Studio2017.  [Щелкните здесь, чтобы узнать, как подготовиться к работе с Visual Studio](https://docs.microsoft.com/en-us/windows/uwp/get-started/get-set-up).
+ Разработка настольных приложений .NET framework. Если у вас еще нет этого установлена, вы можете получить этого повторно запустите установщик Visual Studio и изменении установки Visual Studio 2017.
+   Знание основ C# или похожих объектно-ориентированных языков программирования. [Щелкните здесь, чтобы узнать, как приступить к работе с C#.](https://docs.microsoft.com/en-us/windows/uwp/get-started/create-a-hello-world-app-xaml-universal)
+   Не помешает также знакомство с базовыми понятиями в области вычислительной техники, такими как классы, методы и переменные.

## <a name="why-monogame"></a>Почему MonoGame?
Огромный выбор платформ для разработки игр— от полнофункциональных движков, таких как Unity, до комплексных мультимедийных API, например DirectX— может привести новичка в замешательство. MonoGame— это набор средств, который по уровню сложности находится где-то между игровым движком и более серьезным API наподобие DirectX. Он предоставляет простой в использовании конвейер содержимого, а также все функции для создания "облегченных" игр, работающих на различных платформах. Лучше всего приложения MonoGame пишутся на чистом C#, и вы можете распространить ее быстро через магазин Майкрософт или других аналогичных платформ распространения.

## <a name="get-the-code"></a>Получите код
Если вы просто хотите увидеть MonoGame в действии, не проходя все руководство шаг за шагом, [щелкните здесь, чтобы получить готовое приложение](https://github.com/Microsoft/Windows-appsample-get-started-mg2d).

Откройте проект в Visual Studio 2017 и нажмите клавишу **F5** , чтобы запустить пример. В первый раз это может занять некоторое время, так как Visual Studio необходимо получить пакеты NuGet, которых не хватает в вашей установке.

Если вы уже сделали это, пропустите следующий раздел о настройке MonoGame, чтобы увидеть пошаговое описание кода.

**Примечание.** Этот пример не претендует на то, чтобы считаться завершенной (или даже забавной) игрой. Его единственная цель — продемонстрировать все базовые понятия разработки двухмерных игр в MonoGame. Вы можете использовать этот код, чтобы сделать что-то намного лучшее. Или же, освоив основы, создайте свою игру с чистого листа!

## <a name="set-up-monogame-project"></a>Настройка проекта MonoGame
1. Установите **MonoGame 3.6** для Visual Studio от [MonoGame.net](http://www.monogame.net/).

2. Запустите Visual Studio 2017.

3. Выберите **Файл -> Создать -> Проект**.

4. В разделе c шаблонами проектов Visual C# выберите **MonoGame** и **MonoGame Windows10 Universal Project**.

5. Назовите проект "MonoGame2D" и нажмите кнопку ОК. Возможно, вам покажется, что в новом проекте полно ошибок; они должны быть исправлены после первого запуска проекта и установки недостающих пакетов NuGet.

6. Убедитесь, что для целевой платформы выбраны параметры **x86** и **Локальный компьютер**, а затем нажмите клавишу **F5**, чтобы создать и запустить пустой проект. Если вы выполнили инструкции выше, по окончании создания проекта вы увидите пустое синее окно.

## <a name="method-overview"></a>Обзор метода
Теперь, когда вы создали проект, откройте файл **Game1.cs** из **обозревателя решений**. Именно здесь будет реализована большая часть игровой логики. Здесь автоматически генерируется много важных методов, когда вы создаете проект MonoGame. Давайте кратко их рассмотрим.

**public Game1()**: конструктор. В этом руководстве мы не будем его менять.

**protected override void Initialize()**: здесь мы инициализируем любые переменные класса, которые будем использовать. Этот метод вызывается один раз в начале игры.

**protected override void LoadContent()**: этот метод загружает содержимое (например, текстуры, звук, шрифты) в память перед началом игры. Как и Initialize, он вызывается один раз при запуске приложения.

**protected override void UnloadContent()**: этот метод используется для выгрузки содержимого, которое не обрабатывается диспетчером содержимого. Этот метод мы не используем вообще.

**protected override void Update(GameTime gameTIme)**: этот метод вызывается один раз для каждого цикла игры. Здесь мы обновляем состояния любого объекта или переменной, используемых в игре. Сюда входят такие свойства объектов, как положение, скорость или цвет. Кроме того, здесь обрабатывается ввод данных пользователем. Иными словами, этот метод обрабатывает всю игровую логику, кроме рисования объектов на экране.
**protected override void Draw(GameTime gameTime)**: этот метод отвечает за рисование объектов на экране с учетом положения, заданного с помощью метода Update.

## <a name="draw-a-sprite"></a>Рисование спрайта
Итак, вы запустили свой свежеиспеченный проект MonoGame и увидели чудесное голубое небо. Теперь давайте добавим землю.
В MonoGame двухмерные рисунки добавляются в приложение в виде "спрайтов". Спрайт— это просто компьютерный рисунок, который обрабатывается как единый объект. Спрайты можно перемещать, масштабировать, видоизменять, анимировать и объединять для создания всего, что только можно представить в двухмерном пространстве.

### <a name="1-download-a-texture"></a>1. Загрузка текстуры
Наш первый спрайт будет совершенно неинтересный. [Щелкните здесь, чтобы скачать этот скучный зеленый прямоугольник.](https://github.com/Microsoft/Windows-appsample-get-started-mg2d/blob/master/MonoGame2D/Content/grass.png)

### <a name="2-add-the-texture-to-the-content-folder"></a>2. Добавление текстуры в папку содержимого
- Откройте **обозреватель решений**.
- Щелкните правой кнопкой мыши файл **Content.mgcb** в папке **Содержимое** и выберите команду **Открыть с помощью**. Во всплывающем меню выберите **Monogame Pipeline**, а затем **ОК**.
- В новом окне щелкните правой кнопкой мыши элемент **Содержимое**, а затем выберите **Добавить -> Существующий элемент**.
- Найдите и выберите зеленый прямоугольник в обозревателе файлов.
- Назовите элемент "grass.png" и выберите **Добавить**.

### <a name="3-add-class-variables"></a>3. Добавление переменных класса
Чтобы загрузить это изображение в качестве текстуры спрайта, откройте **Game1.cs** и добавьте следующие переменные класса.

```CSharp
const float SKYRATIO = 2f/3f;
float screenWidth;
float screenHeight;
Texture2D grass;
```

Переменная SKYRATIO сообщает нам, какую часть окна должно занимать небо по сравнению с травой; в данном случае две трети. Переменные **screenWidth** и **screenHeight** отвечают за размеры окна приложения, а **grass** является местом хранения нашего зеленого прямоугольника.

### <a name="4-initialize-class-variables-and-set-window-size"></a>4. Инициализация переменных класса и настройка размера окна
Переменные **screenWidth** и **screenHeight** все еще нужно инициализировать, поэтому добавьте этот код в метод **Initialize**:

```CSharp
ApplicationView.PreferredLaunchWindowingMode = ApplicationViewWindowingMode.FullScreen;

screenHeight = (float)ApplicationView.GetForCurrentView().VisibleBounds.Height;
screenWidth = (float)ApplicationView.GetForCurrentView().VisibleBounds.Width;

this.IsMouseVisible = false;
```

Кроме установки высоты и ширины экрана, мы также задаем для окна приложения значение **Fullscreen** и делаем мышь невидимой.

### <a name="5-load-the-texture"></a>5. Загрузка текстуры
Чтобы загрузить текстуру в переменную grass, добавьте в метод **LoadContent** следующее:

```CSharp
grass = Content.Load<Texture2D>("grass");
```

### <a name="6-draw-the-sprite"></a>6. Рисование спрайта
Чтобы нарисовать прямоугольник, добавьте следующие строки в метод **Draw**:

```CSharp
GraphicsDevice.Clear(Color.CornflowerBlue);
spriteBatch.Begin();
spriteBatch.Draw(grass, new Rectangle(0, (int)(screenHeight * SKYRATIO),
  (int)screenWidth, (int)screenHeight), Color.White);
spriteBatch.End();
```

Здесь мы используем метод **spriteBatch.Draw**, чтобы разместить заданную текстуру в пределах объекта Rectangle. Объект **Rectangle** определяется с помощью координат x и y его верхнего левого и нижнего правого углов. С помощью переменных **screenWidth**, **screenHeight** и **SKYRATIO**, которые мы определили раньше, мы нарисуем зеленую прямоугольную текстуру в нижней трети экрана. Теперь, запустив программу, вы увидите, что наш знакомый синий фон уже частично закрыт зеленым прямоугольником.

![Зеленый прямоугольник](images/monogame-tutorial-1.png)

## <a name="scale-to-high-dpi-screens"></a>Масштабирование для экранов с высоким DPI
Если вы используете Visual Studio на экране с высокой плотностью пикселей (например, на устройствах Surface Pro или Surface Studio), то обнаружите, что получившийся зеленый прямоугольник не полностью охватывает нижнюю треть экрана. Скорее всего, он будет где-то над нижним левым углом экрана. Чтобы устранить эту проблему и унифицировать интерфейс игры на всех устройствах, необходимо создать метод, который масштабирует определенные значения относительно плотности пикселей на экране:

```CSharp
public float ScaleToHighDPI(float f)
{
  DisplayInformation d = DisplayInformation.GetForCurrentView();
  f *= (float)d.RawPixelsPerViewPixel;
  return f;
}
```

Затем замените инициализации переменных **screenHeight** и **screenWidth** в методе **Initialize** таким образом:

```CSharp
screenHeight = ScaleToHighDPI((float)ApplicationView.GetForCurrentView().VisibleBounds.Height);
screenWidth = ScaleToHighDPI((float)ApplicationView.GetForCurrentView().VisibleBounds.Width);
```

Если вы теперь запустите приложение на устройстве с экраном с высоким DPI, то увидите зеленый прямоугольник, занимающий нижнюю треть экрана, как и предполагалось.

## <a name="build-the-spriteclass"></a>Создание класса SpriteClass
Прежде чем приступить к анимированию спрайтов, мы создадим новый класс под названием SpriteClass, который позволит упростить действия со спрайтами на внешнем уровне.

### <a name="1-create-a-new-class"></a>1. Создание нового класса
В **обозревателе решений** щелкните правой кнопкой мыши **MonoGame2D (Universal Windows)** и выберите **Добавить -> Класс**. Назовите класс SpriteClass.cs, а затем нажмите **Добавить**.

### <a name="2-add-class-variables"></a>2. Добавление переменных класса
Добавьте этот код в класс, который вы только что создали:

```CSharp
public Texture2D texture
{
  get;
}

public float x
{
  get;
  set;
}

public float y
{
  get;
  set;
}

public float angle
{
  get;
  set;
}

public float dX
{
  get;
  set;
}

public float dY
{
  get;
  set;
}

public float dA
{
  get;
  set;
}

public float scale
{
  get;
  set;
}
```

Здесь мы настраиваем переменные класса, необходимые для рисования и анимирования спрайта. Переменные **x** и **y** представляют текущее положение спрайта на плоскости, в то время как переменная **angle** обозначает текущий угол спрайта в градусах (0— вертикальное положение, 90— поворот на 90 градусов по часовой стрелке). Обратите внимание, что для этого класса **x** и **y** представляют координаты центра (**center**) спрайта (по умолчанию начало координат находится в верхнем левом углу). Это упрощает вращение спрайтов, так как оно не зависит от заданного начала координат. Так мы получаем единообразные вращательные движения.

Далее у нас есть переменные **dX**, **dY** и **dA**, которые представляют скорость изменения переменных **x**, **y** и **угол** за секунду.

### <a name="3-create-a-constructor"></a>3. Создание конструктора
При создании экземпляра **SpriteClass** мы предоставляем конструктору сведения о графическом устройстве из файла **Game1.cs**, путь к текстуре в папке проекта и нужный масштаб текстуры относительно ее исходного размера. Мы установим остальные переменные класса после начала игры в методе Update.

```CSharp
public SpriteClass (GraphicsDevice graphicsDevice, string textureName, float scale)
{
  this.scale = scale;
  if (texture == null)
  {
    using (var stream = TitleContainer.OpenStream(textureName))
    {
      texture = Texture2D.FromStream(graphicsDevice, stream);
    }
  }
}
```

### <a name="4-update-and-draw"></a>4. Update и Draw
Остаются еще два метода, которые нужно добавить в декларацию SpriteClass:

```CSharp
public void Update (float elapsedTime)
{
  this.x += this.dX * elapsedTime;
  this.y += this.dY * elapsedTime;
  this.angle += this.dA * elapsedTime;
}

public void Draw (SpriteBatch spriteBatch)
{
  Vector2 spritePosition = new Vector2(this.x, this.y);
  spriteBatch.Draw(texture, spritePosition, null, Color.White, this.angle, new Vector2(texture.Width/2, texture.Height/2), new Vector2(scale, scale), SpriteEffects.None, 0f);
}
```

Метод **Update** класса SpriteClass вызывается в методе **Update** файла Game1.cs и используется для обновления значений **x**, **y** и **angle** спрайтов в соответствии со скоростью изменения каждого из них.

Метод **Draw** вызывается в методе **Draw** файла Game1.cs и используется для рисования спрайта в окне игры.

## <a name="user-input-and-animation"></a>Пользовательский ввод и анимация
Теперь, когда у нас есть класс SpriteClass, мы используем его для создания двух новых игровых объектов. Первый из них— игровой персонаж, которым игрок может управлять с помощью клавиш со стрелками и клавиши ПРОБЕЛ. Второй— это объект, которого игрок должен избегать.

### <a name="1-get-the-textures"></a>1. Получение текстур
В качестве игрового персонажа мы будем использовать нашего "фирменного" кота-ниндзя, оседлавшего своего верного динозавра. [Щелкните здесь, чтобы загрузить изображение.](https://github.com/Microsoft/Windows-appsample-get-started-mg2d/blob/master/MonoGame2D/Content/ninja-cat-dino.png)

А теперь займемся препятствием, которого должен избегать игрок. Что коты-ниндзя и плотоядные динозавры ненавидят больше всего? Есть овощи! [Щелкните здесь, чтобы загрузить изображение.](https://github.com/Microsoft/Windows-appsample-get-started-mg2d/blob/master/MonoGame2D/Content/broccoli.png)

Добавьте эти изображения в файл **Content.mgcb** через **MonoGame Pipeline** (так же, как раньше это сделали с зеленым прямоугольником), назвав их ninja-cat-dino.png and broccoli.png соответственно.

### <a name="2-add-class-variables"></a>2. Добавление переменных класса
Добавьте следующий код в список переменных класса в **Game1.cs**:

```CSharp
SpriteClass dino;
SpriteClass broccoli;

bool spaceDown;
bool gameStarted;

float broccoliSpeedMultiplier;
float gravitySpeed;
float dinoSpeedX;
float dinoJumpY;
float score;

Random random;
```

**dino** и **broccoli**— это наши переменные класса SpriteClass. **dino** соответствует игровому персонажу, а **broccoli**— препятствию.

**spaceDown** отслеживает, удерживается ли клавиша ПРОБЕЛ нажатой (а не нажата и отпущена).

**gameStarted** сообщает, начал ли пользователь играть в первый раз.

**broccoliSpeedMultiplier** определяет, насколько быстро препятствие (брокколи) перемещается по экрану.

**gravitySpeed** определяет, как быстро ускоряется движение игрового персонажа во время приземления после прыжка.

**dinoSpeedX** и **dinoJumpY** определяют, как быстро игровой персонаж перемещается и прыгает.
Переменная score отслеживает, сколько препятствий игрок успешно преодолел.

Наконец, переменная **random** сделает поведение препятствия-брокколи в определенной степени случайным.

### <a name="3-initialize-variables"></a>3. Инициализация переменных
Далее нам необходимо инициализировать эти переменные. Добавьте следующий код в метод Initialize:

```CSharp
broccoliSpeedMultiplier = 0.5f;
spaceDown = false;
gameStarted = false;
score = 0;
random = new Random();
dinoSpeedX = ScaleToHighDPI(1000f);
dinoJumpY = ScaleToHighDPI(-1200f);
gravitySpeed = ScaleToHighDPI(30f);
```

Обратите внимание, что последние три переменные нужно масштабировать для устройств с высоким DPI, так как они определяют скорость изменения в пикселях.

### <a name="4-construct-spriteclasses"></a>4. Построение классов SpriteClass
Мы будем строить объекты SpriteClass в методе **LoadContent**. Добавьте этот код к тому, что у нас уже есть:

```CSharp
dino = new SpriteClass(GraphicsDevice, "Content/ninja-cat-dino.png", ScaleToHighDPI(1f));
broccoli = new SpriteClass(GraphicsDevice, "Content/broccoli.png", ScaleToHighDPI(0.2f));
```

Изображение брокколи намного больше, чем оно должно отображаться в игре, поэтому выберем для него масштаб 0,2 от исходного размера.

### <a name="5-program-obstacle-behavior"></a>5. Программирование поведения препятствия
Мы хотим, чтобы брокколи появлялись где-то за пределами экрана и двигались по направлению к игровому персонажу, который должен будет от них уклоняться. Для этого добавьте данный метод в класс **Game1.cs**:

```CSharp
public void SpawnBroccoli()
{
  int direction = random.Next(1, 5);
  switch (direction)
  {
    case 1:
      broccoli.x = -100;
      broccoli.y = random.Next(0, (int)screenHeight);
      break;
    case 2:
      broccoli.y = -100;
      broccoli.x = random.Next(0, (int)screenWidth);
      break;
    case 3:
      broccoli.x = screenWidth + 100;
      broccoli.y = random.Next(0, (int)screenHeight);
      break;
    case 4:
      broccoli.y = screenHeight + 100;
      broccoli.x = random.Next(0, (int)screenWidth);
      break;
  }

  if (score % 5 == 0) broccoliSpeedMultiplier += 0.2f;

  broccoli.dX = (dino.x - broccoli.x) * broccoliSpeedMultiplier;
  broccoli.dY = (dino.y - broccoli.y) * broccoliSpeedMultiplier;
  broccoli.dA = 7f;
}
```

Первая часть метода с помощью двух случайных чисел определяет, в какой именно точке за пределами экрана будет появляться объект брокколи.

Вторая часть определяет, как быстро будут передвигаться брокколи, с учетом текущего значения score. Скорость будет возрастать после каждых пяти брокколи, столкновения с которыми игроку удалось избежать.

Третья часть задает направление движения спрайта брокколи. Появившись, они двигаются по направлению к игровому персонажу (динозавру). Мы также присвоим переменной **dA** значение 7f, что приведет к вращению брокколи в воздухе во время "погони" за игроком.

### <a name="6-program-game-starting-state"></a>6. Программирование состояния начала игры
Чтобы перейти к обработке ввода с клавиатуры, нам нужен метод, который устанавливает состояние начала игры для двух объектов, которые мы создали. Вместо того чтобы игра началась сразу после запуска приложения, мы хотим, чтобы пользователь запускал ее вручную нажатием клавиши ПРОБЕЛ. Добавьте следующий код, который задает начальное состояние анимированных объектов и сбрасывает значение score:

```CSharp
public void StartGame()
{
  dino.x = screenWidth / 2;
  dino.y = screenHeight * SKYRATIO;
  broccoliSpeedMultiplier = 0.5f;
  SpawnBroccoli();  
  score = 0;
}
```

### <a name="7-handle-keyboard-input"></a>7. Обработка ввода с клавиатуры
Затем нам понадобится новый метод для обработки ввода с клавиатуры. Добавьте этот метод в файл **Game1.cs**:

```CSharp
void KeyboardHandler()
{
  KeyboardState state = Keyboard.GetState();

  // Quit the game if Escape is pressed.
  if (state.IsKeyDown(Keys.Escape))
  {
    Exit();
  }

  // Start the game if Space is pressed.
  if (!gameStarted)
  {
    if (state.IsKeyDown(Keys.Space))
    {
      StartGame();
      gameStarted = true;
      spaceDown = true;
      gameOver = false;
    }
    return;
  }            
  // Jump if Space is pressed
  if (state.IsKeyDown(Keys.Space) || state.IsKeyDown(Keys.Up))
  {
    // Jump if the Space is pressed but not held and the dino is on the floor
    if (!spaceDown && dino.y >= screenHeight * SKYRATIO - 1) dino.dY = dinoJumpY;

    spaceDown = true;
  }
  else spaceDown = false;

  // Handle left and right
  if (state.IsKeyDown(Keys.Left)) dino.dX = dinoSpeedX * -1;

  else if (state.IsKeyDown(Keys.Right)) dino.dX = dinoSpeedX;
  else dino.dX = 0;
}
```

Выше использованы четыре инструкции if:

Первая завершает работу при нажатии клавиши **Escape-**.

Вторая начинает игру, если нажата клавиша **ПРОБЕЛ** и игра еще не запущена.

Благодаря третьей динозавр прыгает через изменение свойства **dY**, если нажата клавиша **ПРОБЕЛ**. Обратите внимание, что персонаж не может прыгнуть, если он не на "земле" (dino.y = screenHeight * SKYRATIO). Кроме того, он не будет прыгать, если нажать и удерживать клавишу ПРОБЕЛ вместо того, чтобы нажать ее и отпустить. Это позволяет избежать прыжка сразу после начала игры (ведь такому нажатию уже присвоена функция запуска игры).

Наконец, последнее предложение if/else проверяет, нажимаются ли стрелки влево или вправо, и соответствующим образом изменяет свойство **dX** динозавра.

**Задача:** можете ли вы сделать так, чтобы указанный выше метод обработки ввода с клавиатуры работал со схемой ввода WASD, а не только с клавишами со стрелками?

### <a name="8-add-logic-to-the-update-method"></a>8. Добавление логики в метод Update
Далее необходимо добавить логику для всех этих частей в метод **Update** в файле **Game1.cs**:

```CSharp
float elapsedTime = (float)gameTime.ElapsedGameTime.TotalSeconds;
KeyboardHandler(); // Handle keyboard input
// Update animated SpriteClass objects based on their current rates of change
dino.Update(elapsedTime);
broccoli.Update(elapsedTime);

// Accelerate the dino downward each frame to simulate gravity.
dino.dY += gravitySpeed;

// Set game floor so the player does not fall through it
if (dino.y > screenHeight * SKYRATIO)
{
  dino.dY = 0;
  dino.y = screenHeight * SKYRATIO;
}

// Set game edges to prevent the player from moving offscreen
if (dino.x > screenWidth - dino.texture.Width/2)
{
  dino.x = screenWidth - dino.texture.Width/2;
  dino.dX = 0;
}
if (dino.x < 0 + dino.texture.Width/2)
{
  dino.x = 0 + dino.texture.Width/2;
  dino.dX = 0;
}

// If the broccoli goes offscreen, spawn a new one and iterate the score
if (broccoli.y > screenHeight+100 || broccoli.y < -100 || broccoli.x > screenWidth+100 || broccoli.x < -100)
{
  SpawnBroccoli();
  score++;
}
```

### <a name="9-draw-spriteclass-objects"></a>9. Рисование объектов SpriteClass
Наконец, добавьте следующий код в метод **Draw** в файле **Game1.cs** сразу после последнего вызова **spriteBatch.Draw**:

```CSharp
broccoli.Draw(spriteBatch);
dino.Draw(spriteBatch);
```

В MonoGame новые вызовы **spriteBatch.Draw** будут рисоваться поверх любых предыдущих вызовов. Это означает, что спрайты как брокколи, так и динозавра будут нарисованы поверх существующего спрайта травы, поэтому никогда не будут закрыты последним независимо от своего положения.

Попробуйте запустить игру и передвигать динозавра с помощью клавиш со стрелками и клавиши ПРОБЕЛ. Если вы следовали инструкциям выше, то сможете управлять движениями персонажа в окне игры, а брокколи будут двигаться с возрастающей скоростью.

![Игровой персонаж и препятствие](images/monogame-tutorial-2.png)

## <a name="render-text-with-spritefont"></a>Прорисовка текста с помощью SpriteFont
С помощью приведенного выше кода отслеживаются результаты игры, но для самого игрока это остается незаметным. К тому же, при запуске приложения непонятно, как действовать дальше. Игрок видит сине-зеленое окно без каких-либо намеков на то, что для начала игры нужно нажать ПРОБЕЛ.

Чтобы исправить эти проблемы, мы будем использовать объект MonoGame нового типа, **SpriteFonts**.

### <a name="1-create-spritefont-description-files"></a>1. Создание файлов описания SpriteFont
В **обозревателе решений** найдите папку **Содержимое**. В этой папке щелкните правой кнопкой мыши файл **Content.mgcb** и выберите команду **Открыть с помощью**. Во всплывающем меню выберите **MonoGame Pipeline**, а затем нажмите клавишу **ОК**. В новом окне щелкните правой кнопкой мыши элемент **Содержимое**, а затем выберите **Добавить -> Новый элемент**. Выберите описание **SpriteFont Description**, назовите его "Score" и нажмите клавишу **ОК**. Точно так же добавьте еще одно описание SpriteFont под названием "GameState".

### <a name="2-edit-descriptions"></a>2. Изменение описания
Щелкните правой кнопкой мыши папку **Содержимое** в **MonoGame Pipeline** и выберите команду **Открыть расположение файла**. Вы должны увидеть папку с только что созданными файлами описания SpriteFont, а также с изображениями, которые вы уже успели добавить в папку "Содержимое". Теперь можно закрыть и сохранить окно MonoGame Pipeline. Из **проводника** откройте оба файла описания в текстовом редакторе (Visual Studio, NotePad ++, Atom и т.д.).

Каждое описание содержит несколько значений, которые описывают SpriteFont. Мы внесем некоторые изменения.

В **Score.spritefont** измените значение **<Size>** с 12 на 36.

В **GameState.spritefont** измените значение **<Size>** с 12 на 72, а значение **<FontName>** с Arial на Agency. Agency— один из стандартных шрифтов Windows10— придаст интерфейсу изюминку.

### <a name="3-load-spritefonts"></a>3. Загрузка SpriteFonts
Вернувшись в Visual Studio, мы в первую очередь добавим новую текстуру на экран-заставку. [Щелкните здесь, чтобы загрузить изображение.](https://github.com/Microsoft/Windows-appsample-get-started-mg2d/blob/master/MonoGame2D/Content/start-splash.png)

Как и раньше, добавьте текстуру в проект, щелкнув правой кнопкой мыши папку "Содержимое" и выбрав **Добавить -> Существующий элемент**. Назовите новый элемент start-splash.png.

Далее добавьте в файл **Game1.cs** следующие переменные класса:

```CSharp
Texture2D startGameSplash;
SpriteFont scoreFont;
SpriteFont stateFont;
```

Затем добавьте в метод **LoadContent** следующие строки:

```CSharp
startGameSplash = Content.Load<Texture2D>("start-splash");
scoreFont = Content.Load<SpriteFont>("Score");
stateFont = Content.Load<SpriteFont>("GameState");
```

### <a name="4-draw-the-score"></a>4. Отображение результата
Перейдите к методу **Draw** в файле **Game1.cs** и добавьте непосредственно перед **spriteBatch.End();** следующий код:

```CSharp
spriteBatch.DrawString(scoreFont, score.ToString(),
new Vector2(screenWidth - 100, 50), Color.Black);
```

Приведенный выше код использует созданное нами описание спрайта (Arial Size 36), чтобы отобразить текущий результат игрока в верхнем правом углу экрана.

### <a name="5-draw-horizontally-centered-text"></a>5. Отображение текста горизонтально по центру
При создании игры часто требуется отобразить текст, который выравнивается по центру— горизонтально или вертикально. Чтобы выровнять вводный текст горизонтально по центру, добавьте этот код в метод **Draw** непосредственно перед **spriteBatch.End();**.

```CSharp
if (!gameStarted)
{
  // Fill the screen with black before the game starts
  spriteBatch.Draw(startGameSplash, new Rectangle(0, 0,
  (int)screenWidth, (int)screenHeight), Color.White);

  String title = "VEGGIE JUMP";
  String pressSpace = "Press Space to start";

  // Measure the size of text in the given font
  Vector2 titleSize = stateFont.MeasureString(title);
  Vector2 pressSpaceSize = stateFont.MeasureString(pressSpace);

  // Draw the text horizontally centered
  spriteBatch.DrawString(stateFont, title,
  new Vector2(screenWidth / 2 - titleSize.X / 2, screenHeight / 3),
  Color.ForestGreen);
  spriteBatch.DrawString(stateFont, pressSpace,
  new Vector2(screenWidth / 2 - pressSpaceSize.X / 2,
  screenHeight / 2), Color.White);
  }
```

Сначала нам нужно создать две строки для текста, который мы хотим отобразить. Далее мы измеряем ширину и высоту каждой строки при печати с помощью метода **SpriteFont.MeasureString(String)**. Так мы получаем размер строки как объекта **Vector2**, где свойство **X** соответствует ширине, а **Y**— высоте.

Наконец мы прорисовываем каждую строку. Чтобы выровнять текст горизонтально по центру, нужно присвоить переменной **X** его вектора положения значение **screenWidth / 2 - textSize.X / 2**.

**Задача:** как изменить описанную процедуру, чтобы выровнять текст по центру не только горизонтально, но и вертикально?

Запустите игру. Вы видите вводный экран-заставку? Обновляется ли результат каждый раз с появлением новых брокколи?

![Вводный экран-заставка](images/monogame-tutorial-3.png)

## <a name="collision-detection"></a>Обнаружение столкновений
Итак, нас повсюду атакуют брокколи, при появлении которых постоянно обновляется счетчик. Но пока эту игру невозможно проиграть. Нам нужен способ узнавать о столкновении динозавра с брокколи, в случае чего игра будет заканчиваться.

### <a name="1-rectangular-collision"></a>1. Столкновение прямоугольников
При обнаружении столкновения в игре объекты часто упрощаются, чтобы уменьшить сложность вычислений. При отслеживании столкновений между игровым персонажем и брокколи мы будем считать оба объекта прямоугольными.

Откройте **SpriteClass.cs** и добавьте новую переменную класса:

```CSharp
const float HITBOXSCALE = .5f;
```

От этого значения будет зависеть, насколько строго будут засчитываться столкновения. Если указать значение .5f, прямоугольник, в котором динозавр может столкнуться с брокколи— часто его называют "хитбокс", или "зона поражения",— будет занимать половину полного размера текстуры. В результате в некоторых случаях углы двух текстур будут сталкиваться даже при том, что изображения не будут соприкасаться никакими видимыми частями. Вы можете изменить это значение на свой вкус.

Далее добавьте в файл **SpriteClass.cs** новый метод:

```CSharp
public bool RectangleCollision(SpriteClass otherSprite)
{
  if (this.x + this.texture.Width * this.scale * HITBOXSCALE / 2 < otherSprite.x - otherSprite.texture.Width * otherSprite.scale / 2) return false;
  if (this.y + this.texture.Height * this.scale * HITBOXSCALE / 2 < otherSprite.y - otherSprite.texture.Height * otherSprite.scale / 2) return false;
  if (this.x - this.texture.Width * this.scale * HITBOXSCALE / 2 > otherSprite.x + otherSprite.texture.Width * otherSprite.scale / 2) return false;
  if (this.y - this.texture.Height * this.scale * HITBOXSCALE / 2 > otherSprite.y + otherSprite.texture.Height * otherSprite.scale / 2) return false;
  return true;
}
```

Этот метод обнаруживает столкновение двух прямоугольных объектов. В основе алгоритма лежит проверка, есть ли зазор между какими-либо из сторон двух прямоугольников. Если есть зазор, то столкновения нет. Если нет зазора, значит, должно быть столкновение.

### <a name="2-load-new-textures"></a>2. Загрузка новых текстур

Далее откройте файл **Game1.cs** и добавьте две новые переменные класса: одну для хранения текстуры спрайта для завершения игры, а вторую (логическое значение) для отслеживания состояния игры:

```CSharp
Texture2D gameOverTexture;
bool gameOver;
```

Затем инициализируйте **gameOver** в методе **Initialize**:

```CSharp
gameOver = false;
```

Наконец, загрузите текстуру в **gameOverTexture** в методе **LoadContent**:

```CSharp
gameOverTexture = Content.Load<Texture2D>("game-over");
```

### <a name="3-implement-game-over-logic"></a>3. Реализация логики завершения игры
Добавьте этот код в метод **Update** сразу после вызова метода **KeyboardHandler**:

```CSharp
if (gameOver)
{
  dino.dX = 0;
  dino.dY = 0;
  broccoli.dX = 0;
  broccoli.dY = 0;
  broccoli.dA = 0;
}
```

Это останавливает все движения после завершения игры— динозавр и брокколи застывают в текущем положении.

Затем в конце метода **Update**, непосредственно перед **base.Update(gameTime)**, добавьте следующую строку:

```CSharp
if (dino.RectangleCollision(broccoli)) gameOver = true;
```

Это вызывает метод **RectangleCollision**, который мы создали в **SpriteClass**, и, если возвращается значение true, указывает на окончание игры.

### <a name="4-add-user-input-for-resetting-the-game"></a>4. Добавление пользовательского ввода для сброса игры
Добавьте этот код в метод **KeyboardHandler**, чтобы разрешить пользователю сбрасывать игру нажатием клавиши ВВОД:

```CSharp
if (gameOver && state.IsKeyDown(Keys.Enter))
{
  StartGame();
  gameOver = false;
}
```

### <a name="5-draw-game-over-splash-and-text"></a>5. Отображение экрана-заставки и текста для завершения игры
Наконец, добавьте этот код в метод Draw сразу после первого вызова **spriteBatch.Draw** (это должен быть вызов, который рисует текстуру травы).

```CSharp
if (gameOver)
{
  // Draw game over texture
  spriteBatch.Draw(gameOverTexture, new Vector2(screenWidth / 2 - gameOverTexture.Width / 2, screenHeight / 4 - gameOverTexture.Width / 2), Color.White);

  String pressEnter = "Press Enter to restart!";

  // Measure the size of text in the given font
  Vector2 pressEnterSize = stateFont.MeasureString(pressEnter);

  // Draw the text horizontally centered
  spriteBatch.DrawString(stateFont, pressEnter, new Vector2(screenWidth / 2 - pressEnterSize.X / 2, screenHeight - 200), Color.White);
}
```

Здесь мы используем тот же метод, что и ранее для отображения текста горизонтально по центру (повторно используя шрифт для вводного экрана-заставки), для выравнивания по центру **gameOverTexture** в верхней части окна.

Все готово! Снова попробуйте запустить игру. Если вы следовали инструкциям выше, игра теперь заканчивается, когда динозавр сталкивается с брокколи, и игрок получает предложение перезапустить игру, нажав клавишу ВВОД.

![Игра закончена](images/monogame-tutorial-4.png)

## <a name="publish-to-the-microsoft-store"></a>Публикации в Microsoft Store
Поскольку мы создали эту игру как приложение UWP, можно опубликовать этот проект в Microsoft Store. Для этого нужно выполнить несколько шагов.

[Зарегистрируйтесь](https://developer.microsoft.com/en-us/store/register) в качестве разработчика приложений для Windows.

Заполните [контрольный список](https://docs.microsoft.com/en-us/windows/uwp/publish/app-submissions) для отправки приложения.

Отправьте приложение на [сертификацию](https://docs.microsoft.com/en-us/windows/uwp/publish/the-app-certification-process).

Дополнительные сведения см. в разделе [Публикация вашего приложения UWP](https://developer.microsoft.com/en-us/store/publish-apps).
