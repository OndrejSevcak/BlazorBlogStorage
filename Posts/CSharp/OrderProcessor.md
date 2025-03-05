---
title: "Programujeme Order processor"
description: "Programujeme Order processor"
author: "Ondrej Sevcak"
date: "2025-03-04"
category: "CSharp"
hashtags: "#CSharp #DotNet #Multithreading"
publish: true
---

#### Cíl

- Naprogramovat high-performance service pro zpracování velkého počtu objednávek
- Objednávky by měly být zpracovány ve stejném pořadí, v jakém byly vytvořeny
- Využít maximálně dostnupnou výpočetní kapacitu

#### Modely

```csharp
    public enum OrderStatus
    {
        Pending,
        Shipped,
        Delivered
    }

    public enum ProductCategory
    {
        Electronics,
        Groceries,
        Books
    }

    public class Customer
    {
        public int CustomerId { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
        public string Phone { get; set; }
        public string Address { get; set; }
    }

    public class Product
    {
        public int ProductId { get; set; }
        public string Name { get; set; }
        public string Description { get; set; }
        public decimal Price { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class OrderItem
    {
        public int OrderItemId { get; set; }
        public int ProductId { get; set; }
        public int Quantity { get; set; }
        public decimal Price { get; set; }
        public decimal? Discount { get; set; }
    }

    public class Order
    {
        public int OrderId { get; set; }
        public int CustomerId { get; set; }
        public DateTime OrderDate { get; set; }
        public string OrderStatus { get; set; }
        public List<OrderItem> OrderItems { get; set; } = new List<OrderItem>();
    }
```

#### Vstupní data - JSON se 100 objednávkami

- [JSON](https://raw.githubusercontent.com/OndrejSevcak/CSharpDemos/refs/heads/master/OrderProcessor/ConsoleApp1/Data/orders.json)

#### Pomocná metoda pro načtení objednávek do paměti

```csharp
    public static class DataHelper
    {
        public static IEnumerable<Order>? LoadOrders(string filePath)
        {
            try
            {
                var json = File.ReadAllText(filePath);
                return JsonSerializer.Deserialize<IEnumerable<Order>>(json);
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error loading orders: {ex.Message}");
                return null;
            }
        }
    }
```
#### OrderProcessor - pro zpracování objednávek

Co budeme potřebovat:

- [BlockingCollection<T>](https://learn.microsoft.com/en-us/dotnet/standard/collections/thread-safe/blockingcollection-overview) 
- [Cancellation token](https://learn.microsoft.com/en-us/dotnet/standard/threading/cancellation-in-managed-threads)
- Producer-consumer pattern

#### The Producer / Consumer pattern

*The Producer/Consumer pattern is a design pattern used in multithreaded applications to manage shared resources efficiently. It involves two types of threads:
- Producer: Generates data and places it into a shared buffer or queue.
- Consumer: Retrieves data from the buffer and processes it*

#### The OrderProcessor class

```csharp
public class OrderProcessor
{

    private readonly BlockingCollection<Order> _orders = new BlockingCollection<Order>();
    private readonly ILogger<OrderProcessor> _logger;

    public OrderProcessor(ILogger<OrderProcessor> logger)
    {
        _logger = logger;
    }

    /// <summary>
    /// The ProcessOrdersSequentially method processes orders sequentially in the order they are received. 
    /// It uses a single consumer task to process orders one by one.
    /// </summary>
    /// <param name="orders"></param>
    /// <returns></returns>
    public Task ProcessOrdersSequentially(IEnumerable<Order> orders, CancellationToken cancellationToken)
    {
        // Create a producer task that adds orders to the blocking collection
        var producerTask = Task.Run(() =>
        {
            try
            {
                foreach (var order in orders)
                {
                    _orders.Add(order, cancellationToken);
                    Console.WriteLine($"Added order {order.OrderId} to the queue on thread {Task.CurrentId}");
                }
            }
            catch(OperationCanceledException)
            {
                _logger.LogInformation("The operation was canceled.");
            }
            finally
            {
                _orders.CompleteAdding();
            }
        }, cancellationToken);

        // Create a single consumer task to process orders sequentially
        var consumerTask = Task.Run(() =>
        {
            try
            {
                foreach (var order in _orders.GetConsumingEnumerable(cancellationToken))
                {
                    ProcessOrder(order, cancellationToken);
                }
            }
            catch(OperationCanceledException)
            {
                _logger.LogInformation("The operation was canceled.");
            }
        }, cancellationToken);

        return Task.WhenAll(producerTask, consumerTask);
    }

    /// <summary>
    /// The ProcessOrdersConcurently method processes orders concurrently using multiple consumer tasks.
    /// It creates a producer task to add orders to the blocking collection and multiple consumer tasks to process orders concurrently.
    /// </summary>
    /// <param name="orders"></param>
    /// <returns></returns>
    public Task ProcessOrdersConcurently(IEnumerable<Order> orders, CancellationToken cancellationToken)
    {
        // Create a producer task that adds orders to the blocking collection
        var producerTask = Task.Run(() =>
        {
            try
            {
                foreach (var order in orders)
                {
                    _orders.Add(order, cancellationToken);
                    Console.WriteLine($"Added order {order.OrderId} to the queue on thread {Task.CurrentId}");
                }
            }
            catch(OperationCanceledException)
            {
                _logger.LogInformation("The operation was canceled.");
            }
            finally
            {
                _orders.CompleteAdding();
            }
        }, cancellationToken);

        // Create multiple consumer tasks to process orders concurrently
        var consumerTasks = Enumerable.Range(0, Environment.ProcessorCount).Select(_ => Task.Run(() =>
        {
            try
            {
                foreach (var order in _orders.GetConsumingEnumerable())
                {
                    ProcessOrder(order, cancellationToken);
                }
            }
            catch(OperationCanceledException)
            {
                _logger.LogInformation("The operation was canceled.");
            }

        }, cancellationToken)).ToArray();

        return Task.WhenAll(producerTask, Task.WhenAll(consumerTasks));
    }

    private void ProcessOrder(Order order, CancellationToken cancellationToken)
    {
        try
        {
            // Simulate processing time
            Task.Delay(500, cancellationToken).Wait(cancellationToken);
            Console.WriteLine($"Processed order {order.OrderId} on thread {Task.CurrentId}");
        }
        catch(OperationCanceledException)
        {
            _logger.LogInformation("The operation was canceled.");
        }

    }
}
```