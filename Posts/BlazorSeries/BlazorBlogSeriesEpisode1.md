---
title: "Úvod do Blazoru, WebAssembly, SignalR a hosting modelů"
description: "Úvod do Blazoru, WebAssembly, SignalR a hosting modelů"
author: "Ondrej Sevcak"
date: "2024-12-30"
category: "Blazor"
hashtags: "#CSharp #DotNet #Blazor"
publish: true
---

## Co je Blazor

Blazor je open-source SPA (single-page-application) webový framework pro vytváření full-stack aplikací vytvořený a udržovaný společností Microsoft.

Co je na Blazoru zajímavé, je to, že **můžete psát svůj klientský kód v C# a ten poběží v prohlížeči** (buď přímo v prohlížeči pomocí WebAssembly, nebo bude kód vykonáván na serveru a UI bude aktualizováno v reálném čase pomocí připojení SignalR).

Takže nemusíte znát javascriptové frameworky jako React, Vue.js nebo Angular, abyste mohli vytvářet full-stack aplikace, a proto je Blazor skvělou volbou pro vývojáře C#, kteří chtějí vytvářet kompletní aplikace.

Existuje několik různých typů Blazor aplikací, které můžete vytvořit:

- Blazor Server
- Blazor WebAssembly
- Blazor Auto

## Blazor Server

Blazor Server používá SignalR k usnadnění komunikace v reálném čase mezi klientem a serverem, což umožňuje vykonávání C# kódu na serveru a aktualizaci DOM na klientovi.

Jak Blazor Server funguje:

1. **Počáteční vykreslení stránky**
    - Když uživatel přistoupí k aplikaci Blazor Server, server vykreslí počáteční HTML a pošle ho klientovi (prohlížeči).
2. **Připojení SignalR**
    - Jakmile je počáteční HTML načteno, Blazor naváže připojení SignalR mezi klientem a serverem.
3. **Aktualizace komponent**
    - Když na klientovi dojde k události (například kliknutí na tlačítko), událost je odeslána na server přes připojení SignalR.
    - Server zpracuje událost, vykoná odpovídající C# kód a určí změny ve stavu komponenty.
    - Server poté vygeneruje rozdíl (minimální sadu změn) k aktualizaci vykreslené komponenty.
4. **Aktualizace DOM**
    - Rozdíl je odeslán zpět klientovi přes připojení SignalR.
    - Blazor JavaScript runtime na klientovi aplikuje tyto změny na DOM, aktualizuje uživatelské rozhraní bez úplného obnovení stránky.

## Co je SignalR

