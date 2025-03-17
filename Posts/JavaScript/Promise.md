---
title: "What is Promise"
description: "What is Promise in JavaScript"
author: "Ondrej Sevcak"
date: "2025-03-17"
category: "JavaScript"
hashtags: "#JavaScript #Web #Development"
publish: true
---

#### Co je Promise objekt?

**Promise** v javascriptu reprezentuje **stav** asynchronní operace a její **výslednou hodnotu**

Promise má vždy jeden z následujících 3 stavů:

 - **pending** - prvotní (inital) stav, následující stav je jeden ze 2 následujících: 
 - **fulfilled** - značí, že asynchornní operace byla **úspěšně dokončena**
 - **rejected** - značí, že asynchornní operace skončila chybou (failed)


 Prvotní stav **pending** může být buďto **fulfilled**, tedy dokončený s výslednou hodnotou nebo **rejected**, tedy odmítnutý s nějakou chybou

 Pojďmě se na to podívat jinak. 

  - **Promise** je objekt, který vrátí funkce.
  - k tomuto vrácenému objektu připojujeme **callback funkce**, reagující na výsledek (success / fail)
  - je to jiný přístup než vkládat callback funkce jako parametry přímo do funkce

Příklad:

*Mějme funkci pro vytvoření objednávky, do které jako parametry vkládáme callback funkce, které reagují na výsledný stav vytvoření objednávky*

```javascript
function successCallback(result){
    console.log(`Order ${result.id} has been created`)
}

function failureCallback(error){
    console.log(`Order creation has failed: ${error}`)
}

createOrderAsync(orderData, successCallback, failureCallback);
```

*Pokud by funkce createOrderAsync vracela **Promise**, callback funkce bychom nevkládali jako parametry ale připojili je pomocí **.then()***

```javascript
createOrderAsync(orderData).then(successCallback, failureCallback);
```

#### Promise Chaining

 - **Chaining** je způsob navazování asynchornních operací

 Klíčem úspěchu je funkce **then()**, která vrací nový **Promise**, tedy rozdílný od toho původního 

 Další metody k navázání callback funkcí jsou **catch()** a **finally()**

```javascript
const promise = doSomething();
const promise2 = promise.then(successCallback, failureCallback);
``` 

Bez použí **Promise chaining** se dostáváme do tzv. **Callback hell**

```javascript
//deklarace funkcí s callback funkcemi jako paramtery
function getUser(callback, errorCallback) {
    setTimeout(() => {
        console.log("Fetched user");
        callback({ userId: 1, name: "John Doe" });
    }, 1000);
}

function getOrders(userId, callback, errorCallback) {
    setTimeout(() => {
        console.log("Fetched orders for user", userId);
        callback([{ orderId: 101, total: 50 }, { orderId: 102, total: 75 }]);
    }, 1000);
}

function getOrderDetails(orderId, callback, errorCallback) {
    setTimeout(() => {
        console.log("Fetched order details for order", orderId);
        callback({ orderId: orderId, items: ["Laptop", "Mouse"] });
    }, 1000);
}

//použití funkcí pomocí klasických callback parametrů
getUser(function (user) {
    getOrders(user.userId, function (orders) {
        getOrderDetails(orders[0].orderId, function (orderDetails) {
            console.log("Final Order Details:", orderDetails);
        }, console.error);
    }, console.error);
}, console.error);
```

**Stejné funkce s použitím Promises**

```javascript
//deklarace funkcí vracejícíh promise
function getUser() {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve({ userId: 1, name: "John Doe" }), 1000);
    });
}

function getOrders(userId) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve([{ orderId: 101, total: 50 }, { orderId: 102, total: 75 }]), 1000);
    });
}

function getOrderDetails(orderId) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve({ orderId, items: ["Laptop", "Mouse"] }), 1000);
    });
}

//volání funkcí pomocí then() a catch()
getUser()
    .then(user => getOrders(user.userId))
    .then(orders => getOrderDetails(orders[0].orderId))
    .then(orderDetails => console.log("Final Order Details:", orderDetails))
    .catch(console.error);
```

Syntaxe s použitím Promises je mnohem čitelnější a mnohem lépe se udržuje

TO BE CONTINUED