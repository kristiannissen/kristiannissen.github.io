---
layout: post
title:  "Laravel Events"
date:   2020-06-19 12:31:00 +0200
categories: laravel
---
Egentlig skal jeg ikke bruge Events til noget specielt i den applikation jeg arbejder på, det er vel blevet et COVID-19 hobby projekt for bare at holde gang i de små grå.

## Laravel Events
Det gik ikke helt som præsten prædiker, eller i dette tilfælde, som det stod skrevet i Laravel 7 dokumentationen. Det er også muligt mine tømmermænd spiller mig et pus, fokuseret læsning er sgu ikke så nemt når hjerne er optaget af selvynk.

Men efter lidt læsning fik jeg det på plads. Jeg forstod dokumentationen på den måde at jeg bare skulle registrere mine events og så ville Laravel på magisk vis oprette mapper og filer for mig... Not quit.

Men jeg fik oprettet min event klasse med CLI kommandoen php artisan make:event ImageProcessed
```
<?php

namespace App\Events;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

use App\File as ImageFile;

class ImageProcessed
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    /**
     * Create a new event instance.
     *
     * @return void
     */

    public $image_file;

    public function __construct(ImageFile $image_file)
    {
      //
      $this->image_file = $image_file;
    }

    /**
     * Get the channels the event should broadcast on.
     *
     * @return \Illuminate\Broadcasting\Channel|array
     */
    public function broadcastOn()
    {
        return new PrivateChannel('channel-name');
    }
}
```
Og efterfølgende min listener på samme måde, altså php artisan make:listener SendImageProcessedNotification
```
<?php

namespace App\Listeners;

use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Support\Facades\Log;

use App\Events\ImageProcessed;
use App\File as ImageFile;

class SendImageProcessedNotification
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
     * @param  object  $event
     * @return void
     */
    public function handle(ImageProcessed $event)
    {
      //
      $files_count = ImageFile::where('model_id', '=', $event->image_file->model_id)->count();
      Log::debug("$files_count files created");
    }
}
```
Men som jeg skrev tidligere, skal jeg ikke bruge denne event til noget, andet end at teste hvordan det fungerer i Laravel.

Registrering af min event og listener sker i App\Providers\EventServiceProvider
```
    protected $listen = [
        Registered::class => [
            SendEmailVerificationNotification::class,
        ],
        ImageProcessed::class => [
          SendImageProcessedNotification::class,
        ],
    ];
```
Ved hjælp af tinker kan jeg så teste logikken, heldigvis husker tinker min sidste kommando så selv om det ikke er muligt at reloade en session, så hjælper tinker alligevel ved at huske sidste kommando.
```
Psy Shell v0.10.4 (PHP 7.3.18 — cli) by Justin Hileman
>>> App\Jobs\ProcessImage::dispatch(App\File::first());
=> Illuminate\Foundation\Bus\PendingDispatch {#3980}
>>> App\Jobs\ProcessImage::dispatch(App\File::first());
=> Illuminate\Foundation\Bus\PendingDispatch {#3991}
>>>
```
Og i min log kan jeg se beskeden "26 files created".

It twerks!