..podle [Microsoft Learn](https://learn.microsoft.com/en-us/aspnet/core/signalr/introduction?view=aspnetcore-8.0)..

*"ASP.NET Core SignalR je open-source knihovna, která zjednodušuje přidávání funkcionality v reálném čase do aplikací. Funkcionalita v reálném čase umožňuje serverovému kódu okamžitě posílat obsah klientům. SignalR poskytuje API pro vytváření vzdálených procedurálních volání (RPC) ze serveru na klienty. RPC volání spouští funkce na klientech z serverového .NET Core kódu."*

SignalR nám umožňuje:

- posílat aktualizace s vysokou frekvencí ze serveru na klienta
- posílat zprávy všem připojeným klientům současně
- posílat zprávy konkrétním klientům nebo skupinám klientů
- automaticky spravuje připojení
- podporuje WebSockets, Server-Sent events, Long Polling

**Blazor používá připojení SignalR mezi serverem a klientem k vykonávání C# na serveru a odesílání aktualizací do DOM na klientovi.**

![Blazor Server](https://ondrejsevcak.github.io/img/blazor-server.png)

## Blazor WebAssembly (WASM)

je klientský hostingový model Blazoru, který umožňuje spouštět C# kód přímo v prohlížeči pomocí WebAssembly.

Jak Blazor WebAssembly funguje:

1. **Počáteční načtení**
    - Když uživatel přistoupí k aplikaci Blazor WebAssembly, prohlížeč stáhne malý bootstrapovací HTML soubor, Blazor WebAssembly runtime, aplikační DLL a .NET runtime ve formátu WebAssembly.
    - Prohlížeč požádá o počáteční HTML stránku.
    - HTML stránka obsahuje odkaz na Blazor WebAssembly JavaScript soubor (blazor.webassembly.js).
    - Skript blazor.webassembly.js je vykonán, což načte .NET runtime a aplikační DLL.
2. **Vykonávání WebAssembly**
    - WebAssembly binární soubor je vykonán JavaScriptovým enginem prohlížeče, který inicializuje .NET runtime a načte aplikační sestavení.
3. **Spouštění C# kódu**
    - Aplikační C# kód běží v prohlížeči prostřednictvím .NET runtime ve WebAssembly.
4. **Vykreslování komponent**
    - Blazor komponenty jsou vykresleny pomocí WebAssembly runtime do DOM.
    - Uživatelské interakce (například kliknutí) spouštějí události, které jsou zpracovány Blazor WebAssembly runtime.
5. **Aktualizace UI**
    - Když se změní stav komponenty, Blazor runtime vygeneruje rozdíl a aktualizuje DOM podle potřeby.

![Blazor WASM](https://ondrejsevcak.github.io/img/blazor-webassembly.png)

## Co je WebAssembly

..podle [MDN docs](https://developer.mozilla.org/en-US/docs/WebAssembly)..

*"WebAssembly je typ kódu, který může být spuštěn v moderních webových prohlížečích — je to nízkoúrovňový jazyk podobný assembleru s kompaktním binárním formátem, který běží s téměř nativním výkonem a poskytuje jazykům jako C/C++, C# a Rust kompilaci cílového kódu, aby mohly běžet na webu. Je také navržen tak, aby běžel vedle JavaScriptu, což umožňuje oběma spolupracovat."*

WebAssembly nám umožňuje:

- spouštět kód v různých jazycích v prohlížeči (včetně C#)
- má téměř nativní rychlost vykonávání (rychlejší než JavaScript)
- běží spolu s JavaScriptem (může být kombinován)
- je bezpečný v tom, že - WebAssembly je specifikován tak, aby běžel v bezpečném, sandboxovaném prostředí
- není bezpečný v tom, že - wasm kód může být stažen a dekompilován, neměl by obsahovat důvěrné informace nebo kód

**Blazor používá WebAssembly k spouštění kompilovaného C# kódu v prohlížeči prostřednictvím .NET runtime, který je stažen do prohlížeče během počátečního načtení stránky.**

## Co je RenderTree

**Blazor používá abstrakční vrstvu mezi DOM a aplikačním kódem, nazvanou RenderTree. Je to lehká kopie stavu DOM složená ze standardních C# tříd.**

RenderTree může být aktualizován efektivněji než DOM a slučuje více změn do jedné aktualizace DOM. Aby byla maximalizována efektivita, RenderTree používá algoritmus pro porovnávání rozdílů, aby zajistil, že aktualizuje pouze nezbytné prvky v DOM prohlížeče.

Proces mapování DOM do RenderTree může být řízen pomocí direktivy key. Řízení tohoto procesu může být nezbytné v určitých scénářích, které vyžadují zachování kontextu různých prvků DOM při aktualizaci DOM.

**..takže jsme již obeznámeni s různými hostingovými modely, před .NET 8 jsme museli při vytváření nového projektu volit mezi WASM a Blazor Server.**

## Renderovací režimy vydané v .NET 8

Blazor v .NET 8 nám umožňuje měnit renderovací režim pro různé komponenty ve stejné aplikaci.
Nyní můžeme nastavit renderovací režim na jednu z následujících možností:

- **None** - pouze statické renderovací soubory, žádná interaktivita
- **WebAssembly** - interaktivita prostřednictvím kódu běžícího ve WebAssembly
- **Server** - interaktivita prostřednictvím připojení SignalR mezi klientem a serverem
- **Auto** - možnost nastavit Server nebo WebAssembly nebo obojí pro komponenty v jedné aplikaci

### Šablony projektů a renderovací režimy v .NET 8

V .NET 8 můžeme vytvořit pouze dva typy Blazor projektů s následujícími možnostmi renderovacích režimů:

<table style="border: 1px solid black;">
    <thead style="border: 1px solid black;">
        <tr style="border: 1px solid black;">
            <th style="border: 1px solid black;">Název šablony</th>
            <th style="border: 1px solid black;">Interaktivita</th>
        </tr>
    </thead>
    <tbody style="border: 1px solid black;">
        <tr>
            <td>Blazor Web App</td>
            <td>Server</td>
        </tr>
        <tr>
            <td>Blazor Web App</td>
            <td>WebAssembly</td>
        </tr>
        <tr>
            <td>Blazor Web App</td>
            <td>Auto</td>
        </tr>
        <tr>
            <td>Blazor WebAssembly Standalone App</td>
            <td>WebAssembly ve výchozím nastavení (žádný renderovací režim)</td>
        </tr>
    </tbody>    
</table>