# Scenario Flow: Realizing Your Fantastic Story in Unity Engine

## Table of Contents
- [Scenario Flow: Realizing Your Fantastic Story in Unity Engine](#scenario-flow-realizing-your-fantastic-story-in-unity-engine)
  - [Table of Contents](#table-of-contents)
- [Introduction](#introduction)
  - [Who uses Scenario Flow?](#who-uses-scenario-flow)
  - [Embedding A Story on A Game](#embedding-a-story-on-a-game)
  - [Motivation of Using Scenario Flow](#motivation-of-using-scenario-flow)
- [Hello World Example](#hello-world-example)
- [Preparation of Scenario Method](#preparation-of-scenario-method)
  - [1st step: Attach `ScenarioMethod` attribute](#1st-step-attach-scenariomethod-attribute)
    - [`alias`](#alias)
    - [`description`](#description)
  - [2nd step: Implement Decoder methods](#2nd-step-implement-decoder-methods)
  - [3rd step: Make classes implement the `IReflectable` interface](#3rd-step-make-classes-implement-the-ireflectable-interface)
  - [Scenario Method Table](#scenario-method-table)
- [Write, Publish, and Read Scenario Book to Invoke Scenario Method](#write-publish-and-read-scenario-book-to-invoke-scenario-method)
  - [The Structure of Scenairo Book](#the-structure-of-scenairo-book)
    - [Extensions](#extensions)
    - [Make The Best Use of *Fluent Interface*](#make-the-best-use-of-fluent-interface)
  - [Write Scenario Book](#write-scenario-book)
    - [The Format of `TsvScenarioPublisher`](#the-format-of-tsvscenariopublisher)
    - [The Grammar of `TsvScenarioPublisher`](#the-grammar-of-tsvsceanriopublisher)
  - [Publish Scenario Book](#publish-scenario-book)
  - [Read Scenario Book](#read-scenario-book)

# Introduction

## Who uses Scenario Flow?

These days, the world is overflowing with various kinds of games, for example, action, puzzle, fighting, etc. And many of them contain a common factor.

It is a story. A game players think it wonderful often contains a exciting, emotional, or enthusiastic story, which enhances quality of the game. In general, there are not very many games that people play just for reading the embedded story, it is certain that the more fantastic an embedded story is, the more amazing a game is, however.

Then, the developer who want to embed a story on their game may implement many classes and methods to manage the story data, and they may want to read the data and call the methods in order so that the story is progressed. Scenario Flow helps a developer to do such operations. Actually, Scenario Flow analyzes what method is called and how order is, and call methods in proper order a developer hopes. The developer can use .txt files or optional .xlsx files to manage the story data that contains what method is called and its order. That is, they can call implemented methods from the outside of c# script files freely. 

Using Scenario Flow, the developer can easily control "scenario flow," for example, displaying characters' conversation, changing character's sprite image, making a sound, and so on.

## Embedding A Story on A Game

"Little Red Riding Hood" is one of the most famous fairy tales. The story is roughly as follows:

1. Once upon a time there was a dear little girl who always wore a little cap of red velvet given by her grandmother. So She was called "Little Red Riding Hood."
2. One day, she was going to visit her grandmother because her mother said to her, "Your grandmother is ill and weak, so take a piece of cake and a bottle of wine to her."
3. On her way, Little Red Riding Hood unfortunately encountered a suspicious wolf. Despite of her mother's advice, she told wolf her grandomother's place, and dropped by a flower garden.
4. When Little Red Riding Hood got to her grand mother's house, her grandmother was already devoured by the wolf. Soon after that, Little Red Riding Hood was devoured, too.
5. However, fortunately, a huntsman was just passing the house. He saved Little Red Riding Hood and her grandmother. 
6. Finally, Little Red Riding Hood fetched great stones with which they filled ther wolf's belly.  The wolf collapsed at once because the stones were so heavy, and fell dead.

This fairy tale is very famous, so probably many people read this story when they were a child.

By the way, by what media did you know "Little Red Riding Hood" story? It is most likely a picture book, and following is probably a movie. Then, have you ever seen or played any video games with a Little Red Riding Hood theme? If you make such a game, how should it be realized? What is required?

Someone may imagine an action game in which a player operates Little Red Riding Hood as a player character, and the player take her to the grandmother's house while avoiding danger. Someone else may imagine a puzzle game in which a player solves mystery to escape from a forest on the way to the grandmother's house. Or someone else even imagine a fighting game in which a huntsman fights to the wolf to save Little Red Riging Hood and the grandmother.

Then, what do they require in common? Do they have same functions that have to be implemented? One of the answers is, funtions to embed the story on the game. For example, in all types of game described above, showing the characters' conversation is indispensable to tell the story to players playing the game. Also, to express the character's emotion, their sprite image should be displayed and changed depending on the situation. Furthermore, appropriate BGM enhances the story's fun, which is a big advantage that a picture book doesn't have.

In next section, let's try realizing such functions.

## Motivation of Using Scenario Flow

First we're going to do is to make a data object for managing story progression. The data object must have an character name that indicates who is speaking and a line that indicates what is spoken so that a player can watch characters' conversation.

```cs:ScenarioData.cs
    public class ScenarioData
    {
        public string CharacterName;

        public string Line;
    }
```

Then, we implement the code as follows:

```cs:ScenarioManager.cs
    public class ScenarioManager
    {
        public void ReadScenario(ScenarioData scenarioData)
        {
            //Display CharacterName and Line
        }
    }
```

Now we can use `ScenarioData[]` as a story script. And scenario data is reflected by `ReadScenario(ScenarioData scenarioData)` method. So next we're going to do is to make a BGM sound and to change it depending on the story's situation. 

```cs:ScenarioData2.cs
    public class ScenarioData2
    {
        public string CharacterName;

        public string Line;

        public AudioClip BgmClip;
    }
```

```cs:ScenarioManager2.cs
    public class ScenarioManager2
    {
        public void ReadScenario(ScenarioData2 scenarioData)
        {
            //Display CharacterName and Line

            if (scenarioData.BgmClip != null)
            {
                //Change BGM
            }
        }
    }
```

`BgmClip` can be `null` and if it is not `null`, BGM will be changed to given BGM soon. Now we can make a BGM sound and the story become more exciting.

Then, how is BGM stopped? Even if `BgmClip` is `null`, BGM will not be stopped because whether `BgmClip` is `null` or not just decides whether BGM is changed or not. So we have to add boolean flag like `DoesStopBGM` to `ScenarioData`. We ended up adding a new element to the data structure again, now the number of element is four, `CharacterName`, `Line```, `BGMClip`, and `DoesBgmStop`.

Unfortunately, we're not done yet. There still be a lot of things to realize, for example, to adjust the volume of BGM, to display and change character's sprite image, to set scenery image behind the characters, and so on. Do we add extra elements to `ScenarioData` to realize those functions? Needless to say, we don't. We don't want to do such tasks every time new functions is added. Also, this implementation is very bad design because the more functions there are, the more responsibility `ScenarioManager` has. The code in `ReadScenario(ScenarioData)` will be too long and filled with a large amount of branches in short time. In addition, `ReadScenario` method will be rewritten frequently due to modification of specification.

**It's time to use Scenario Flow.** In Scenario Flow, `ScenarioBook`  is used for story data management instead of `ScenarioData`. `ScenarioBook` contains an array of `ScenarioPage`, and `ScenarioPage` contains an array of `ScenarioSentnece`. Then, `ScenarioSentence` is responsible only for executing tasks to progress the story like displaying characters' conversation, changing character's sprite image, and making a sound. Such tasks are called "Scenario Method," and each changes to Scenario Method don't cause changes to `ScenarioBook`, `ScenarioPage`, and `ScenarioSentence`. 

From next section, we are going to learn how to use Scenario Flow, and the reader will realize why it is a strong tool for embedding a story on a game.

# Hello World Example

Let's output "Hello World" message to the console in Unity to begin with. Follow the procedure below.

1. Create a new unity project, and import Scenario Flow by .unitypackage file.
2. Create a new c# script file called "ScenarioManager.cs" and new empty object called "ScenarioManager," and attach the script file to the object.
3. Create a new UTF-8 text file called "HelloWorld.txt," then it will be imported as `TextAsset` so that you will be able to attach a reference to it to the property of `ScenarioManager` component.
4. Play, and "Hello, World!" message will be output to the console.

```cs:ScenarioManager.cs
using ScenarioFlow;
using UnityEngine;

public class ScenarioManager : MonoBehaviour, IReflectable
{
    [SerializeField]
    private TextAsset storyScript;

    private void Start()
    {
        IScenarioPublisher<TextAsset> scenarioPublisher = new TsvScenarioPublisher(
            new ScenarioMethodSeacher(
                new IReflectable[] { this }));

        IScenarioBook scenarioBook = scenarioPublisher.Publish(storyScript);

        IScenarioPage scenarioPage = scenarioBook.OpenTo(0).ReadPage();

        IScenarioSentence scenarioSentence = scenarioPage.PointTo(0).ReadSentence();

        scenarioSentence.OnRead();
    }

    public object Reflect()
    {
        return this;
    }

    [ScenarioMethod("log.message")]
    public void LogMessage(string message)
    {
        Debug.Log(message);
    }

    [Decoder]
    public string StringDecoder(string source)
    {
        return source;
    }
}
```

```txt:HelloWorld.txt
//All lines are "tab" separated, NOT blank!! (This line doesn't have to be written)
<Page>
log.message	Hello, World!
</Page>
```

![HelloWorld Result](https://github.com/AkiraIto2023/ScenarioFlow/blob/main/Images/HelloWorldResult.jpg)

## Note
 + The character code of the text file **MUST BE UTF-8** encoding. A Shift-JIS encoding text file is not interpreted correctly, for example.
+ Lines in the text file are all "tab" separated. Don't use blank for the separation.
+ These restrictions are due to `TsvScenarioPublisher`. If you want to use text files written in other character code like "Shift-JIS," or if you want to use format other than .txt, you can make new "SceanrioPublisher" as you like by implementing `IScenarioPublisher<>`.
+ If you want to use excel file, you can import [`ExcelFlow`](ExcelFlow.md), which is an optional tool of `ScenarioFlow` and supports Shift-JIS .xlsx file.

# Preparation of Scenario Method

As you saw in previous [Hello World Example](#hello-world-example) chapter, Scenario Flow enables us to call a method
we implemented from the outside of a c# script file. Such methods are called "Scenario Method." In this chapter, we are going to learn how to prepare Scenario Method.

To call a method you implemented as a Scenario Method, you follow the procedure below.

1. Attach ScenarioMethod Attribute `[ScenarioMethod(string alias, string description)]` to methods you want to register as a Scenario Method. `alias` is used for calling the methods.
2. Implement methods that convert a string type into an other type, and attach Decoder Attribute `[Decoder]`. Such methods are called "Decoder," and Decoder must be prepared for all the types used for the parameter passed into the Scenario Method you use.
3. Make each of classes declaring one or more Scenario Method or Decoder implement the `IReflectable` interface, and implement the `object Reflect()` method that returns a reference to an instance of the class.

Then, let's try declaring two methods in `SimpleCounter` class below as a Scenario Method:

```cs:SimpleCounter.cs
using UnityEngine;

public class SimpleCounter
{
    private int count = 0;

    public void SetCount(int n)
    {
        count = n;
    }

    public void LogCount()
    {
        Debug.Log($"The count is {count}!");
    }
}
```

These methods in the class are called like:

```cs:CounterManager.cs
using UnityEngine;

public class CounterManager : MonoBehaviour
{
    private void Start()
    {
        SimpleCounter simpleCounter = new SimpleCounter();

        simpleCounter.LogCount();

        simpleCounter.SetCount(1);

        simpleCounter.LogCount();

        simpleCounter.SetCount(4);

        simpleCounter.LogCount();
    }
}
```

![Counter Manger Result](https://github.com/AkiraIto2023/ScenarioFlow/blob/main/Images/SimpleCounterResult1.jpg)

## 1st Step: Attach `ScenarioMethod` Attribute

First we have to do is to attach `ScenarioMethod` attribute to the methods.

```cs:SimpleCounter.cs
using ScenarioFlow;
using UnityEngine;

public class SimpleCounter
{
    private int count = 0;

    [ScenarioMethod("set.count", "Change count to given number")]
    public void SetCount(int n)
    {
        count = n;
    }

    [ScenarioMethod("log.count")]
    public void LogCount()
    {
        Debug.Log($"The count is {count}!");
    }
}
```

Note that access modifiers of all the Scenario Methods must be `public`.

`ScenarioMethod` attribute has two parameters, `alias`and `description`. 

### `alias`

`alias` is the another name of the method, and Sceanrio Methods are called by the alias they have. For example, you write "set.count" on a text file to call `SetCount(int n)`, and you write "log.count" to call `LogCount()`.

 This name should be characteristic, and MUST be unique in all Scenario Methods because it is impossible to identify which method should be called if there are same `alias` methods.

### `description`

`description` is the explanation of a Scenario Method. This is optional, so you don't have to pass this parameter into the attribute necessarily. However, note that you can confirm information about existing Scenario Methods on a table of Scenario Methods (called "Scenario Method Table"). You can see it via menu item "Window/ScenarioMethodTable," and you will be able to read a document about Scenario Methods where there are descriptions you wrote. 

However, you are most likely to unable to see any information about `SimpleCounter` class's Scenario Methods now. That's because the class doesn't implement `IReflectable` interface. In 3rd step, we make `SimpleCounter` class implement the interface, and we will be able to see the information about the Scenario Methods on Scenario Method Table.

## 2nd Step: Implement Decoder Methods

Next, we are going to prepare Decoder methods that enable us to call Scenario Methods by text-based commands.

We used `TsvScenarioPublisher` to convert `TextAsset` to `IScenarioBook` in [Hello World Example](#hello-world-example) section. `IScenarioBook` is a data object that has information about what and when a Scenario Method is called, and the details about it are described later.

Then, the important thing is that since a text file has only `string` information, we have to call methods just by passing `string` parameters, even if the methods require other types of parameters like `int` and `float`.

So, must we use only `string` type as Scenario Method parameter type? - Of course, if we had to do that, this toolkit would be completely useless. Methods would lost reusability because Scenario Methods are used only as Scenario Method, and our program code would be filled with similar code that implements conversion logic.

We don't have to have such a restriction. Let's implement "Decoder" to solve this problem. Create a new file `PrimitiveDecoder.cs`, and write code as follows:

```cs:PrimitiveDecoder.cs
using ScenarioFlow;
using UnityEngine;

public class PrimitiveDecoder
{
    [Decoder]
    public int IntDecoder(string s)
    {
        if (int.TryParse(s, out int n))
        {
            return n;
        }
        else
        {
            Debug.LogWarning($"{s} is not number! Int Decoder returns 0");

            return 0;
        }
    }
}
```

`IntDecoder` receives only one `string` parameter, and returns an integer converted from the argument. If given string can't be converted into an integer, then a warning message is output, and zero is returned. 

Each types used in Scenario Method parameters must have its Decoder. Now, we implemented a Decoder for `int` type so that we can call `SetCount(int n)` that requires an `int` argument as a Scenario Method.

If you want to use `float` type, you must implement Decoder for `float`. And If you want to use `Foo` type, which is an user-defined class, you must implement Decoder for it.

After all, Decoder must follow four rules:
  1. Its access modifier is `public`.
  2. It has single `string` parameter.
  3. It returns a specific type value.
  4. It has `Decoder` attribute.

### Note
+ Even `string` type must have its Decoder although it just returns given argument directly.
+ Each types used in Scenario Method parameters must have its Decoder, but one is enough for it. Don't implement more than one decoder for a same type. 
+ As a suggestion, Decoder should be in class exclusively for it. Except is when there are any special reasons. Actually, [`TaskFlow`](TaskFlow.md), which is optional tool of `ScenarioFlow` and  enables us to manage asyncronous methods freely, has Decoder for `CancellationToken` that also has other than conversion logic.

## 3rd Step: Make Classes Implement The `IReflectable` Interface

Lastly, we are going to make all the classes that has any Scenario Methods or Decoders implement the `IReflectable` interface.

```cs:IReflectable.cs
namespace ScenarioFlow
{
    public interface IReflectable
    {
        object Reflect();
    }
}
```

 The  `IReflectable` interface declares the `object Reflect()` method. So class that implement the `IReflectable` interface must override that method.

 Now, we declared two classes, `SimpleCounter` and `PrimitiveDecoder`. Since they have Scenario Method or Decoder, they must implement the `IReflectable` interface and override the `object Reflect()` method.

 It is as easy as pie. We just have to write the template code below:

 ```cs
 public object Reflect()
{
        return this;
}
```

This just returns a reference to an instance of the class declaring this method. All we have to do is this.

Now, two classes `SimpleCounter` and `PrimitiveDecoder` are modified like:

```cs:SimpleCounter.cs
using ScenarioFlow;
using UnityEngine;

public class SimpleCounter : IReflectable
{
    private int count = 0;

    [ScenarioMethod("set.count", "Change count to given number")]
    public void SetCount(int n)
    {
        count = n;
    }

    [ScenarioMethod("log.count")]
    public void LogCount()
    {
        Debug.Log($"The count is {count}!");
    }

    public object Reflect()
    {
        return this;
    }
}
```

```cs:PrimitiveDecoder.cs
using ScenarioFlow;
using UnityEngine;

public class PrimitiveDecoder : IReflectable
{
    [Decoder]
    public int IntDecoder(string s)
    {
        if (int.TryParse(s, out int n))
        {
            return n;
        }
        else
        {
            Debug.LogWarning($"{s} is not number! Int Decoder returns 0");

            return 0;
        }
    }

    public object Reflect()
    {
        return this;
    }
}
```

That's all. We are ready to call Scenario Methods.

In next section, we are going to learn how to write "scenario script" to call these Scenario Methods in the order as you like.

## Scenario Method Table

Now, we should be able to see information about Scenario Method and Decoder we declared. Go to Scenario Method Table window via "Window/ScenarioMethodTable" on menu item.

![Scenario Method Table](https://github.com/AkiraIto2023/ScenarioFlow/blob/main/Images/ScenarioMethodTable.jpg)

# Write, Publish, and Read Scenario Book to Invoke Scenario Method

In previous chapter, we learned how to declare Scenario Method. Then, what we want to do next is to invoke Scenario Method in the order as you like.

 write up a script to line up Scenario Method in the order as you like, to convert the script into data object, and to invoke Scenario Method as you like by interpreting the data object.

To realize it, `ScenarioFlow` suggests a data strucure called "Scenario Book." In this section, we are going to learn how the structure of Scenario Book is, how to create Scenario Book, and how to invoke Scenario Method.

## The Structure of Scenairo Book

To learn the structure of Scenario Book, let's see the `IScenarioBook` interface, the `IScenarioPage` interface, and the `IScenarioSentence` interface that are provided by `ScenairoFlow` and follow the structure of Scenario Book.

```cs:IScenarioBook.cs
using System.Collections.Generic;

namespace ScenarioFlow
{
    public interface IScenarioBook
    {
        int MaxIndex { get; }

        int CurrentIndex { get; }

        IScenarioBook OpenTo(int n);

        IScenarioBook OpenLabel(string label);

        bool HasLabel(string label);

        IScenarioPage ReadPage();

        IEnumerable<IScenarioPage> ReadAll();
    }
}
```

```cs:IScenarioPage.cs
using System.Collections.Generic;

namespace ScenarioFlow
{
    public interface IScenarioPage
    {
        int MaxIndex { get; }

        int CurrentIndex { get; }

        IScenarioPage PointTo(int n);

        IScenarioSentence ReadSentence();

        IEnumerable<IScenarioSentence> ReadAll();
    }
}
```

```cs:IScenarioSentence.cs
namespace ScenarioFlow
{
    public interface IScenarioSentence
    {
        object OnRead();
    }
}
```

At first, relate the structure of Scenario Book to the structure of a real paper-book. 

Real paper-book consists of some pages, and the pages consists of some sentences. Then, each sentences indicate what the author would like to tell.

Scenario Book is very similar to it. `ScenarioBook` implementing the `IScenarioBook` interface has instances implementing the `IScenarioPage` interface, and `ScenarioPage` implementing the `IScenarioPage` interface has instances implementing the `IScenarioSentence` interfae. And `Scenario Sentence` implementing the `IScenarioSentence` interface indicates what Scenario Method is called and what arguments are passed.

More details are described below:
+ Scenario Book
  + `int MaxIndex`: This read-only property returns the maximum index of the Scenario Page that the Scenario Book has. But note that the index starts with `0`, so if a Scenario Book has three Scenario Pages, it returns `2`.
  + `int CurrentIndex`: This read-only property returns what number of the page is opened. Note that it start with `0`, so "page 4" in paper-book means "page 3" in Scenario Book.
  + `IScenarioBook OpenTo(int n)`: This is as well as we flip the paper-page. The nth Scenario Page is opened.
  + `IScenarioBook OpenLabel(string label)`: This is as well as we open the page with the bookmark. We can put "label" in the Scenario Book as well as we insert a bookmark in the paper-book. The Scenario Sentence bound to given label is selected.
  + `bool HasLabel(string label)` This simply returns whether the Scenario Book has given label or not.
  + `IScenarioPage ReadPage()`: This returns the Scenario Page that is currently opened.
  + `IEnumerable<IScenarioPage> ReadAll()`: This returns all the Scenario Pages in the Scenario Book.
+ Scenario Book
  + `int MaxIndex`: This is just like `int MaxIndex` in Scenario Book, except is that this returns the maximum index of the Scenario Sentence.
  + `int CurrentIndex`: This is just like `int CurrentIndex` in Scenario Book, except is that this returns what index of the Scenario Sentence is selected.
  + `IScenarioPage PointTo(int n)`: The nth Scenario Sentence in the Scenario Page is selected. It is as well as we are about to read one sentence in tha paper-page.
  + `IScenarioSentence ReadSentence()`: This returns the Scenario Sentence that is currently selected.
  + `IEnumerable<IScenarioSentence> ReadAll()`: This returns all the Scenario Sentences in the Scenario Page.
+ Scenario Book
  + `object OnRead()`: When this method is called, the Scenario Method bound to the Scenario Sentence is invoked. The return type is `object`, and this value is actually the same as the value the Scenario Method returns. Therefore, this return value is no meaning if the return type of the Scenario Method is `void`. However, it is a very powerful means of handling asynchronous methods. See [`TaskFlow`](TaskFlow.md) section for more details.

### Extensions
The extension methods for `IScenarioBook` and `IScenarioPage` are provided. 

+ `IScenarioBook`
  + `IScenarioBook Flip(this ISceanrioBook)`: This is equal to `OpenTo(CurrentIndex + 1)`. Next page is opened.
  + `bool Remain(this IScenarioBook)`: This returns whether `CurrentIndex` is less than `MaxIndex` or not.
  + `IScenarioBook Reset(this ISceanrioBook)`: When this method is called, `OpenTo(0)` is called for the Scenario Book at first. Then, `PointTo(0)` is called for all the Scenario Pages in the Scenario Book. In the end, first Scenario Page is opened, and first Scenario Sentence is selected for all the Scenario Pages.
+ `IScenarioPage`
  + `IScenarioPage Follow(this IScenarioPage)`: This is equal to `PointTo(CurrentIndex + 1)`. Next sentence is selected.
  + `bool Remain(this IScenarioPage)`: This returns whether `Current Index` is less than `MaxIndex` or not.

### Make The Best Use of *Fluent Interface*

Some methods declared in the `IScenarioBook` interface and the `IScenarioPage` interface return a reference to the instance for which the method is called after some processes. This is called *fluent interface design pettern*.

We can handle an instance of the `IScenarioBook`  interface like:

```cs
//Invoke Scenario Method bound to the 3rd Scenario Sentence in the zeroth Scenario Page in the Scenario Book.
scenarioBook
    .OpenTo(0)
    .ReadPage()
    .PointTo(3)
    .ReadSentence()
    .OnRead();

//Read next Scenario Page.
IScenarioPage scenarioPage = scenarioBook.Flip().ReadPage();
```

## Write Scenario Book

Now, you should know about the structure of Scenario Book. Even if you are not quite sure, that's no problem. We are going to learn how we can write a Scenario Book as a stroy script, and you will realize what we would like to do.

At first, the grammar, which means how to write a text to create a Scenario Book, and the format, which means what format of file is used, depend on "Scenario Publisher" which means a concrete class that implements the `IScenarioPublisher<>` generic interface. For example, a text is written on .txt file for `TsvScenarioPublisher` that implements the `IScenarioPublisher<TextAsset>` interface, while written on .xlsx file for `ExcelScenarioPublisher` that implements the `IScenarioPublisher<ExcelAsset>` interface, where the `IScenarioPublisher<TextAsset>` class and the `ExcelAsset` class are provided by [`ExcelFlow`](ExcelFlow.md).

In this section, we are going to learn how to write a Scenario Book by `TsvScenarioPublisher`.

Then, you might like to skip this section and read the document about `ExcelScenarioPublisher` because you prefer .xlsx file to .txt file, but you should read this section first because the grammars of the two Scenario Publishers are almost the same so that the grammar is not explained in detail in the document of `ExcelScenarioPublisher`. You will be able to understand about the grammar of `ExcelScenairoPublisher` easily if you are familiar with the grammar of `TsvScenarioPublisher`.

### The Format of `TsvScenarioPublisher`

UTF-8 encoding .txt file is available.

### The Grammar of `TsvSceanrioPublisher`

As described before, Scenario Book has the structure where a Scenario Book has some Scenario Pages, a Scenario Page has some Scenario Sentences, and a Scenario Sentence invokes a Scenario Method. Then, first we are going to do is to declare Scenario Pages in a .txt file.

```txt:SimpleCounting.txt
//--- Page1 ---
<Page>

</Page>

//--- Page2 ---
<Page>

</Page>
```

We can declare a Scenario Page with two symbols `<Page>` and `</Page>`, where `<Page>` is start symbol and `</Page>` is end symbol. The important thing is that all the texts written on the outside of page block are ignored so that We can write comment texts there.

Next, we are going to write additional texts as a Scenario Sentence on the inside of page blocks.

```
alias   param1  param2  param3  ...
```

The necessary elements to call a Scenario Method are firstly the method name and secondly parameters. The method name corresponds to the `alias`, so we write `alias` of the Scenario Method we want to call on the beginning of the line. Then, we write all the necessary parameters following the alias, where the elements are separated by the Tab.

```cs:SimpleCounter.cs
using ScenarioFlow;
using UnityEngine;

public class SimpleCounter
{
    private int count = 0;

    [ScenarioMethod("set.count", "Change count to given number")]
    public void SetCount(int n)
    {
        count = n;
    }

    [ScenarioMethod("log.count")]
    public void LogCount()
    {
        Debug.Log($"The count is {count}!");
    }
}
```

The `SimpleCounter` class has two Scenario Methods, `SetCount(int n)` and `LogCount()`.

The `alias` of the method `SetCount(int n)` is "set.count," and the method has one parameter, whose type is `int`. We can call this method like:

```
set.count   1
```

When the Scenario Method is called a `string` type parameter "1" is converted into a `int` type parameter 1 by Decoder for `int` we declared before.

On the other hand, the `LogCount()` method has `alias` "log.count," and has no parameters. If a Scenario Method has no parameters, we don't have to write any texts for it. Therefore, we can call this method like:

```
log.count
```

By the way, recall that Scenario Book can have `Label` as a bookmark. We can declare labels anywhere above a Scenario Sentence by writing the label symbol like:

```
<Label> name
```

"\<Label>" is label symbol, and "name" is label name. Of course, they are separated by the Tab.

Then, we can write the aliases of the Scenario Methods we want to call and labels on the each inside of the page blocks as we like.

```txt:SimpleCounting.txt
//--- Page0 ---
<Page>
<Label> p0-l0
set.count	0
log.count
</Page>

//--- Page1 ---
<Page>
set.count	1
<Label> p1-l1
log.count
</Page>
```

The Scenario Methods and the Scenario Sentences are read in in order from top to bottom. Therefore, this text file will be converted into a Scenario Book whose structure is like:

+ Scenario Book
  + Scenario Page 0
    + Scenario Setnence 0: `SetCount(0)`
    + Scenario Sentence 1: `LogCount()`
  + Scenario Page 1
    + Scenario Sentence 0: `SetCount(1)`
    + Scenario Sentence 1: `LogCount()`
+ Label
  + p0-l0: Scenario Sentence 0 in Scenario Page 0
  + p1-l1: Scenario Sentence 1 in Scenario Page 1

### Note

We can write a Scenario Book by editing a text file directly in Visual Studio, Notepad, or any text editors. However, a recommendation is to use a spreadsheet because it tends to happen to mistake the tab for the space, and it is also hard to read. With a spreadsheet, the separation is automatically executed so that it is easy to edit and read.

Another option is to import .xlsx file directly by [`ExcelFlow`](ExcelFlow.md). The .xlsx file is imported as an instance of the `ExcelAsset` class, but we don't have to know about the structure in detail. That's because .xlsx file is automatically converted into `ExcelAsset` by the `ExcelAssetImporter` class, and `ExcelAsset` is automatically converted into `IScenarioBook` by `ExcelScenarioPublisher`.

## Publish Scenario Book

If we finished writing a Scenario Book. It's time to publish it to read it by c# program. `ScenarioFlow` provides the `IScenarioPublisher<>` generic interface, which converts a source object we created into an instance of a class that implements the `IScenarioBook` interface.

```cs:IScenarioPublisher.cs
namespace ScenarioFlow
{
    public interface IScenarioPublisher<TSource>
    {
        IScenarioBook Publish(TSource source);
    }
}
```

The source object corresponds to .txt file imported as `TextAsset` in Unity, and the class corresponds to the `ScenarioBook` class in `TsvScenarioPublisher`, for example. So the `Publish` method in `TsvScenarioPublisher` receives an instance of `TextAsset` and returns an instance of `ScenarioBook` as `IScenarioBook`.

In addition, you should know that a class that implements the `IScenarioPublisher<>` interface is most likely to require an instance of a class that implements the `IScenarioMethodSearcher` interface for initialization. Actually, the constructor of `TsvScenarioPublisher` requires it to get information about Scenario Methods required in Scenario Book.

The `ScenarioMethodSeacher` class that implements the `IScenarioMethodSeacher` is provided by `ScenarioFlow`. So we are likely to use it to create an instance of Scenario Publisher.

The important thing is that the `ScenarioMethodSeacher` class requires instances of a class that implements the `IReflectable` interface for initialization. We have to pass the classes that implements the `IReflectable` interface and have Scenario Methods we want to call as `IEnumerable<IReflectable>` into the constructor.

Then, we can create an instance of `TsvScenarioPublisehr`, and publish a Scenario Book as follows.

```cs
IScenarioMethodSearcher scenarioMethodSearcher = new ScenarioMethodSeacher(
    new IReflectable[]
    {
        new SimpleCounter()
    });

IScenarioPublisher<TextAsset> scenarioPublisher = new TsvScenarioPublisher(scenarioMethodSearcher);

IScenarioBook scenarioBook = scenarioPublisher.Publish(scenarioSource);
```

## Read Scenario Book

After we published a Scenario Book, we can freely read the Scenraio Book and call Scenario Methods.

There are many ways of reading. Let's see an example.

At first, make a change to the `ScenarioManager` class created in [Hello World Example](#hello-world-example) chapter and the `PrimitiveDecoder` class created in [Preparation of Scenario Method](#preparation-of-scenario-method) chapter.

```cs:ScenarioManager.cs
using ScenarioFlow;
using System;
using UnityEngine;

public class ScenarioManager : MonoBehaviour, IReflectable
{
    [SerializeField]
    private TextAsset storyScript;

    private IScenarioBook readingBook;

    private void Start()
    {
        IScenarioPublisher<TextAsset> scenarioPublisher = new TsvScenarioPublisher(
            new ScenarioMethodSeacher(
                new IReflectable[]
                {
                    this,
                    new SimpleCounter(),
                    new PrimitiveDecoder()
                }));

        readingBook = scenarioPublisher.Publish(storyScript);

        readingBook.Reset().ReadPage().ReadSentence().OnRead();
    }

    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            if (readingBook.ReadPage().Remain())
            {
                readingBook.ReadPage().Follow().ReadSentence().OnRead();
            }
            else
            {
                if (readingBook.Remain())
                {
                    readingBook.Flip().ReadPage().ReadSentence().OnRead();
                }
                else
                {
                    Debug.Log("Reading finished!");
                }
            }
        }
    }

    public object Reflect()
    {
        return this;
    }

    [ScenarioMethod("log.message")]
    public void LogMessage(string message)
    {
        Debug.Log(message);
    }

    [ScenarioMethod("log.message.rep")]
    public void LogRepetedMessage(string message, int count)
    {
        if (count < 0)
        {
            throw new ArgumentException("count must not be negative number");
        }

        for (var i = 0; i < count; i++)
        {
            Debug.Log($"Message {i + 1}: {message}");
        }
    }
}
```

```cs:PrimitiveDecoder.cs
using ScenarioFlow;
using UnityEngine;

public class PrimitiveDecoder : IReflectable
{
    public object Reflect()
    {
        return this;
    }

    [Decoder]
    public int IntDecoder(string s)
    {
        if (int.TryParse(s, out int n))
        {
            return n;
        }
        else
        {
            Debug.LogWarning($"{s} is not number! Int Decoder returns 0");

            return 0;
        }
    }

    [Decoder]
    public string StringDecoder(string source)
    {
        return source;
    }
}
```
The methods the `IScenarioBook` interface has are described in the [The Structure of Scenario Book](#the-structure-of-scenairo-book) section.

Then, create a new UTF-8 encoding text file called "SampleScenario.txt," and attach it to the `storyScript` property of the `ScenarioManager` object in the hierarchy window.

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

Be careful not to mistake the tab for the space.

When playing, we can continue reading by pressing the space key.

![SampleSecenarioResult](https://github.com/AkiraIto2023/ScenarioFlow/blob/main/Images/SampleScenarioResult.jpg)

### Note

In this section, we implemented the simple logic to read a Scenario Book in order as it is. However, it is no useful at all. For it doesn't support asynchronous method or even `Label` inserted into the Scenario Book.

To use `ScenarioFlow` more practically, we should implement "Scenario Method Reader" that supports asynchronous method and `Label`. 

Then, [`TaskFlow`](TaskFlow.md), which is an optional tool of `ScenarioFlow`, provides the `ScenarioBookReader` class that implements the `IScenarioBookReader` interface. It enables us to use asyncronous method or open the Scenario Sentence that has particular `Label` in the Scenario Book easily.

See [`TaskFlow`](TaskFlow.md) for more details, and if it is hard to implement the practical logic to read a Scenario Book, or you think the tool useful, consider using this.

# License

This library is under the MIT license.
