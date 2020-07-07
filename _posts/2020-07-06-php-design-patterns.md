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
Det navn er vist den mere moderne udgave af et ældre design pattern som jeg ikke lige kan huske navnet på (jeg kom efterfølgende i tanke om, at det hedder et Factory Method Pattern), men jeg brugte det da jeg løste en [kode](https://github.com/kristiannissen/morningcoffee/tree/master/src/MorningCoffee) udfordring. Ved at bruge det design pattern i min PHP løsning, blev det muligt nemt at skifte den template parser ud jeg havde skrevet, men en der kan forstå en anden syntaks.

## Factory Method
Ved at anvende et interface, er det muligt at udskifte parseren i min kode udfordring.

{% highlight php %}
<?php
/**
 * @ParserInterface
 */
namespace MorningCoffee;

interface ParserInterface
{
    /**
     * @param string $content
     * @return PHP output
     */
    public function parse(string $content);
}
{% endhighlight %}

Når jeg instantierer min klasse, instantierer jeg selve interfacet i stedet for den konkrete klasse som i dette tilfælde er en BashParser klasse ;) Jeg synes selv det var ret sjovt at bruge bash som template sprog. Helt vildt sjovt...

Selve parseren der implementerer mit interface.

{% highlight php %}
<?php
/**
 * BashParser class
 */
namespace MorningCoffee;

use MorningCoffee\ParserInterface;
use MorningCoffee\Str;

class BashParser implements ParserInterface
{
    public function __construct()
    {
        $this->content = "";
    }

    /**
     * @param string $content
     */
    public function parse(string $content)
    {
    }
}
{% endhighlight %}

Og til sidst instantieringen i min template løsning.

{% highlight php %}
<?php

namespace MorningCoffee;

use MorningCoffee\CoffeeException;
use MorningCoffee\ParserInterface;
/**
 * class Coffee
 */
class Coffee
{
    protected $file_path;
    protected $file_contents;
    protected $parser;

    /**
     * @param ParserInterface $parser
     */
    public function __construct(ParserInterface $parser)
    {
        $this->file_path = "";
        $this->file_content = "";
        $this->parser = $parser;
    }
}
{% endhighlight %}

Bemærk at det ikke er BashParseren jeg kalder når jeg instantierer klassen, i stedet bruger jeg det interface jeg har lavet. Hvis jeg ville lave en anden parser for at benytte et andet template sprog, skal jeg blot implementere mit interface og føje den til her under.

{% highlight php %}
use MorningCoffee\Coffee;
use MorningCoffee\BashParser;

$my_coffee = new Coffee(new BashParser);
{% endhighlight %}

Det kræver lidt mere arbejde, der skal skrive noget mere kode til og der skal tænkes mere over løsningen inden de første linje kode skrives, men det betaler sig virkelig når du skal i gang med at ændre i din løsning.

## Factory Method
Factory Method design pattern hører til i en gruppe af design patters som kaldes for Creational Patterns, i samme gruppe finder du Abstract Factory, Builder, Object Pool, Prototype og Singleton, nogle af disse er mere anvendt end andre.