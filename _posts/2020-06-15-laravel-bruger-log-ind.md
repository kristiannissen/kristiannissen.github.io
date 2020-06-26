---
layout: post
title:  "Laravel bruger log ind"
date:   2020-06-15 11:00:00 +0200
categories: laravel
tags: [Laravel, Bruger Log ind]
excerpt_separator: <!--more-->
---
Laravel har en rigtig god ud-af-boksen log ind håndtering men for at holde projeket simpelt, har jeg valgt at implementere min egen løsning baseret på Laravels funktioner.
<!--more-->
## Route::group og middleware
Jeg har en Route::group() som sætter prefix på de routes der hører til administrationen af applikationen.

```
Route::group(['middleware' => 'checkauth', 'prefix' => 'admin'], function(){
  Route::resource('/blogposts/', 'BlogPostController');
  Route::view('/dashboard/', 'dashboard');
});
```
For at sikre brugerne er logget ind når de skal oprette nye blog posts, har jeg lavet en middleware løsning som sikrer at brugeren er kendt når en router fra denne gruppe rammes. Hver gang en route i den gruppe rammes bliver min middleware logik kaldt.

## CheckAuth middleware
Det er latterligt nemt at lave en middleware løsning som sikrer at brugerne er logget ind når de tilgår bestemte dele af din applikation.

Opret middleware
```
php artisan make:middleware navnet-på-middleware
```
I mit tilfælde CheckAuth. Middleware koden oprettes i **app/Http/Middleware** mappen.
````
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Auth;

class CheckAuth
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        Log::debug('middleware checkauth ' . Auth::check());
        if (!Auth::check()) {
            return redirect('/login/');
        }
        return $next($request);
    }
}
```
I min løsning bruger jeg Laravels **Auth::check()** funktion som automatisk sikrer at der er et User object i Requested, du kan undersøge dette ved hjælp af **$request->user()** som giver dig bruger objektet.

Ved at dumpe Auth::check() i loggen kunne jeg se at den metode returnerer en boolean.

For at kunne bruge min middleware løsning i min web router skal min CheckAuth klasse ind i App\Http\Kernel som vist her
```
    protected $routeMiddleware = [
        ...,
        'checkauth' => \App\Http\Middleware\CheckAuth::class
    ];
```
Og det er 'checkauth' du skal bruge når du refererer til din middleware kode i in web router.

Det eneste der nu mangler er en Controller som kan håndtere logikken til at logge brugere ind med.

Jeg valgte at lave en LoginController som indeholder den smule logik der skal til for at logge brugere ind. Jeg har ikke lige nu planer om at man skal kunne registrere sig, hvilket vil sige at brugerne skal være oprettet i databasen for at kunne logge ind.

## LoginController
LoginController'en har kun meget lidt logik, og det er her Laravels indbyggede metoder virkelig hjælper dig til at hurtigt at kunne skrue det her sammen.
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Log;

class LoginController extends Controller
{
    // Show login screen
    public function index()
    {
        return view('login');
    }

    public function authenticate(Request $request)
    {
        $credentials = $request->only('email', 'password');

        if (Auth::attempt($credentials)) {
            return redirect()->intended('/admin/dashboard/');
        }
        return redirect('/login/');
    }

    public function logout(Request $request)
    {
        Auth::logout();
        return redirect('/login/');
    }
}
```
LoginControllerens Index metode returnerer log ind view som bare er en form hvor brugere kan indtaste email der sammen med password er Laravels default måde at identificere sig på.

For at kunne bruge LoginControlleren skal der være en web router.
```
Route::get('login', 'LoginController@index');
Route::post('login', 'LoginController@authenticate');
Route::get('logout', 'LoginController@logout');
```
Det sidste du nu skal er at få oprettet en bruger i databasen og huske at inkludere @csrf i den log ind for du har i dit view.

## Oprettelse af brugere i databsaen
Jeg brugte Tinker til at oprette brugeren med og var overrasket over at opdage, at Laravel ikke pr default hashede brugerens password, jeg kunne først ikke logge ind, indtil jeg lavede et opslag i min database og kunne se at det kodeord jeg havde oprettet min bruger med, var i clear text og ikke hashed.

```
% php artisan tinker
Psy Shell v0.10.4 (PHP 7.3.18 — cli) by Justin Hileman
>>> use App\User;
>>> use Illuminate\Support\Facades\Hash;
>>> $user = new User();
=> App\User {#3972}
>>> $user->email = 'hello@kitty.com';
=> "hello@kitty.com"
>>> $user->password = Hash::make('hellokitty');
=> "$2y$10$RBWqsNhXdm1j9hxAR0AB4ey01mX1yIOxZNfFS98ljoE086AI.qSk6"
>>> $user->name = 'Hello Kitty';
=> "Hello Kitty"
>>> $user->save();
=> true
>>> 
```
Nu har jeg en bruger i min database med email hello@kitty.dk og kodeord hellokitty og er klar til teste løsningen.