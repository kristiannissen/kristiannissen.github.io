---
layout: post
title:  "Laravel file upload med Storage"
date:   2020-06-14 10:00:00 +0200
categories: laravel
tags: [Laravel, Storage]
excerpt_separator: <!--more-->
---
Jeg søgte efter eksempler på Google for at finde ud af, hvad der er bedste praksis når det gælder file upload i en Laravel applikation. De første mange links jeg fandt viste alle samme eksempel, i mange tilfælge havde forfatteren ikke en gang gidet forholde sig til det kode idioten havde kopieret fra en anden blog! Enkelte gange var kodeeksemplerne ikke en gang læsbare pga manglende formattering af selve kodeeksemplet!
<!--more-->
Hvad fanden er det for noget lort... så hold dog op!

## Kodeeksempel
I den blog applikation jeg arbejder på, kan det et Corona-projekt, er det muligt at uploade flere billeder når en ny blog post gemmes. Den store forskel mellem at uploade en enkelt fil, eller mange i samme POST request er meget lille. I eksemplet herunder, itererer jeg hen over de filer som brugeren har vagt at uploade og gemmer dem ved hjælp af Laravels indbyggede Storage funktion.

{% highlight php %}
// Files upload blog_file
if ($request->hasfile('blog_file')) {
    foreach ($request->file('blog_file') as $key => $blog_file) {
        $path = $blog_file->store(env('IMAGE_THEME_FOLDER'));
        // Store in DB
        $file = File::create([
            'file_name' => $path,
            'role' => $request->blog_file_role[$key],
            'model_name' => 'BlogPost',
            'model_id' => $blog_post->id,
            'mimetype' => $blog_file->getClientMimeType(),
            'priority' => $request->blog_file_priority[$key],
            'file_size' => 'original',
        ]);
        // Create image resize job
        // FIXME: Add more mimetypes as well as upload validation
        if (in_array($blog_file->getClientMimeType(), ["image/jpeg"])) {
            ProcessImage::dispatch($file)->delay(now()->addMinutes(10));
        }
    }
}
{% endhighlight %}

## Kodeforklaring
Først sikrer jeg mig, at der er uploaded en fil. Da det er muligt at uploade flere filer og angive, om det er blog postens primære billede eller et billede som skal være en del af et galleri, skal jeg samtidig have data fra 2 andre arrays. Jeg har brugt env('IMAGE_THEME_FOLDER') for ikke at have en sti gemt i selve koden men i stedet i .env filen.

Men hele magien er denne linje kode:

```
$blog_file->store(env('IMAGE_THEME_FOLDER'));
```
Du behøver ikke mere magi idet Laravel har alt, hvad du skal bruge implementeret i selve requested. Laravel gemmer filen og returnerer en UUID i stedet for filens navn, hvilket vil sige, at i Files tabellen i databasen gemmer jeg 'public/theme_images/5BqQwbuFJ3PB3oyheRsUMy3UdeovdHjP6WJfkfuC.jpeg'.

For at ovenstående kan lade sig gøre, skal du symlinke et par mapper, men det har Laravel også gjort nemt for dig, alt hvad du skal bruge er i denne kommando

```
php artisan storage:link
```
Desværre er det så også her filmen knækker, eller vil knække for mange. For hvordan laver man et symlink hvis den hosting partner man bruger kun tillader dig at uploade filer via ftp? I mange tilfælde er det ikke muligt at symlinke noget som helst.

Men det er sgu stadig smart!