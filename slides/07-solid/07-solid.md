# SOLID: Object-Oriented Design Principles

<!-- TODO
  - Можно переупорядочить принципы (например OCP рассказывать после DIP)
  - На месте ли закон Деметера? Привести пример на закон одной точки
  - Object Calisthenics <http://habrahabr.ru/post/206802/>
-->

# Признаки "гниющего" кода

  - Жесткость (Rigidity)
    - Единственное изменение вызывает каскад изменений в других модулях
  - Хрупкость (Fragility)
    - Свойство программы повреждаться во многих местах при внесении
      единственного изменения
  - Неподвижность (Immobility)
    - Сложность переиспользования кода в других системах / подсистемах
  - Вязкость (Viscosity)
    - Когда неправильное решение дешевле правильного
    - Вязкость программы и окружения
  - Ненужная сложность
  - Ненужные повторения
  - Непрозрачность

# SOLID principles

  - Single Responsibility Principle
  - Open/Closed Principle
  - Liskov Substitution Principle
  - Interface Segregation Principle
  - Dependency Inversion Principle

# Single Responsibility Principle

![](./images/srp.png)

# Single Responsibility Principle

> Программные сущности должны иметь только одну ответственность.

> Не должно быть больше одной причины для изменения класса.

# Пример

![](./images/e1.png)

# Пример

![](./images/e0.png)

# SOLID principles

  - Single Responsibility Principle
  - <font color=red>Open/Closed Principle</font>
  - Liskov Substitution Principle
  - Interface Segregation Principle
  - Dependency Inversion Principle

# Open/Closed Principle

![](./images/ocp.jpg)

# Open/Closed Principle

> "Программные сущности должны быть открыты для расширения,\
> но закрыты для изменения."

Б. Мейер, 1988 / Р. Мартин, 1996

# Open/Closed Principle

![](./images/e2.png)

# Пример c явной зависимостью

```java
ReportFormat format = TXT;
Reporter reporter(format);
...
ReportType type = QUARTERLY;
reporter.report(type);
```

```java
class Reporter {
    TxtPrinter txtPrinter;
    XmlPrinter xmlPrinter;

    Reporter(ReportFormat) {
        if (format == TXT)
            txtPrinter = new TxtPrinter();
        else if (format == XML)
            xmlPrinter = new TxtPrinter();
    }

    void Report(ReportType type) {
        if (type == QUARTERLY) {
            QuarterlyReport report = new QuarterlyReport();

            if (txtPrinter)
                txtPrinter.print(report);
            else if (xmlPrinter)
                xmlPrinter.print(report);
        }
        else if (type == ANNUAL) {
        ...
    }
}
```

# Пример с неявной зависимостью

```java
IPrinter printer = new TxtPrinter();
Reporter reporter(printer);
...
IReport report = new AnnualReport();
reporter.report();
```

```java
class Reporter {
    IPrinter printer;

    Reporter(IPrinter printer) {
        this.printer = printer;
    }

    void Report(IReport report) {
        printer.print(report);
    }
}
```

# Стратегическое закрытие

> "Ни одна значительная программа не может быть на 100% закрытой"

Р. Мартин, 1996

# Полезные советы

  - Данные не в public полях
  - Нет глобальным переменным
  - Скрываем неподдерживаемую функциональность
  - Проверка типов времени исполнения (RTTI) опасна

# Закон Деметеры

> - Метод М объекта О имеет право вызывать методы только у:
    - аргументов М
    - объектов, созданных внутри М
    - самого О
    - объектов-членов О
    - глобальных переменных, доступных О, в пределах М
  - Вопрос: а чего делать нельзя?
  - Закон одной точки
    - `a.Method()`, но не `a.b.Method()`
    - `player.Accounts[0].Balance.Sum(a => a.ConvertTo(“RUR”));`
    - `player.AvailableCash()`

# Закон Деметеры

 - Аналогия из жизни: eсли Вы хотите, чтобы собака побежала, глупо командовать
   ее ногами, лучше отдать команду собаке, а она уже разберётся со своими ногами
   сама.
 - Объект должен иметь как можно меньше представления о структуре и свойствах
   чего угодно (включая собственные подкомпоненты).
 - Закон Деметры является специальным случаем слабой связанности (Loose
   coupling).
 - Метафора про подлодку и шпионов.

Wikipedia

# SOLID principles

  - Single Responsibility Principle
  - Open/Closed Principle
  - Liskov Substitution Principle
  - Interface Segregation Principle
  - <font color=red>Dependency Inversion Principle</font>

# Dependency Inversion Principle

![](./images/dip.png)

# Dependency Inversion Principle

> Абстракции не должны зависеть от деталей.\
Детали должны зависеть от абстракций.

# Пример

``` java
enum OutputDevice {PRINTER, DISK};

void Copy(OutputDevice device) {
    int c;
    while ((c = ReadKeyboard()) != EOF)
        if (device == PRINTER)
            WritePrinter(c);
        else
            WriteDisk(c);
}
```

# Пример

``` java
void Copy(Writer writer) {
    int c;
    while ((c = ReadKeyboard()) != EOF) {
        writer.Write(c);
    }
}
```

# Пример

