## Базовое устройство

Для создания пользовательского интерфейса используется наследование от класса Screen - сначала вы создаёте свой дочерний класс с экраном, а затем заменяете текущий экран клиента на объект созданного дочернего класса.

К примеру, вы можете создать 2 файла - MyScreen и [ClientEvents](Подписки%20на%20события.md)
MyScreen:
```java
public class MyScreen extends Screen {// наследуем от Screen  
  
    public MyScreen(Component pTitle) {  
        super(pTitle);  
    }  
  
    @Override  
    protected void init() { // Вызывается при создании экрана и при масштабировании окна с игрой  
        super.init();  
    }  
  
    @Override  
    public void render(GuiGraphics pGuiGraphics, int pMouseX, int pMouseY, float pPartialTick) { // Вызывается каждый кадр  
        this.renderBackground(pGuiGraphics); // Задний фон как в настройках и других ванильных экранах  
        // Рисуем заголовок вверху
        pGuiGraphics.drawString(  
            this.font, // параметры шрифта  
            this.title, // Заголовок - то, что мы передаём в конструктор  
            this.width / 2 - this.font.width(this.title) / 2, // расположение теста по X  
            20,   // расположение текста по Y   
			0xFFFFFF, // цвет  
            false     // тень от текста  
        );  
        super.render(pGuiGraphics, pMouseX, pMouseY, pPartialTick);  
    }  
}
```
ClientEvents:
```java
@Mod.EventBusSubscriber(modid = "myfirstmod", value = Dist.CLIENT)  
public class ClientEvents {  
    @SubscribeEvent  
    public static void onKeyInput(InputEvent.Key event) {  
        Minecraft mc = Minecraft.getInstance();  
        if (event.getKey() == InputConstants.KEY_N)  {  // при нажатии N
            mc.setScreen(new MyScreen(Component.literal("Мой экран"))); // заменяем текущий экран на объект нашего 
        }  
    }  
}
```

После запуска игры и нажатия на **N** вы увидите примерно это:
![img](https://github.com/35mt/Ru-Minecraft-1.20.1-forge-modding-tutorial/blob/main/Системное%20хранилище/Pasted%20image%2020260519222917.png)

Теперь разберём код: 
В вашем классе существует 2 главных метода которые вы можете переопределить:
- **init** - вызывается при создании и масштабировании экрана - super метод вызывается **перед** вашим кодом 
- **render** - вызывается каждый клиентский кадр - super метод вызывается **после** вашего кода 
При этом в render, из неочевидного, передаётся объект **GuiGraphics** - он предоставляет высокоуровневые инструменты для отрисовки текста (как в примере) и простых геометрических фигур, а так-же текстур. 

Так-же стоит упомянуть при метод isPauseScreen():
```java
@Override  
public boolean isPauseScreen() {  
    return false; // Определяет, будет ли игра ставиться на паузу в одинчке: true - да, false - нет (стандартно - true)
}
```

## ⚠️ ВНИМАНИЕ !!!

Если ваш экран ничего не переопределяет и не создаёт задний экран, а так-же метод isPauseScreen не переопределён - при открытии экрана может показаться что игра зависла, сам споткнулся и потерял самые тупые 15 минут в жизни
## Создание кнопок

Для создания своих кнопок используется метод addRenderableWidget().
Теперь добавим этот метод в **init**:
```java
@Override  
protected void init() { // Вызывается при создании экрана и при масштабировании окна с игрой  
    super.init();  
  
    Component text = Component.literal("Закрыть меню");  
    int buttonWidth = this.font.width(text) * 2;  
  
    this.addRenderableWidget(Button.builder(  
            text, // текст кнопки  
            (b) -> this.onClose() // колбэк функция, активируемая при нажатии кнопки  
    )  
                    .bounds(this.width / 2 - buttonWidth / 2, 50, buttonWidth, 20) // позиция и размер кнопки  
                    .build()  
    );  
}
```
Теперь на вашем экране появиться кнопка:
![img](https://github.com/35mt/Ru-Minecraft-1.20.1-forge-modding-tutorial/blob/main/Системное%20хранилище/Pasted%20image%2020260520033352.png)

Очевидно, эта кнопка закрывает этот экран. Естественно, вы можете прикрутить туда любую другую логику.

