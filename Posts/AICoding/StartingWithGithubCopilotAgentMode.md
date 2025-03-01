---
title: "Jak začít s GitHub copilot agentem ve VSCode"
description: "Jak začít s GitHub copilot agentem ve VSCode"
author: "Ondrej Sevcak"
date: "2025-02-27"
category: "AICoding"
hashtags: "#CSharp #DotNet #AI #VSCode"
---

### Jak začít s GitHub Agent módem ve VS Code

1. Nainstalujte si VSCode, pokud jej ještě nemáte
2. Přihlašte se do VSCode svým GitHub účtem, kde máte k dispozici GitHub copilot - nyní mají všichni free
3. Nainstalujte si extensionu C# Dev kit - [Jak na to](https://code.visualstudio.com/docs/csharp/get-started)
4. Vytvořte si nový projekt - klidně Console aplikaci
5. Ve VSCode si otevřete Copilot chat a přepnete se na Copilot edits

![VSCode](https://raw.githubusercontent.com/OndrejSevcak/BlazorBlogStorage/refs/heads/main/Posts/AICoding/GHCopilotEditsVSCode.png)

### Co budeme programovat?

Naším cílem bude vyvinout **high-performance processor objednávek**
Tedy program, který bude zpracovávat velké množství objednávek v pořadí, v jakém přijdou.

### 1. Vytvoření základních modelů

- prvním krokem je vytvoření základních modelů, vytvoří si tedy složku Models, nový soubor Order.cs a napíšu komentář:
    *//generate model class for Order*

- stejným způsobem si vygeneruji modely pro třídy Product.cs, Customer.cs a OrderItem.cs

- nyní napíšu prompt do chatu copilot agenta aby do třídy Order.cs přidal kolekci OrderItems



