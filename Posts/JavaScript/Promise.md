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

```javascript
const promise = doSomething();
const promise2 = promise.then(successCallback, failureCallback);
``` 

TO BE CONTINUED