---
layout: post
title:  "PHP Kode Udfordring"
date:   2020-06-22 16:34:00 +0200
categories: php
---
Jeg fandt ved et tilfælde Morningtrain.dk's kodeudfordring, den de bruger når de ansætter udviklere, både JavaScript og PHP udfordringen er interessante udfordinger.

### Lav en simpel men fleksibel template parser
Hvilket vil sige, at udfordringen er, at indlæse HTML som indeholder "tokens" og disse "tokens" skal så dynamisk erstattes med PHP kode. Her er et eksempel:
```
<html></body>Hello {name}</body></html>
```
**{name}** skal så ved hjælp af denne template engine kunne erstattes med en værdi der kommer fra et objekt eller Array. For at gøre det hele lidt mere udfordrende valgte jeg, at jeg også ville understøtte funktioner, så **{name}** f.eks kan erstattes med et output der kommer fra en PHP funktion.
```
$key_value_ = [
    'name' => 'Kitty',
    'greeting' => 'Hello',
    'kitty' => function() {
        return 'Whazuup';
    },
];
```
Ja, jeg gider ikke Hello World længere, jeg bruger altid Hello Kitty, eller Hello Pussy når det skal være lidt lummert...

### Template logik
Efter at have implementeret den løsning, syntes jeg det kunne være sjovt at føje et template programmeringslignede sprog til mit løsningsforslag. PHP er allerede et ganske udemærket template sprog, du bruger det når du f.eks skriver følgende i dine .php filer
```
<html>
  <body>
    <?echo "Hello Kitty!" ?>
  </body>
</html>
```
Men mange framework laver deres egen implementering, i Laravel og Symfony bruger du hhv. Blade og Twig. Begge understøtter brugen af f.eks for løkker, statements og al verdens andre muligheder. Så det samme skulle min template engine selvfølgelig også kunne klare.

### Bash som template sprog
Jeg synes selv det er en sjov idé, at bruger Bash's syntaks som template sprog og jeg besluttede at implementere IF statements og FOR løkker, mere gad jeg ikke, ikke lige nu.
```
if [[ 1 + 1 = 2]] then
    echo "1 + 1 = 2"
elif [[ 1 + 1 = 3]] then
    echo "1 + 1 = 3"
fi
```
Ovenstående bliver så parset og ændret til noget der kan afvikles som PHP kode. Det hele er håndteret ved hjælp af preg_replace() som modtager et array med regulære udtryk og et array med de tilsvarende erstatninger. På den måde bliver
```
if [[ 1 + 1 = 2]] then
```
Til PHP kode som følgende
```
<?php if (1 + 1 == 2): ?>
```
På samme måde gjorde jeg det med FOR løkker og variabler. Du kan se hele projektet her, jeg har navngivet det [MorningCoffee](https://github.com/kristiannissen/morningcoffee)

### OOP løsning
Bare for at vise lidt erfaring med OOP programmering i PHP valgte jeg at lave gøre brug af dependency injection (mener jeg det hedder) så løsningen er nem at ændre hvis man hellere vil have et andet template sprog end mit Bash lignende sprog.
Alt hvad man skal gøre er, at skrive en ny parser der implementerer [ParserInterface](https://github.com/kristiannissen/morningcoffee/blob/master/src/MorningCoffee/ParserInterface.php). Ved instantiering af Coffee klassen bruger man så den parser man gerne vil bruge.
```
$my_coffee = new Coffee(new BashParser);
```
Vil man gerne have mulighed for flere bask muligheder kan man føje dem til BashParser'ens pattern og replacements.

Det var en sjov udfordring, og bare fordi jeg synes det var sjovt, sendte jeg mit løsningsforslal til Morningtrain.dk