```java

public interface IReader {
   char Read();
}
public interface IWriter {
   void Write(char c);
}

void Copy(IReader reader, IWriter writer) {
    int c;
    while ((c = reader.Read()) != EOF) {
        writer.Write(c);
    }
}
```

В чем преимущества такого подхода?

# Procedural vs. OO Architecture

Procedural Architecture\
![](./images/pa.png)

Object-Oriented Architecture\
![](./images/ooa.png)

# Внедрение зависимости (Dependency Injection)

  - Внедрение зависимости — процесс предоставления внешней зависимости
    программному компоненту.
  - Часть объектов в программе по-прежнему создается обычным способом языка
    программирования, часть  — создается контейнером на основе предоставленной ему
    __конфигурации__.
  - Конфигурационный файл описывает какие конкретно реализации должны быть
    инициализированы. В программный код изменения не вносятся!

Wikipedia

# Пример (создание вручную)

```java
public class MyApplication
{
    public static void main(String[] args)
    {
        // Явное конструирование
        ICar car = new DefaultCarImpl();

        // Использование
        car.setPedalPressure(5);
        float speed = car.getSpeed();
    }
}
```

# Пример (с использованием DI-фреймворка)


```java
public class MyApplication
{
    public static void main(String[] args)
    {
        // Неявное конструирование
        Service service = (Service)DependencyManager.get("CarBuilderService");
        ICar car = (ICar)service.getService(Car.class);

        // Использование
        car.setPedalPressure(5);
        float speed = car.getSpeed();
    }
}
```

Конфигурационный файл

```xml
    <service-point id="CarBuilderService">
        <invoke-factory>
            <construct class="Car">
                <service>DefaultCarImpl</service>
                <service>DefaultEngineImpl</service>
            </construct>
        </invoke-factory>
    </service-point>
```

# SOLID principles

  - Single Responsibility Principle
  - Open/Closed Principle
  - Liskov Substitution Principle
  - <font color=red>Interface Segregation Principle</font>
  - Dependency Inversion Principle

# Interface Segregation Principle

![](./images/isp.png)

# Interface Segregation Principle

> "Клиенты не должны зависеть от интерфейсов, которые они не используют"

Р. Мартин, 1996

# Пример

![](./images/e4.png)

Пример `namespace utils`.

# Пример

Cлишком «толстые» интерфейсы необходимо разделять на более маленькие и
специфические.

![](./images/e3.png)

# SOLID principles

  - Single Responsibility Principle
  - Open/Closed Principle
  - <font color=red>Liskov Substitution Principle</font>
  - Interface Segregation Principle
  - Dependency Inversion Principle

# Liskov Substitution Principle

![](./images/lsp.jpg)

# Композиция vs Наследование

  - По прошествии лет люди осознали, что\
    __вреда от наследования обычно больше, нежели пользы__,\
    и на самом деле наследование нужно лишь в исключительных случаях
  - Наследование
    - Не должно является способом переиспользования кода
    - Должно обеспечивать полиморфное использование (DIP)
  - Коротко
    - Предпочитайте композицию наследованию
    - Старайтесь ограничиться реализацией интерфейсов
  - Критерии корректности наследования
    - "Is A" критерий
    - LSP принцип является важнейшим критерием\
      для оценки качества принимаемых решений\
      при построении иерархий наследования.

# Liskov Substitution Principle

> "Наследование должно гарантировать,\
> что любое свойство, справедливое для супертипа,\
> должно быть справедливо и для наследников."

Б. Лисков, 1987

![](./images/bliskov.png)

# Пример

Базовый класс

``` java
class Bird {                    // Есть клюв, крылья...
    public virtual void fly();  // Птица может летать
};
```

Наследник 1

``` java
class Parrot : Bird {     // Попугай – птица
    public override void fly() { ... }
};
```

Наследник 2

``` java
class Penguin : Bird {
    public override void fly() {
        throw "Пингвины не летают!";
    }
};
```

# Пример

``` java
void PlayWithBird (Bird bird) {
    bird.Fly(); // OK если попугай, но будет ошибка если будет передан пингвин!
}
```

> - Проблема в том, что из-за неправильного наследования ранее корректный код
    становится некорректным!
  - Что нужно сделать, чтобы код стал корректным с такой иерархией наследования?\
    Чем плохо такое "решение"?

# Пример — решение

``` java
class Bird {
    // есть клюв, крылья...
};

class FlyingBird : Bird {
    public virtual void fly();  // птица может летать
};
```

``` java
class Parrot : FlyingBird {     // Попугай – летающая птица
 public override void fly() { …  }
};
```

``` java
class Penguin : Bird {
    // ...
};
```

# Задача: квадрат — это прямоугольник?

``` java
class Rectangle {
    private double width;
    private double height;

    public void SetWidth(double w);
    public void SetHeight(double h);
    public double GetWidth();
    public double GetHeight();

    public double GetArea();
}
```

``` java
class Square : Rectangle {...} // Стоит ли так делать?
```

``` java
void Adjust(Rectangle rect)
{
  rect.setWidth(5); rect.setHeight(4);
  double area = rect.GetArea(); // Какая будет площадь?
}
```

# Применяй SOLID!

![](./images/use_solid.jpg)

# Спасибо!

Вопросы?
