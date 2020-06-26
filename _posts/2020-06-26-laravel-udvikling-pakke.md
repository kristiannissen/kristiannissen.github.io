---
layout: post
title:  "Laravel udvikling af pakke"
date:   2020-06-26 12:08:00 +0200
categories: laravel
tags: [Laravel, Package]
excerpt_separator: <!--more-->
---
Som en del af den idé jeg har med at bruge Google Datastore i stedet for en relationel database kræver at jeg laver en pakke som kan integreres ind i Laravel.
<!--more-->
I et meget simpelt og hurtigt forsøg lavede jeg følgende klasse
```
<?php

namespace Hello\Kitty;

class HelloKitty {
  public static function says(bool $happy): string
  {
    return $happy == true ? 'purr purr' : 'sod off, perv!';
  }
}
```
Det ser ikke ud af meget, men er nok til at teste med. Klassen er i mappen "packages/hello/kitty/src". Efter den var oprettet navigerede jeg ind i mappen "packages/hello/kitty/" og brugte kommandoen "composer init" for at oprette en composer.json fil
```
{
    "name": "hello/kitty",
    "description": "A package for Kitties",
    "type": "library",
    "license": "MIT",
    "authors": [
        {
            "name": "Kristian Nissen",
            "email": "kristian.nissen@gmail.com"
        }
    ],
    "minimum-stability": "dev",
    "autoload": {
      "psr-4": {
        "Hello\\Kitty\\": "src/"
      }
    },
    "require": {}
}
```
Om det skal være "project" eller "library" skal jeg have fundet ud af, jeg har ikke sat mig ind i forskellen endnu.

Fra terminalen gik jeg tilbage til roden af mit projekt og åbnede composer.json for at fortælle composer at der er en ny pakke som skal være tilgængelig. Find følgende i composer.json
```
"autoload": {
    "psr-4": {
        "App\\": "app/"
    },
    "classmap": [
        "database/seeds",
        "database/factories"
    ]
},
```
Og ændre det til
```
"autoload": {
    "psr-4": {
        "App\\": "app/",
        "Hello\\Kitty\\": "packages/hello/kitty/src"
    },
    "classmap": [
        "database/seeds",
        "database/factories"
    ]
},
```
Hvis ellers alt er gået godt, vil composer dump-autoload kommandoen sørge for at klassen nu er tilgængelig i projektet.

Jeg gad ikke gøre så meget mere ud af testen end bare at se at klassen loades og er tilgængelig for mig i min applikation så jeg implementerede den i min ShowIndex.php controller og skrev output i loggen
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Log;

use App\BlogPost;
use Hello\Kitty\HelloKitty;

class ShowIndex extends Controller
{
    /**
     * Handle the incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function __invoke(Request $request)
    {
      Log::debug('Hello Kitty says ' . HelloKitty::says(false));
        // Show latest blog post
        $blog_posts = BlogPost::latest()
            ->limit(10)
            ->orderBy('online_at', 'desc')
            ->get();

        return view('index', [
          'blog_posts' => $blog_posts
        ]);
    }
}
```
I "storage/log/laravel.log" kan jeg se "Hello Kitty says sod off, perv!" og "Hello Kitty says purr purr" afhændig af om "HelloKitty::says()" er true eller false.

Det næste ville være at oprette et git repository til pakken så den kan hentes ind i de projekter hvor jeg vil gøre brug af den, men det bliver en anden god gang. I første omgang handlede det bare om at få testen på plads.

Laravel har også nogle andre [features](https://laravel.com/docs/7.x/packages) til udvikling af pakker som jeg skal teste.