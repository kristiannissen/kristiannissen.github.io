---
layout: post
title:  "Laravel og Google Cloud Datastore"
date:   2020-06-26 09:14:00 +0200
categories: php Laravel datastore
tags: [Laravel, Google Cloud Datastore]
excerpt_separator: <!--more-->
---
Det er muligt at bruge Google Cloud til at hoste en Laravel applikation, jeg har leget med Google Cloud igennem flere år, specielt Google Datastore synes jeg er interessant fordi arkitekturen er speciel og kræver at du udvikler din data model anderledes end når du arbejder med en mere traditionel relationel database.
<!--more-->
## Deploy til Google Cloud
Google har dokumenteret hvordan det er muligt at deploye en [Laravel applikation](https://cloud.google.com/community/tutorials/run-laravel-on-appengine-standard) til deres platform, det hele er som når du gør brug af f.eks Python, fremgangsmåden er meget det samme.

Men deres dokumentation gør brug af MySQL som er en klassisk relationel database, hvilket selvfølgelig betyder at du nemt kan bruge hele Laravel frameworket out of the box inkl. Eloquent som er Laravels ActiveRecord ORM. Hvilket minder mig om, at jeg en gang sammen med en tidligere ansat, udviklede [Uteeni](https://github.com/nueaf/uteeni) som et ActiveRecord baseret på PHP.
## Google Datastore og Laravel
Google Datastore stiller nogle interessante krav til din data model idet det ikke er muligt f.eks at lave joins, hvilket i Eloquents tilfælde betyder, at hasMany og hasOne ikke skal implementeres.

Der er allerede et par interessante projekter på Github der er værd at undersøge nærmere, men de er af ældre dato og ikke lavet til den seneste version af Laravel.
* [Dale Mooney](https://github.com/dalejmooney/laravel-google-datastore)
* [Conrad Carpenter](https://github.com/czim/laravel-datastore)
Men begge projekterne er ældre og vedligeholdes ikke længere aktivt, hvilket aldrig er et godt tegn da der løbende kommer nye opdateringer til Laravel.

En anden interessant ting ved Googles Datastore er, at det ser ud til, at Firestore nu skal [erstatte datastore](https://cloud.google.com/datastore/docs/upgrade-to-firestore)