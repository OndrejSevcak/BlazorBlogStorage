---
title: "Základy multitheadingu v C#"
description: "Základy multitheadingu v C#"
author: "Ondrej Sevcak"
date: "2025-03-15"
category: "CSharp"
hashtags: "#CSharp #Multithreading #ParallelProgramming"
publish: true
---

#### Parallel programming - Úvod

- dnešní počítače i mobily již dávno mají více-jádrové procesory, které umožňují současný(simultální) běh několika vláken (threadů) najednou
- každý softwarový vývojář by tedy měl být schopný tento hardwarový potenciál využít a umět programovat tak, aby byla práce aplikace rozložena mezi tyto procesory
- s vývojem .NET se vyvýjí i způsobem práce s vlákny a jejich programování. Již nějakou dobu mají .NET vývojáři k dispozici [TPL - Task Parallel Library](https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/task-parallel-library-tpl), která práci s vlákny výrazně zjednodušuje. 
- cílem **multithreadingu** je **provádění více operací ve stejný okamžik - na více separátních vláknech**. 

#### Task Parallel Library (TPL)

- jedná se knihovnu typů a APIs v namespace *System.Threading* a *System.Threading.Tasks*
- účelem této knihovny je zjednodušení procesu paralelizování a souběžnosti(concurrency) našeho kódu
- TPL dynamicky škáluje úroveň paralelizace a efektivně využívá dostupné procesory
- jedná se o preferovaný způsob psaní vícevláknového a paralelního kódu 

#### O co se TPL stará?

- rozdělení práce (partitioning)
- časování vláken(threadů) na ThreadPoolu
- ukončování asynchroních úloh (cancellation support)
- state management a další low-level věci o které se nemusíme jako vývojáři starta

#### Ne vždy je paralelní programování nutné a přínosné

- ne všechen kód je vhodný k paralelizaci a existují situace, kdy jeho přidání zbytečně zvyšuje komplexnost a aplikaci zpomalí
- například loop, který při každé iteraci dělá jen velmi málo práce není nutné paralelizovat

**I když TPL výrazně zjednodušuje práci s vlákny a možnosti paralelizace, určitě je dobré mít alespoň základní znalost multithreading konceptů jako jsou locky, deadlocky, apod**

#### Data Parallelism (pomocí TPL)

