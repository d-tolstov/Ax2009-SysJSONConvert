# Конвертация объектов Ax2009 в JSON
В аксапте нельзя очень просто сериализовать любой класс, как это например делается в `C#` с помощью пакета `NewtonJSoft`.  
Поэтому приходится извращаться.

### Цель
Представить в формате `JSON` данные из стандартных классов типа `Struct`, `Set`, `List`, `Container`. Если они включают в себя какие-то нестандартные классы, то облегчить сериализацию нестандартных классов с помощью реализации в них метода с предопределённым названием (в нашем случае - `json()`).

### Два вида структур данных в `JSON`
В `JSON` два вида структур данных :
* коллекция key/value пар
* массив

Распределение стандартных классов Ax2009 по видам структур :  
|Структура              |Классы                                         |
|-----------------------|-----------------------------------------------|
|Коллекция key/value пар|`Struct`<br>`Map` (если key имеет тип `String`)|
|Массив                 |`Set`<br>`List`<br>`Container`                 |

### null
В `null` конвертируются только неинициализированные объекты типа `Class`.  
Неинициализированные примитивы выводятся в виде следующих значений.

|Примитив                            |Неинициализированное значение         |
|------------------------------------|--------------------------------------|
|`String`<br>`Date`<br>`utcDateTime` |""                                    |
|`Int`<br>`Int64`<br>`Enum`<br>`Real`|0                                     |
|`Guid`                              |"00000000-0000-0000-0000-000000000000"|

### boolean
Значения типа `boolean` выводятся как `int` - в виде 0 или 1.

### Вызов сериализации
```
    SysJSONConvert::construct().SerializeObject( object);
```

Пример реализации функции `json()` в классах :  
1. Если на выходе хотим получить collection of name/value pairs :
```
    str json()
    {
        return SysJSONConvert::construct()
            .WithKeyValue( "field1", fieldValue1)
            .WithKeyValue( "field2", fieldValue2)
            .json()
        ;
    }
```
2. Если класс по сути является массивом (например, обёрткой для `Set`, `List` и т.д.) и на выходе хотим получить ordered list of values :
```
    str json()
    {
        return SysJSONConvert::construct()
            .SerializeObject( valueSet)
        ;
    }
```

### Зависимости от других репозиториев
* [SysEnumerators](https://github.com/mazzy-ax/SysEnumerators)
