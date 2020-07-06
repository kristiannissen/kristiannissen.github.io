---
layout: post
title:  "PHP Design Patterns"
date:   2020-07-06 19:24:00 +0200
categories: php
tags: [PHP, Design Patterns]
excerpt_separator: <!--more-->
---
Design Patterns er sgu ret cool! Jeg kan huske, hvordan jeg tilbage i ca 2005 sad og svedte over Gang Of Four som vi brugte til at lære om Design Patterns i Java. Senere læste jeg Head First udgaven men det satte sig ikke rigtigt fast, indtil jeg i 2006/2007 var dybt involveret i at udvikle en PPC baseret jobdatabase løsning sammen med nogle andre gutter.
<!--more-->
Pludselig kunne jeg godt se ideen i, at splitte koden op og følge de anvisninger jeg fandt på en hjemmeside om Java Enterprice Patterns. Det var ikke Java men PHP jeg brugte til løsningen og jeg havde det hele kørende med Data Access Objects, interfaces osv. Det var også i den forbindelse at de første spæde linjer til Uteeni blev skrevet.
## Decorator Pattern
I forbindelse med mit Covid-19 hygge projekt fik jeg brug for at cache data i Laraval. Jeg ville ikke have caching metoderne ind i min model, dér hører de ikke til, det ville være en forkert måde at implementere det på.

Men f.eks at bruge et [decorator pattern, eller Repository pattern](/laravel/2020/06/16/laravel-decorator-pattern.html) som det hedder i Laravel, betyder at jeg kunne føje et abstraktionslag ind i min løsning som gav mig det jeg havde brug for.

## Interface Injection
Det navn er vist den mere moderne udgave af et ældre design pattern som jeg ikke lige kan huske navnet på, men jeg brugte det da jeg løste en [kode](https://github.com/kristiannissen/morningcoffee/tree/master/src/MorningCoffee) udfordring. Ved at bruge det design pattern i min PHP løsning, blev det muligt nemt at skifte den template parser ud jeg havde skrevet, men en der kan forstå en anden syntaks.