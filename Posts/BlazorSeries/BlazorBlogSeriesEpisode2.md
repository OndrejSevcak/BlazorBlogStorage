---
title: "Úvod do komponent a jejich životního cyklu"
description: "Úvod do komponent a jejich životního cyklu"
author: "Ondrej Sevcak"
date: "2025-01-22"
category: "Blazor"
hashtags: "#CSharp #DotNet #Blazor"
---

### Co je Blazor Komponenta?

Blazor komponenty jsou stavebními bloky Blazor aplikací. Jsou to znovupoužitelné části UI napsané v C#, HTML a CSS. Každá komponenta se nachází v souboru `.razor` a zapouzdřuje svou logiku, vykreslování a zpracování událostí. Příklad Blazor komponenty:

```csharp
<h3>Counter</h3>
<p>Current count: @currentCount</p>
<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### Klíčové vlastnosti Blazor komponent:

- **Znovupoužitelnost:** Komponentu napíšete jednou a použijete kdekoliv ve své aplikaci
- **Zapouzdření:** Udržujete logiku UI a styly samostaně.
- **Interaktivita:** Propojujení UI, načítání dat a zprcování událostí.

### Pochopení životního cyklu komponenty

Životní cyklus komponenty v Blazoru je sekvence metod a událostí, které Blazor vyvolává při inicializaci, vykreslování a likvidaci komponenty. Pochopení těchto metod životního cyklu je základem pro úspěšné vyvíjení Blazor aplikací.

### Přehled metod životního cyklu

1. **`OnInitialized` a `OnInitializedAsync`**
   - Spouští se při inicializaci komponenty.
   - Používá se k nastavení stavu komponenty nebo inicializaci dat ze služeb - data loading nebo fetchování api

   ```csharp
   protected override void OnInitialized()
   {
       // Synchronous initialization logic
       _data = _service.GetData();
       Console.WriteLine("Component initialized.");
   }

   protected override async Task OnInitializedAsync()
   {
       // Asynchronous initialization logic
       _data = await _service.GetDataAsync();_
       Console.WriteLine("Component initialized asynchronously.");
   }
   ```

2. **`OnParametersSet` a `OnParametersSetAsync`**
   - Vyvolá se při nastavení nebo aktualizaci(změně hodnoty) parametrů komponenty (označené atributem [Parameter]).
   - Užitečné pro reakci na změnu dat v nadřazené komponentě.

   ```csharp
   [Parameter] public string Title { get; set; }

   protected override void OnParametersSet()
   {
       _specificData = _service.LoadSpecificData(MyParameter) 
       Console.WriteLine($"Title parameter set to: {Title}");
   }
   ```

3. **`ShouldRender`**
   - Určuje, zda by se komponenta měla znovu vykreslit.
   - Tuto metodu můžete využít pro optimalizaci výkonu vykreslování.

   ```csharp
   protected override bool ShouldRender()
   {
       return _myData.Count != _service.Data.Count; // Allow re-rendering
   }
   ```

4. **`OnAfterRender` a `OnAfterRenderAsync`**
   - Vyvolá se po vykreslení komponenty.
   - Ideální pro interakci s DOM nebo inicializaci JavaScriptových funkcí, protože stránka (DOM) je již plně vykreslena

   ```csharp
   protected override async Task OnAfterRenderAsync(bool firstRender)
   {
       if (firstRender)
       {
           Console.WriteLine("First render completed.");
       }

       jsModule = await _jsRuntime.InvokeAsync<IJSObjectReference>("import", path);
       await jsModule.InvokeVoidAsync("applyPostStyling");
   }
   ```

5. **`Dispose`**
   - Používá se k uvolnění zdrojů, když je komponenta odstraněna z UI.

   ```csharp
   public void Dispose()
   {
       _service.Dispose();
       Console.WriteLine("Component disposed.");
   }
   ```

### Praktický příklad: Životní cyklus v akci

Postavme jednoduchou Blazor komponentu, která demonstruje tyto metody životního cyklu. Komponenta bude:

- Asynchronně načítat data při inicializaci.
- Zaznamenávat aktualizace parametrů.
- Optimalizovat logiku vykreslování.

**Kód komponenty:**

```csharp
@page "/lifecycle-demo"
<h3>Lifecycle Demo</h3>

<p>Data: @Data</p>

@code {
    [Parameter] public string InputParameter { get; set; }

    public string Data { get; set; } = "Loading...";

    protected override async Task OnInitializedAsync()
    {
        // Simulate data fetching
        await Task.Delay(2000);
        Data = "Fetched Data";
    }

    protected override void OnParametersSet()
    {
        Console.WriteLine($"InputParameter updated to: {InputParameter}");
    }

    protected override bool ShouldRender()
    {
        // Avoid unnecessary re-rendering
        return !string.IsNullOrEmpty(Data);
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            Console.WriteLine("Component rendered for the first time.");
        }
    }

    public void Dispose()
    {
        Console.WriteLine("Cleaning up resources.");
    }
}
```


### Doporučení

1. Vyhněte se těžkým výpočtům v `ShouldRender` a `OnAfterRender`.
2. Používejte `OnInitializedAsync` pro asynchronní úkoly.
3. Vždy implementujte `IDisposable` pro komponenty, které drží neřízené zdroje.
4. Využívejte `ShouldRender` k prevenci zbytečných aktualizací.



