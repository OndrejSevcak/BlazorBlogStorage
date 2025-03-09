---
title: "Co je yield return a jak souvisí s IEnumerable"
description: "Co je yield return a jak souvisí s IEnumerable"
author: "Ondrej Sevcak"
date: "2025-03-09"
category: "CSharp"
hashtags: "#CSharp #DotNet #Basics"
publish: true
---

#### Co je yield return

- *yield return* je C# keyword
- používá se v metodách k produkování hodnot systémem *jedna po druhé*
- yield return pozastaví aktuální exekuci metody a vrátí hodnotu volajícímu kódu (caller) přičemž si stále zachovává stav aktuální iterace
- jakmile je iterator znovu zavolán, exekuce pokračuje tam, kde naposledy skončila
- umožňuje *Lazy evaluation*


*Lazy evaluation (the opposite of eager evaluation) is when we wait to execute a piece of code until we absolutely, positively have to*

### Demo

```csharp
IEnumerable<int> GetNumbersOneByOne()
{
    yield return 1;
    yield return 3;
    yield return 2;
    yield return 4;
}

var numbers = GetNumbersOneByOne();

foreach (var number in numbers) 
{
  Console.WriteLine(number);
  // Output:
  // 1
  // 3
  // 2
  //4
}
```

- toto demo ukazuje tzv. deffered exekucion
- když se zavolá funkce *GetNumbersOneByOne()*, vrátí se nám return value i přes to, že **kód této funkce nebyl exekuován**
- metoda se exekuuje až když data z tohot IEnumerable<int> potřebujeme - zde ve foreach loopu
  -> v každé iteraci skáčeme dovnitř metody *GetNumbersOneByOne()* a dle jejího aktuálního vnitřního stavu dostáváme jednotlivé hodnoty


### Příklad 1 - Nekonečný generátor čísel

```csharp
IEnumerable<int> GenerateNumbers()
{
    int i = 1;
    while(true){
        yield return i++;
    }
}
```

### Příklad 2 - Filtrování dat

```csharp
IEnumerable<int> GetEvenNumbers(IEnumerable<int> numbers)
{
    foreach (var num in numbers)
    {
        if (num % 2 == 0)
            yield return num;
    }
}

var evenNumbers = GetEvenNumbers(new int[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 });
foreach (var number in evenNumbers) 
{
  Console.WriteLine(number);
  // Output:
  // 2
  // 4
  // 6
  // 8
  // 10
}
```

### Příklad 3 - zpracování textového souboru řádek po řádku

```csharp
IEnumerable<string> ReadLargeFile(string filePath)
{
    using (StreamReader reader = new StreamReader(filePath))
    {
        string line;
        while ((line = reader.ReadLine()) != null)
        {
            yield return line;
        }
    }
}
```
