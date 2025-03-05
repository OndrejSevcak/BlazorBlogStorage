---
title: "IEnumerable a IEnumerator - iterace kolekcí"
description: "IEnumerable a IEnumerator - iterace kolekcí"
author: "Ondrej Sevcak"
date: "2025-03-05"
category: "CSharp"
hashtags: "#CSharp #DotNet #Basics"
---

#### Jak vypadá a k čemu slouží *IEnumerable*?

- IEnumerable je interface reprezentující kolekci objektů, kterou lze iterovat

```csharp
public interface IEnumerable
{
    // Returns an IEnumerator for this enumerable Object.
    IEnumerator GetEnumerator();
}

//generic version
public interface IEnumerable<out T> : IEnumerable
{
    IEnumerator<T> GetEnumerator();
}

```

 - v implementaci vidíme, že IEnumerable obsahuje pouze jedinou metodu *GetEnumerator*, která vrací IEnumerator.<br>

#### Jak vypadá a k čemu slouží  *IEnumerator*?

 - IEnumerator již představuje konkrétní implementaci, jak interovat skrze kolekci
 - je to také interface, takže definuje metody, které je nutné implementovat

```csharp
//Base interface for all enumerators, providing a simple approach to iterating over a collection.
public interface IEnumerator
{
    //Advances the enumerator to the next element of the enumeration
    // returns a boolean indicating whether an element is available
    //the first call to MoveNextbrings the first element of the enumeration into view
    bool MoveNext();

    //Returns the current element of the enumeration
    object Current { get; }

    //Resets the enumerator to the beginning of the enumeration
    void Reset();
}

//generic version -> extends the IEnumerator definition by by T Current
public interface IEnumerator<out T> : IEnumerator, IDisposable
{
    //Returns the current element of the enumeration
    T Current { get; }
}
```

Pozn: Implementace je převzata z [source.dotn.net](https://source.dot.net/)

IEnumerator tedy poskytuje funkcionalitu pro iterování skrze kolekci.<br><br>
Kde se to používá?

- například když používáme foreach loop, C# volá metodu *GetEnumerator* té kolekce, kterou voláme a každá iterace volá metodu *MoveNext* pro posunutí na další položku kolekce.

**foreach vs. enumerator**

- vytvořme jednoduchou IEnumerable kolekci obsahující int čísla
```csharp
IEnumerable<int> collection = [1, 2, 3, 4, 5];
```

- iterace kolekcí pomocí foreach
```csharp
foreach (int item in collection)
{
    Console.WriteLine(item);
}
```

- interace pomocí enumeratoru
```csharp
IEnumerator<int> enumerator = collection.GetEnumerator();
while (enumerator.MoveNext())
{
    Console.WriteLine(enumerator.Current);
}
```

Předchozí 2 příklady dělají úplně stejnou věc, jen jsou zapsány jinou syntaxí.

- zajímavé je, že metoda *MoveNext* se volá ještě před tím, než máme k dispozici první element kolekce<br>
    -> to je z důvodu, že na začátku míří enumerator jednu pozici před první element - tedy do jakéhosi 'prázdna' a až po prvním zavolání metody *MoveNext* míří na první element

### Vlastní implementace IEnumeratoru

- implementujme vlastní enumerator, který bude iterovat pouze sudá čísla
- vytvoříme tedy novou třídu *MyCustomEvenEnumerator*, která implementuje interface *IEnumerator<int>*
- přes construktor si předáme do privátního fieldu předáme data, která chceme iterovat
- vytvoříme index *_currentIndex*, který bude representovat index položky aktuální iterace
- implementujeme propertu Current a metody MoveNext(), Reset() a Dispose()

```csharp
public class MyCustomEvenEnumerator : IEnumerator<int>
{
    private int[] _data;
    private int _currentIndex = -1;

    public MyCustomEvenEnumerator(int[] data)
    {
        _data = data;
    }

    public int Current => _data[_currentIndex];
    object IEnumerator.Current => Current;

    public bool MoveNext()
    {
        _currentIndex++;
        if(_currentIndex >= _data.Length)
        {
            return false;
        }

        if(_data[_currentIndex] % 2 != 0)
        {
            return MoveNext();
        }

        return _currentIndex < _data.Length;
    }

    public void Reset()
    {
        _currentIndex = -1;
    }

    public void Dispose(){}
}
```

### Vlastní implementace IEnumerable

- cílem je vytvořit nový typ kolekce, která bude obsahovat náš nový *MyCustomEvenEnumerator* a využije jej při iteraci hodnot kolekce

```csharp
public class MyCustomEvenEnumerable : IEnumerable<int>
{
    private MyCustomEvenEnumerator _enumerator;

    public MyCustomEvenEnumerable(int[] data)
    {
        _enumerator = new MyCustomEvenEnumerator(data);
    }

    public IEnumerator<int> GetEnumerator()
    {
        return _enumerator;
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        return GetEnumerator();
    }
}
```

### Testování 

```csharp
MyCustomEvenEnumerable customCollections = new MyCustomEvenEnumerable(new int[] { 1, 2, 3, 4, 5 });
IEnumerator<int> customEnumerator = customCollections.GetEnumerator();
while (customEnumerator.MoveNext())
{
    Console.WriteLine(customEnumerator.Current);
}

//vrátí hodnoty: 2 4
```