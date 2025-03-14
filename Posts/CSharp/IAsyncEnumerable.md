---
title: "IAsyncEnumerable - streamování dat"
description: "IAsyncEnumerable - streamování dat"
author: "Ondrej Sevcak"
date: "2025-03-14"
category: "CSharp"
hashtags: "#CSharp #CSharp8 #Basics"
publish: true
---

#### Jak vypadá a k čemu slouží *IAsyncEnumerable<T>*?

- *IAsyncEnumerable<T>* umožňuje asynchronní iteraci dat pomocí *await foreach*
- Funguje podobně jako *IEnumerable<T>* ale je navržen speciálně pro asynchronní operace bez blokování hlavního vlákna
- dostupný od verze C# 8 a .NET Core 3.0

**Definice**

```csharp
public interface IAsyncEnumerable<out T> where T : allows ref struct
{
    /// <summary>Returns an enumerator that iterates asynchronously through the collection.</summary>
    /// <param name="cancellationToken">A <see cref="CancellationToken"/> that may be used to cancel the asynchronous iteration.</param>
    /// <returns>An enumerator that can be used to iterate asynchronously through the collection.</returns>
    IAsyncEnumerator<T> GetAsyncEnumerator(CancellationToken cancellationToken = default);
}
```

Jak napsat metodu, která vrací IAsyncEnumerable? 
 - Stejně jako pro klasické IEnumerable :-)

```csharp
async IAsyncEnumerable<int> GetNumbersRangeAsync(int start, int count)
{
    for(int i = start; i < start + count; i++)
    {
        await Task.Delay(1000);
        yield return start + i;
    }
}
```

Jak metodu použít?
 - C# podporuje asynchronní enumerables stejně jako synchronní enumerables 
 - Pro asnychronní iterování je nutné použít *await foeach*

```csharp
await foreach (var number in GetNumbersRangeAsync(10, 5))
{
    Console.WriteLine(number);
}
//prints: 20 21 22 23 24 
```

Jak vypadá tento kód po kompilaci? 

```csharp
IAsyncEnumerator<int> e = GetNumbersRangeAsync(10, 5).GetAsyncEnumerator();
try
{
  while (await e.MoveNextAsync()) Console.Write(e.Current + " ");
}
finally { if (e != null) await e.DisposeAsync(); }
```

#### Cancellation token s *IAsyncEnumerable*

- nepovinný parametr metody *GetAsyncEnumerator *
- slouží jako prostředek ke zrušení asynchronní operace (v tomto případě enumerace hodnot)
- pokud je prostřednictvím tokenu zaslán požadavek o zrušení enumerace, aktuální nebo další *MoveNextAsync* bude narušen a vrátí *OperationCanceledException* 

**Použití cancellation tokenu**

Vraťme se k příkladu iterace hodnot z *IAsyncEnumerable* pomocí *await foreach*
```csharp
await foreach (var number in GetNumbersRangeAsync(10, 5))
{
    Console.WriteLine(number);
}
```

**Jak při použití této syntaxe dostaneme cancellation token do metody *GetAsyncEnumerator*, kterou používá compiler?**

1. upravíme naši *GetNumbersRangeAsync* metodu tak, že přidáme *CancellationToken* jako neprovinný parametr a označíme ho atributem *[EnumeratorCancellation]*. Tento atribut zajistí přdání tokenu do *GetAsyncEnumerator* metody

```csharp
async IAsyncEnumerable<int> GetNumbersRangeAsync(int start, int count, [EnumeratorCancellation] CancellationToken cancellationToken = default)
{
    for(int i = start; i < start + count; i++)
    {
        await Task.Delay(1000, cancellationToken);
        yield return start + i;
    }
}
```

2. použitím extension metody *WithCancellation* pro *IAsyncEnumerable<T>*, která konzumuje cancellation token jako parametr

```csharp
await foreach (int item in GetNumbersRangeAsync(10, 5).WithCancellation(token))
{
  Console.Write(item + " ");
}
```

#### Kde se dá *IAsyncEnumerable<T>* využít?

-> **Data streaming**

Podívejme se následující WeatherForecastController enpoint, který vrací IEnumerable a který všichni známe jako bolier plate code při vyvoření nového projektu WebAPI:

```csharp
[HttpGet("GetForecast")]
public IEnumerable<WeatherForecast> Get()
{
    return Enumerable.Range(1, 5).Select(index => new WeatherForecast
    {
        Date = DateOnly.FromDateTime(DateTime.Now.AddDays(index)),
        TemperatureC = Random.Shared.Next(-20, 55),
        Summary = Summaries[Random.Shared.Next(Summaries.Length)]
    })
    .ToArray();
}
```

-> tento endpoint vrátí výstupní data jako jednorázovou response obsahující kompletní datovou sadu najednou

**Úprava pomocí *IAsyncEnumerbale* a *yield return* **

-> já ale nechi jednorázovou odpověď. Chci si vytvořit data processing pipeline a chci data dostávat kontinuálně aktualizované každou sekundu. Přesně toho dosáhneme pomocí následující úpravy s použitím *IAsyncEnumerbale* a *yield return*

```csharp
[HttpGet("GetForecastStream")]
public async IAsyncEnumerable<WeatherForecast> GetStreaming([EnumeratorCancellation] CancellationToken cancellationToken)
{
    var index = 0;
    while (!cancellationToken.IsCancellationRequested)
    {
        yield return new WeatherForecast
        {
            Date = DateOnly.FromDateTime(DateTime.Now.AddDays(index)),
            TemperatureC = Random.Shared.Next(-20, 55),
            Summary = Summaries[Random.Shared.Next(Summaries.Length)]
        };
        await Task.Delay(1000, cancellationToken);
        index++;
    }
}
```

-> příklad z praxe je stramování velkých souborů přes API, které může klient konzumovat i když nemá kompletní soubor - například video

-> další zajímavý use-case je streamování real-time dat - například aktuální ceny akcií na burze nebo data z nějakých senzorů.
   Tomuto tématu bych se chtěl věnovat detailněji v nějakém separátním postu. 