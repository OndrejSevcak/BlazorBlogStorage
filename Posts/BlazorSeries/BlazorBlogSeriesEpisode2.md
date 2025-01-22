---
title: "Blazor Series Episode 2"
description: "A deep dive to component lifecycle"
author: "Ondrej Sevcak"
date: "2025-01-22"
category: "Blazor"
hashtags: "#CSharp #DotNet #Blazor"
---


# Blazor Series Episode 2 - Deep Dive into Components and Their Lifecycle

In this episode of our Blazor Series, we will delve deep into Blazor components and their lifecycle, a crucial concept for creating efficient and maintainable applications.

---

## What is a Blazor Component?

Blazor components are the building blocks of Blazor applications. They are reusable pieces of UI written in C#, HTML, and optionally CSS. Each component resides in a `.razor` file and encapsulates its logic, rendering, and event handling. For example:

```razor
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

### Key Features of Blazor Components:

- **Reusability:** Write once and use anywhere in your Blazor application.
- **Encapsulation:** Keep the UI logic and styles self-contained.
- **Interactivity:** Bind data and handle events seamlessly.

---

## Understanding the Component Lifecycle

The component lifecycle in Blazor is a sequence of methods and events that Blazor invokes as a component is initialized, rendered, and disposed of. Understanding these lifecycle methods can help you manage state, optimize performance, and debug effectively.

### Lifecycle Methods Overview

1. **`OnInitialized` and `OnInitializedAsync`**
   - Triggered when the component is initialized.
   - Used to set up component state or initialize data from services.

   Example:
   ```csharp
   protected override void OnInitialized()
   {
       // Synchronous initialization logic
       Console.WriteLine("Component initialized.");
   }

   protected override async Task OnInitializedAsync()
   {
       // Asynchronous initialization logic
       await Task.Delay(1000);
       Console.WriteLine("Component initialized asynchronously.");
   }
   ```

2. **`OnParametersSet` and `OnParametersSetAsync`**
   - Invoked when component parameters are set or updated.
   - Useful for responding to changes in parent component data.

   Example:
   ```csharp
   [Parameter] public string Title { get; set; }

   protected override void OnParametersSet()
   {
       Console.WriteLine($"Title parameter set to: {Title}");
   }
   ```

3. **`ShouldRender`**
   - Determines whether the component should re-render.
   - Override this method to optimize rendering performance.

   Example:
   ```csharp
   protected override bool ShouldRender()
   {
       return true; // Allow re-rendering
   }
   ```

4. **`OnAfterRender` and `OnAfterRenderAsync`**
   - Invoked after the component has been rendered.
   - Ideal for interacting with the DOM or initializing JavaScript libraries.

   Example:
   ```csharp
   protected override async Task OnAfterRenderAsync(bool firstRender)
   {
       if (firstRender)
       {
           Console.WriteLine("First render completed.");
       }
   }
   ```

5. **`Dispose`**
   - Used to clean up resources when the component is removed from the UI.

   Example:
   ```csharp
   public void Dispose()
   {
       Console.WriteLine("Component disposed.");
   }
   ```

---

## Practical Example: Lifecycle in Action

Let’s build a simple Blazor component to demonstrate these lifecycle methods. The component will:

- Fetch data asynchronously on initialization.
- Log parameter updates.
- Optimize rendering logic.

**Component Code:**

```razor
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

### Running the Demo

- Set the `InputParameter` from the parent component or route.
- Observe the console logs for lifecycle method execution.
- Note how rendering is controlled using `ShouldRender`.

---

## Best Practices for Using Lifecycle Methods

1. **Minimize Work in Render Methods:** Avoid heavy computation in `ShouldRender` and `OnAfterRender`.
2. **Async Initialization:** Use `OnInitializedAsync` for asynchronous tasks.
3. **Dispose Resources:** Always implement `IDisposable` for components holding unmanaged resources.
4. **Optimize Re-renders:** Leverage `ShouldRender` to prevent unnecessary updates.

---

## Conclusion

Blazor’s component lifecycle provides a powerful way to manage state, respond to changes, and optimize performance. Mastering these lifecycle methods can significantly enhance your ability to build robust and efficient applications. In the next episode, we’ll explore advanced component interactions and state management.

Stay tuned and happy coding!



