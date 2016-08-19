# Excel parser for OpenCart

Приложения для обработки прайс-листов, для последующей загрузки в интернет магазина разработанный на OpenCart.

## Технические требования

* Windows 7 или выше
* Для сборки и работы приложения необходим установленный Microsoft Office 2007 или выше.
* Среда для разработки Visual Studio 2013/2015 (C# 5.0/.NET 4.5)

## Основной дизайн и бизнес-логика программы обработчика прайсов:

1. Программа может открывать прайс-листы
2. Определять тип прайс-листы
3. Выполнять анализ выбранного прайс-листы в зависимости от типа.
4. После анализа заполнять шаблон ( который находится вот здесь `ExcelParserForOpenCart/Output/Debug/template.xls` или в папке Release и сохранять обработанный прайс лист под именем, который задаёт пользователь.

## Необходимо сделать в общем (при обработки любого нового прайса) :

1. Научить программу определять тип нового прайса, если есть необходимость добавить тип в перечисление  `ExcelParserForOpenCart/UI/EnumPrices.cs`. Определение происходит по уникальниы меткам или уникальной комбинации этих меток.
Определение типа прайса происходит с помощью метода _DetermineTypeOfPriceList_, который находится в файле
 `ExcelParserForOpenCart/UI/ExcelParser.cs` Определение типа происходит по уникальной метке или уникальной комбинации меток, расположенных на листах прайса.
2. Разработать алгоритм для анализа прайса.
Анализ прайса представляет собой обработку структуры прайса и приведение к списку, содержащему класс:
 `ExcelParserForOpenCart/UI/OutputPriceLine.cs`
3. Для того, что бы добавить новый анализатор для обработки нового типа прайса необходимо создать новый класс в папке _Prices_ проекта  _ExcelParserForOpenCart_, для удобства был реализован класс с общими методами _GeneralMethods.cs_, для удобства его можно унаследовать.
4. Название нового класса соответствует краткому названию анализируемого прайса.
5. После этого можно использовать новый класс в _ExcelParser.cs_ в методе _DoWorkOpen_

#### Рекомендуемый шаблон для анализатора

```cs
    using System.ComponentModel;
    using Microsoft.Office.Interop.Excel;

    public class НазваниеАнализатора : GeneralMethods
    {
        public НазваниеАнализатора(object sender, DoWorkEventArgs e)
        {
            Worker = sender as BackgroundWorker;
            E = e;
        }

        public void Analyze(int row, Range range)
        {
            if (Worker.CancellationPending)
            {
                E.Cancel = true;
                return;
            }
            // цикл для обработки прайс-листы
            for (var i = 7; i < row; i++)
            {
                if (Worker.CancellationPending)
                {
                    E.Cancel = true;
                    ResultingPrice.Clear();
                    break;
                }
                // выйти из цикла необходимо с помощью оператора break
            }
        }
    }
```

### Нюансы:

Артикул прайса ключевой атрибут сущности товар в базе данных интернет магазина, в который мы загружаем данные прайсы, он должен быть уникальным и алгоритм его формирования должен быть всегда однозначным, т. е. прайслист будут загружать с определённым периодом, по артиклу будет происходить обновление товара.

### Примеры прайсов для обработки:

[PriceExample.zip](https://app.box.com/s/phl77vc86kz1483r35qelsrvj7q1jl4m)

### Пример выходного прайса:

[OutPrice.zip](https://app.box.com/s/icxt0t1yo3boi9qk3zsbz5qybt2y39g3)