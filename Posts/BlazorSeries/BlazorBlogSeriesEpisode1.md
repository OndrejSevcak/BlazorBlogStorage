---
title: "Intro to Blazor"
description: "Intro to Blazor, WebAssembly, SignalR and Hosting models for Blazor in .NET 8"
author: "Ondrej Sevcak"
date: "2024-12-30"
category: "Blazor"
hashtags: "#CSharp #DotNet #Blazor"
---


## What is Blazor

Blazor is an open-source SPA(single-page-application) web framework for building full-stack applications created and maintained by Microsoft.

What is interesting about Blazor is that you **can write your client code in C# and it will run in the browser** (either directly in browser using WebAssembly or the code will be executed on the server and UI will be updated in real-time using SignalR connection).

So you do not have to know javascript frameworks like React, Vue.js or Angular to build full-stack applications and therefore Blazor is a great option for C# developers who want to build complete applications.

There are a few different types of Blazor applications you can create:

- Blazor Server
- Blazor WebAssembly
- Blazor Auto

## Blazor Server

Blazor Server uses SignalR to facilitate real-time communication between the client and server, allowing the execution of C# code on the server and updating the DOM on the client.

How Blazor Server works:


1. **Initial page render**
    - When a user accesses a Blazor Server app, the server renders the initial HTML and sends it to the client (browser).
2. **SignalR connection**
    - Once the initial HTML is loaded, Blazor establishes a SignalR connection between the client and the server.
3. **Component updates**
    - When an event (such as a button click) occurs on the client, the event is sent to the server over the SignalR connection.
    - The server handles the event, executes the corresponding C# code, and determines the changes to the component's state
    - The server then generates a diff (a minimal set of changes) to update the rendered component.
4. **DOM updates**
    - The diff is sent back to the client over the SignalR connection.
    - The Blazor JavaScript runtime on the client applies these changes to the DOM, updating the user interface without a full page reload.

## What is SignalR

..as per the [Microsoft Learn](https://learn.microsoft.com/en-us/aspnet/core/signalr/introduction?view=aspnetcore-8.0)..

*"ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps. Real-time web functionality enables server-side code to push content to clients instantly. SignalR provides an API for creating server-to-client remote procedure calls (RPC). The RPCs invoke functions on clients from server-side .NET Core code"*

So SingnalR enables us to:

- send high frequency updates from the server to the client
- send messages to all connected clients simultaneously
- send messages to specific clients or groups of clients
- it handles connection management automatically
- supports WebSockets, Server-Sent events, Long Polling

**Blazor uses SignalR connection between server and client to execute C# on the server and send updates to the DOM on the client.**

![Blazor Server](https://ondrejsevcak.github.io/img/blazor-server.png)

## Blazor WebAssembly (WASM)

is a client-side hosting model of Blazor, which allows you to run C# code directly in the browser using WebAssembly.

How Blazor WebAssembly works:

1. **Initial load**
    - When a user accesses a Blazor WebAssembly application, the browser downloads a small bootstrapping HTML file, the Blazor WebAssembly runtime, the application DLLs, and the .NET runtime in WebAssembly format.
    - The browser requests the initial HTML page.
    - The HTML page includes a reference to the Blazor WebAssembly JavaScript file (blazor.webassembly.js).
    - The blazor.webassembly.js script is executed, which loads the .NET runtime and application DLLs.
2. **WebAssembly execution**
    - The WebAssembly binary is executed by the browser’s JavaScript engine, which initializes the .NET runtime and loads the application assemblies.
3. **Running C# code**
    - The application’s C# code runs in the browser via the .NET runtime in WebAssembly.
4. **Rendering Components**
    - Blazor components are rendered using the WebAssembly runtime into the DOM.
    - User interactions (such as clicks) trigger events that are handled by the Blazor WebAssembly runtime.
5. **UI updates**
    - When a component’s state changes, the Blazor runtime generates a diff and updates the DOM accordingly.

![Blazor WASM](https://ondrejsevcak.github.io/img/blazor-webassembly.png)

## What is WebAssembly

..as per the [MDN docs](https://developer.mozilla.org/en-US/docs/WebAssembly)..

*"WebAssembly is a type of code that can be run in modern web browsers — it is a low-level assembly-like language with a compact binary format that runs with near-native performance and provides languages such as C/C++, C# and Rust with a compilation target so that they can run on the web. It is also designed to run alongside JavaScript, allowing both to work together."*

So WebAssembly enables us to:

- run code of multiple languages in the browser (including C#)
- has near-native execution speed (faster than javascript)
- runs together with javascript (can be combined)
- is secure in that - WebAssembly is specified to be run in a safe, sandboxed execution environment
- is not secure in that - wasm code can be downloaded and decompiled, should not contain confidential information or code

**Blazor uses WebAssembly to run compiled C# code in the browser via .NET runtime, which is downloaded to the browser during initial page load.**

## What is a RenderTree

**Blazor uses an abstraction layer between the DOM and the application code, called a RenderTree. It is a lightweight copy of the DOM's state composed by standard C# classes.**

The RenderTree can be updated more efficiently than the DOM and reconciles multiple changes into a single DOM update. To maximize effectiveness the RenderTree uses a diffing algorithm to to ensure it only updates the necessary elements in the browser’s DOM.

The process of mapping a DOM into a RenderTree can be controlled with the key directive. Controlling this process may be necessary in certain scenarios that require the context of different DOM elements to be maintained when a DOM is updated.

**..so we are already familiar with the different hosting models, prior .NET 8 we had to choose between WASM and Blazor Server when creating a new project.**

## Render modes released in .NET 8

Blazor in .NET 8 allows us to change the render mode for different components in the same application.
Now we can set a rendermode to one of the following options:

- **None** - only static render files, no interactivity
- **WebAssembly** - interactivity via code running in WebAssembly
- **Server** - interactivity via SignalR connection between Client and Server
- **Auto** - ability to set Server or WebAssembly or both per component in single application

### Project templates and render modes in .NET 8

In .NET 8, we can create only two Blazor project types with following render modes options:

<table style="border: 1px solid black;">
    <thead style="border: 1px solid black;">
        <tr style="border: 1px solid black;">
            <th style="border: 1px solid black;">Template Name</th>
            <th style="border: 1px solid black;">Interactivity</th>
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
            <td>WebAssembly by default (no render mode)</td>
        </tr>
    </tbody>    
</table>