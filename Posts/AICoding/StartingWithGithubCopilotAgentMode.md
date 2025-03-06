---
title: "Jak začít s GitHub Copilot Edits VSCode (agent mode comming)"
description: "Jak začít s GitHub Copilot Edits VSCode (agent mode comming)"
author: "Ondrej Sevcak"
date: "2025-02-27"
category: "AICoding"
hashtags: "#CSharp #DotNet #AI #VSCode"
publish: true
---

### Co je GitHub Copilot Edits

Copilot Edits je funkcionalita GitHub Copilota, která umožňuje inteligentní úpravy kódu na základě kontextu a návrhů generovaných AI. Pomáhá automatizovat úpravy a na základě promptů dělá refaktoring kódu - v edioru interaktivně vidíte, jaká upravuje soubory ve vašem projektu

- je to tedy taková interaktivní AI asistovaná coding session, píšete co chcete změnit a copilot to dělá za vás
- vaším úkol je následné změny zrevidovat a případně urpavit dle svých preferencí


### Jak začít s GitHub Copilot Edits ve VS Code s jazykem C#

1. Nainstalujte si Visual Studio Code, pokud jej ještě nemáte - [k dispozici zde](https://code.visualstudio.com/)
2. Přihlašte se do VSCode svým GitHub účtem, kde máte k dispozici GitHub copilot - nyní mají všichni free verzi
3. Nainstalujte si extensionu C# Dev kit - [Jak na to](https://code.visualstudio.com/docs/csharp/get-started)
4. Vytvořte si nový projekt - klidně Console aplikaci
5. Ve VSCode si otevřete Copilot chat a přepnete se na Copilot edits

![VSCode](https://raw.githubusercontent.com/OndrejSevcak/BlazorBlogStorage/refs/heads/main/Posts/AICoding/GHCopilotEditsVSCode.png)


- do pole *Edit files in your workspace* již můžete zadávat prompty na základě kterých bude Copilot sám navrhovat změny v jednotlivých souborech projektu

### Agent mode - aktuálně v preview (psáno 06.03.2025)

- nyní se chystá kromě Edit mode také Agent mode, který je popsán následovně:

*In agent mode, Copilot Edits operates in a more autonomous and dynamic manner to achieve the desired outcome. Copilot agent mode determines the relevant context, offers both code changes and terminal commands, and iterates to remediate issues. To perform these tasks, agent mode uses a set of tools.*

Upřímně se těším, až to vyzkouším a porovnám například s Cursor IDE. 

*Dostupnost*

*Note: Agent mode is available today in VS Code Insiders, and we just started rolling it out gradually in VS Code Stable. Once agent mode is enabled for you, you will see a mode dropdown in the Copilot Edits view — simply select Agent.*

Takže pokud chcete Agent mode vyzkoušet už nyní, můžete si stáhnout VS Code Insiders [zde](https://code.visualstudio.com/insiders/)

Více info o Agent módu [zde](https://code.visualstudio.com/docs/copilot/copilot-edits)

