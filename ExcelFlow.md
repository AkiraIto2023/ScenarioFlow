# ExcelFlow

`ScenarioFlow` provides `TsvSceanrioPublisher` as a standard Scenario Publisher, and we can use UTF-8 encoding .txt file to create a Scenario Book with it.
Also, we discussed that we can edit a .txt file by spreadsheet applications for efficiency.

However, a smarter way is to import a spreadsheet and edit it directly.

`ExcelFlow` supports `Shift-JIS` encoding .xlsx file, where the .xlsx file is imported as an instance of `ExcelAsset` class.

## Get Ready for ExcelFlow

After you imported `ExcelFlow` via the .unitypackage file, you have to resolve two dependencies at first. Actually, `ExcelFlow` depends on [ExcelDataReader](https://github.com/ExcelDataReader/ExcelDataReader) to interpret .xlsx file and [System.Text.Encoding.CodePages](https://www.nuget.org/packages/System.Text.Encoding.CodePages/) to handle Shift-JIS encoding file.

In Unity, it is little complicated to install NuGet packages, but you can easily install the two packages via [NuGet importer for Unity](https://github.com/kumaS-nu/NuGet-importer-for-Unity). However, note that it doesn't necessarily mean that you must use it.

Make sure that you can import .xlsx file into Unity before you go to the next section.

## The Structure of `ExcelAsset`

A .xlsx file is imported as an instance of the `ExcelAsset` class by `ExcelFlow`. Then, the structure of the `ExcelAsset` class is shown below:

```cs:ExcelAsset.cs
namespace ScenarioFlow.ExcelFlow
{
    public class ExcelAsset : MonoBehaviour
    {
        public Table DataTable;

        public ExcelAsset(Table dataTable)
        {
            this.DataTable = dataTable;
        }

        [Serializable]
        public class Table
        {
            public Line[] Lines;

            public Table(IEnumerable<Line> lines)
            {
                this.Lines = lines.ToArray();
            }
        }

        [Serializable]
        public class Line
        {
            public string[] Texts;

            public Line(IEnumerable<string> texts)
            {
                this.Texts = texts.ToArray();
            }
        }
    }
}
```

You may think it complicated because it has the two inner classes, `Table` and `Line`. However, it is very simple design in practice. At first, `ExcelAsset` has the array of the `Line` class. It corresponds to rows in a spreadsheet. Then, `Line` has the array of `string` named `Texts`, and it corresponds to elements in a row in a spreadsheet. Therefore, we can access the element in column 0 of row 3 in a spreadsheet by `excelAsset.DataTable.Lines[3].Texts[0]`.

However, note that we don't have to know about the structure in detail as long as we just use it for creating a Sceanrio Book. For the `ExcelScenarioPublisher` class is responsible for converting `ExcelAsset` into `ISceanrioBook`.

## The Grammar of `ExcelScenarioPublisher`

`ExcelFlow` provides the `ExcelScenarioPublisher` class that implements the `IScenarioPublisher<ExcelAsset>` interface as a Scenario Publisher. It means that the `ExcelScenarioPublisher` is able to convert `ExcelAsset` into `IScenarioBook`.

Since a .xlsx file is automatically converted into `ExcelAsset` by the `ExcelAssetImporter` class, only thing we have to do is to write a story script on a .xlsx file.

However, the grammar of `ExcelScenarioPublisher` is almost the same as `TsvSceanrioPublisher` so that there are very little things to learn. Compare the .txt file called "SampleSceanrio.txt" to a spreadsheet, where both two files are converted into the same Scenario Book.

```txt:SampleScenario.txt
<Page>
log.message	Page 0 is opened.
log.message	What is the count now?
log.count
</Page>

<Page>
log.message	Page 1 is opened.
log.message	1 will be set to the count.
set.count	1
log.message	What is the count now?
log.count
</Page>

<Page>
log.message	Page 2 is opened.
log.message.rep	Hello, ScenarioFlow!	4
log.message	This is last sentence. See you again!
</Page>
```

`SampleScenario.xlsx: `

![SampleScenario_Excel](https://github.com/AkiraIto2023/ScenarioFlow/blob/main/Images/SampleScenario_Excel.jpg)

That is, elements are separated by the cell in `ExcelScenarioPublisher` while elements are separeted by the Tab in `TsvScenarioPublisher`.

## Publish Scenario Book by `ExcelScenarioPublisher`

The constructor of the `ExcelSceanrioPublisher` has the same parameter as `TsvScenarioPublisher`. So we can publish a Scenario Book as follows:

```cs
IScenarioPublisher<ExcelAsset> scenarioPublisher = new ExcelScenarioPublisher(
    new ScenarioMethodSeacher(
        new IReflectable[]
        {
            this,
            new SimpleCounter(),
            new PrimitiveDecoder()
        }));

IScenarioBook scenarioBook = scenarioPublisher.Publish(excelAsset);
```

Also, note that the `ExcelAsset` class is *serializable* so that we can add `SerializeField` attribute to a field of `ExcelAsset` and attach an instance to it.

```cs
[SerializeField]
private ExcelAsset excelAsset;
```