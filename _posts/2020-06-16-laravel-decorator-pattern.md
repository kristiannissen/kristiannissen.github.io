---
layout: post
title:  "Laravel Decorator Pattern"
date:   2020-06-16 22:19:00
categories: laravel
---
Den løsning jeg har valgt til, hvordan billeder hentes for en enkelt blog post, er jeg ikke glad for. Udfordringen er, at jeg gør brug af Cache inde i min model, hvilket bare er noget snask. Modellen skal ikke vide noget om Caching, det hører til ude i Controlleren, ikke i Modellen.
```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Facades\Cache;

use App\Activity;
use App\File;

class BlogPost extends Model
{
  ...
}
```
Caching strategien hører bare ikke hjemme i en Data Access Object. Det bliver sgu et nej tak her fra, det skal jeg ikke bede om.

## Decorator Pattern
I tidernes morgen da jeg læste Gang of Four for at lære om brugen af Design Patterns, lærte jeg om Decorator Pattern.
Hvis du har fundet denne blog post, ved du hvad du leder efter og derfor gider jeg ikke kede dig med hvad decorator pattern er.

Det jeg netop var på udkig efter skulle hjælpe mig til at give min Model nogle funktioner som normalt ikke hører til i en Model. Jeg fandt [Ahmad Ashrafs](https://dev.to/ahmedash95/design-patterns-in-php-decorator-with-laravel-5hk6) artikel på dev.to om netop [dette emne](https://dev.to/ahmedash95/design-patterns-in-php-decorator-with-laravel-5hk6). Han beskriver samme problematik som den jeg sidder med og hans løsningsforslag skal prøves af. Du skal ca. halvvejs ned i artiklen for at finde eksemplet der er interessant.

Så bliver jeg sikkert også nødt til at skrive min [ProcessImage](https://github.com/kristiannissen/kaleidoscope/blob/master/app/Jobs/ProcessImage.php) om og [refaktorere min](https://github.com/kristiannissen/kaleidoscope/blob/master/app/File.php) File model så den bliver til en polymorphic model i stedet og får et knap så generisk navn. Den idé tror jeg sgu er "lånt" af Rails Active Record...