---
layout: post
title:  "Laravel Events og Listeners"
date:   2020-07-02 20:28:00 +0200
categories: laravel
tags: [Laravel, Events, Listeners]
excerpt_separator: <!--more-->
---
Til at gemme pageview data valgte jeg at bruge Events og Listeners, mest fordi jeg ikke kan finde ud af hvordan min data model skal se ud til at gemme data i.
<!--more-->
Jeg kunne selvfølgelig bare vælge at gemme en række af pageview data i et json-b felt i databasen og på den måde slippe for at skulle finde på en mere statisk datamodel. Det med at bruge migreringer til at rette fejl i data modellerne er jeg ikke en stor fan af. Så hellere bruge det mere tid på at undersøge mulighederne og finde på en god løsning.

## Oprette Event & Listener
Laravel artisan har de kommandoer du skal bruge for at oprette både Events og Listeners.

{% highlight php %}
php artisan make:event PageViewed
{% endhighlight %}

Opretter et Event til dig i mappen app/Events, og på samme måde kan du oprette en tilhørende Listener

{% highlight php %}
php artisan make:listener StorePageView --event=PageViewed
{% endhighlight %}

Fordi jeg fortæller den listener jeg laver at der er et tilhørende Event, bliver de 2 automatisk koblet sammen for mig.

{% highlight php %}
<?php

namespace App\Listeners;

use App\Events\PageViewed;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Support\Facades\Log;

class StorePageView
{
    /**
     * Create the event listener.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Handle the event.
     *
     * @param  PageViewed  $event
     * @return void
     */
    public function handle(PageViewed $event)
    {
        //
        Log::debug(var_dump($event));
    }
}
{% endhighlight %}

Det eneste jeg her har tilføjet er Log::debug(), ikke andet, resten tager Laravel sig af at oprette for mig... Jeg elsker når CLI værktøjer bare gør præcist det de skal så jeg ikke skal sidde og oprette og gemme filer!

Nu mangler jeg bare at registrere min nye Event og Listener, hvilket jeg gør i Laravels EventServiceProvider.

{% highlight PHP %}
protected $listen = [
    Registered::class => [
        SendEmailVerificationNotification::class,
    ],
    ImageProcessed::class => [
      SendImageProcessedNotification::class,
    ],
    PageViewed::class => [
      StorePageView::class,
    ],
];
{% endhighlight %}

Og så skal jeg selvfølgelig også dispatche min event, ellers sker der ikke en skid...

Jeg bruger Laravels API router til at opsamle pageview data med, jeg har baseret hele løsningens frontend på Googles AMP projekt og der er også en måde at fyre events af, på bestemte hendelser.

Her er den metode der bliver kaldt, hver gang der er en page view event fra klienten.

{% highlight PHP %}
public function store(Request $request, string $random_id)
{
    $all = $request->all();
    $all['random_id'] = $random_id;
    event(new PageViewed($all));
    return 'ok';
}
{% endhighlight %}

Ikke skide spændende men det eneste der skal ske er, at der fyres et Event af og at den Listener jeg har oprettet så gør, hvad den skal når denne Event fyres af.

Nu fik jeg oprettet den Laravel Event og Listener jeg skal bruge for at kunne gemme page view data, tilbage er så bare fortsat (!) at finde ud af, hvordan min database model skal se ud.