- **Data parallelism** označuje situaci, kdy je na elementech pole nebo kolekce **jedna stejná operece je prováděna současně(concurrently)** 
- zdrojová kolekce dat je tedy rozdělena na části(**partitions**) a díky tomu na každé části může rázoveň pracovat více vláken současně
- TPL poskytuje podporu pro data parallelism pomocí třídy *System.Threading.Tasks.Parallel* kde najdeme metody jako *Parallel.For* nebo *Parallel.ForEach*
- s pomocí těchto metod můžeme psáno paralelní cykly stejně jako synchronní a TPL se postará o vytvoření vláken, work queues a locků za nás
- detailní informace jak funguje *Parallel.For* nebo *Parallel.ForEach* přímo od Microsoftu lze stáhnout zde [PATTERNS OF PARALLEL PROGRAMMING](https://www.microsoft.com/en-us/download/details.aspx?id=19222)

**Parallel.For**

- metoda exekuuje for loop, ve kterém by jednotlivé iterace měly běžet paralelně
- je synchroní = blokující (blokuje volající vlákno dokud se loop nedokončí)

**Příklad - For loop s 10 opakováními, kde jedna iterace trvá 1 sekundu zpracovaný paralelně**
```csharp
//For vs. Parallel.For
stopwatch.Start();
for (int i = 0; i < 10; i++)
{
    Task.Delay(1000).Wait();
}
stopwatch.Stop();
Console.WriteLine($"For loop took {stopwatch.ElapsedMilliseconds} ms"); //10112 ms

stopwatch.Restart();
Parallel.For(0, 10, i =>
{
    Task.Delay(1000).Wait();
});
stopwatch.Stop();
Console.WriteLine($"Parallel.For loop took {stopwatch.ElapsedMilliseconds} ms");    //1051 ms
```

Klasické sekvenční zpracování trvá 10 x 1s, tedy 10 sekund (10112ms). Paralelní zpracování trvalo pouze 1 sekundu (1051 ms);

**Parallel.ForAsync**

- asynchronní verze, podporuje cancellation token
- neblokuje volající vlákno - vrací Task

**Prallel.ForEach příklad:**

```csharp
using System.Diagnostics;
using System.Security.Cryptography;
using System.Text;

List<Item> items = new List<Item>();
var random = Random.Shared;
Stopwatch stopwatch = new Stopwatch();

long demo1 = 0;
long demo2 = 0;

for(int i = 0; i < 1_000_000; i++)  //small data sets are not suitable for parallel processing
{
    var item = new Item { Id = i, Name = $"Item {i}" };
    items.Add(item);
}

Console.WriteLine("Data is ready for processing");

//sequential processing
stopwatch.Start();

foreach (var item in items)
{
    AddHashCode(item);
}

Console.WriteLine("Sequential processing is done");

stopwatch.Stop();
demo1 = stopwatch.ElapsedMilliseconds;

//parallel processing
stopwatch.Restart();

ParallelOptions options = new ParallelOptions { MaxDegreeOfParallelism = Environment.ProcessorCount };
Parallel.ForEach(items, options, item => AddHashCode(item));

stopwatch.Stop();
demo2 = stopwatch.ElapsedMilliseconds;

Console.WriteLine($"Sequential processing took {demo1} ms");    //17734 ms
Console.WriteLine($"Parallel processing took {demo2} ms");      //3872 ms


void AddHashCode(Item item)
{
    int code;  
    code = random.Next(1000, 9999); //here we are using Random.Shared which is thread-safe and available since .NET 6
    
    //simulating heavier workload
    for (int i = 0; i < 10; i++)
    {
        item.HashCode = $"{HashNumber(code)}";
    }    
}

static string HashNumber(int input)
{
    using (SHA256 sha256Hash = SHA256.Create())
    {
        byte[] bytes = sha256Hash.ComputeHash(Encoding.UTF8.GetBytes(input.ToString()));
        StringBuilder builder = new StringBuilder();
        for (int i = 0; i < bytes.Length; i++)
        {
            builder.Append(bytes[i].ToString("x2"));
        }
        return builder.ToString();
    }
}

class Item
{
    public int Id { get; set; }
    public string? Name { get; set; }
    public string? HashCode { get; set; }
}
```

**Na co si dávat pozor?**
 - v metodě, která se má zpracovávat je potřeba přemýšlet nad tím, že vše co obsahuje bude současně využíváno více vlákny
   -> není tedy vhodné používat třídy, které nejsou tread-save a vytvářet tam například locky, které způsobí, že jednotlivé vlákna buou čekat na uvolnění objektu a zpracování bude ve výsledku sekvenční a ne paralelní 
   -> pozn. psaní do konzole je sychronní a blokuje vlákna
   -> klasická třída Random také není thread-save, je třeba použít Random.Shared nebo nějaký mechanismus pro vytváření samostatných instancí na jednotlivých vláknech použitím např. *ThreadLocal<T>*


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

**Příklad 2 - Paralelní sčítání 1 milionu čísel pomocí Parallel.For**

```csharp
int[] nums = Enumerable.Range(0, 1_000_000).ToArray();
long total = 0;

// Use type parameter to make subtotal a long, not an int
Parallel.For<long>(0, nums.Length, () => 0,
    (j, loop, subtotal) =>
    {
        subtotal += nums[j];
        return subtotal;
    },
    subtotal => Interlocked.Add(ref total, subtotal));

Console.WriteLine("The total is {0:N0}", total);
```

#### Task-based asynchronous programming

- základním prvkem je *Task*, který reprezentuje asynchronní operaci
- https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/task-based-asynchronous-programming




TO BE CONTINUED..

