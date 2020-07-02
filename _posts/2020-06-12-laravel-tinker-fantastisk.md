---
layout: post
title:  "Laravel Tinker er fantastisk"
date:   2020-06-12 10:30:10 +0200
categories: laravel tinker
tags: [Laravel, Tinker]
excerpt_separator: <!--more-->
---
Med Tinker kan du teste kode direkte i terminalen.
<!--more-->
Start tinker
{% highlight php %}
php artisan tinker
Psy Shell v0.10.4 (PHP 7.3.18 — cli) by Justin Hileman
>>> _
{% endhighlight %}

Nu kan du teste kode uden at skulle skrive en Unit test eller reloade browseren igen... og igen... og igen

{% highlight PHP %}
use App\File;
use App\Jobs\ProcessImage;

$file = File::first();
=> App\File {#3981
     id: "19",
     created_at: "2020-06-11 17:08:47",
     updated_at: "2020-06-11 17:08:47",
     file_name: "public/theme_images/KLcRiAfkAOk1IQE6vTbzcAFuGeftGhJhWIc61LXW.jpeg",
     mimetype: "image/jpeg",
     role: "hero_image",
     model_name: "BlogPost",
     model_id: "51",
     priority: "1",
     file_size: "original",
   }
ProcessImage::dispatch($file);
=> Illuminate\Foundation\Bus\PendingDispatch {#3986}
{% endhighlight %}

Det er genialt, intet mindre! Det er lig hvad du kan gøre når du arbejder i Python, det kan spare dig for en pokkers masse tid, og så ser det lidt cool ud at flippe ud i terminalen ;)

Det eneste jeg savner er muligheden for at kunne reloade min tinker session, så ændringer i koden jeg tester, i dette tilfælde ProcessImage job som skal bruges til at skalere et billede som uploades af brugeren.

## Brug terminalen
Et andet tip når du arbejder med Laravel er at lære at bruge **Log::debug()** funktionen og hele tiden have en tab åben, hvor du kan se, hvad du skriver i loggen.

Du finder Laravel debug loggen under

{% highlight PHP %}
storage/logs/laravel.log
{% endhighlight %}

**Log::debug()** skriver til denne log og du kan selvfølgelig manuelt åbne filen og se hvad du har skrevet i loggen, eller du kan følge den interaktivt og hele tiden få seneste log besked skrevet ud i din terminal

{% highlight PHP %}
tail -f storage/logs/laravel.log
{% endhighlight %}

Så når du kombinerer Tinker med tail har et setup som gør at du hele tiden kan følge dine ændringer.

Som VIM bruger er det et perfekt setup fordi jeg allerede laver alt mit arbejde i terminalen.

### Opdatering den 1. juli
En kommando som er nyttig og hvor tinker er virkelig god er når du skal cleare din applications cache.

{% highlight PHP %}
>>> Illuminate\Support\Facades\Cache::flush();
=> true
{% endhighlight %}
