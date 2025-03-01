---
title: "Multitheading in C# - První kroky"
description: "Multitheading in C# - první kroky"
author: "Ondrej Sevcak"
date: "2025-03-01"
category: "CSharp"
hashtags: "#CSharp #DotNet #AI #VSCode"
---

*Pozn: V tomto vlákně si budu psát své poznámky a postřehy k tématu multithreading programování v C#*

Cílem **multithreadingu** je provádění více operací ve stejný okamžik - na více separátních vláknech. 

### Parallel programming - Úvod

- procesory dnešních počítačů již dávno mají více jader, které umožňují běh paralelních vláken (threads), které jsou prováděny simultálně

- každý softwarový vývojář by tedy měl umět tento potenciál využít k distribuci práce aplikace na tyto procesory

- s vývojem .NET se vyvýjí i způsobem práce s vlákny a jejich programování. Již nějakou dobu mají .NET vývojáři k dispozici [TPL - Task Parallel Library](https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/task-parallel-library-tpl), která práci s vlákny výrazně zjednodušuje. 

- asynchronnímu programování s *Task* a async-*await* se ale v tomto vlákně věnovat nebudu. Budu se věnovat třídám spjatým přímo s multithreadingem

### Třída Interlocked

- poskytuje atomické operace pro proměnné, které jsou sdílené mezi více vlákny
- [reference](https://learn.microsoft.com/en-us/dotnet/api/system.threading.interlocked?view=net-9.0)


**Příklad - sčítání sumy kolekce typu <int> při jejím paralalením odbavování více vláky**
```csharp
    int outerSum = 0;
    Action processItems = () => 
    {
        int localSum = 0;
        while(_queue.TryDequeue(out int item)){
            localSum += item;
            Console.WriteLine($"Dequeued {item}");
        }
        Interlocked.Add(ref outerSum, localSum);
    };
```

### Třída Parallel

- poskytuje metody pro paralelní zpracování

**Parallel.For**

- metoda exekuuje for loop, ve kterém by jednotlivé iterace měly běžet paralelně

**Příklad - For loop s 10 opakováními, kde jeda iterace trvá 1 sekundu zpracovaný paralelně**
```csharp
    Parallel.For(0, 10, i =>
    {
        Console.WriteLine($"Task {i} started");
        Task.Delay(1000).Wait();
        Console.WriteLine($"Task {i} completed");
    });
```

- tento kód se díky paralelnímu zpracování dokončí ne za 10s, ale za 1,1s
![Parallel.For Demo](https://raw.githubusercontent.com/OndrejSevcak/BlazorBlogStorage/refs/heads/main/Posts/CSharp/ParallelForDemo.png)