---
layout: post
title:  "Go lang pakker"
date:   2020-12-02 13:35:00 +0200
categories: golang
tags: [golang]
excerpt_separator: <!--more-->
---
Pakker i Go giver dig muligheden for at organisere din applikation og samtidig skabe en række microservices. Hver enkelt pakke i Go er netop tænkt som en microservice som andre brugere kan importere ind i deres projekt.
<!--more-->
Inden jeg laver min pakke sikrer jeg mig at mit setup kører ved at skrive den klassiske Hello Kitty funktion.
```
package main

import "fmt"

func main() {
	fmt.Println("Hello Kitty")
}
```
Ovenstående program skulle gerne vise dig teksten "Hello Kitty" i din terminal når du afiklver kommandoen `go run main.go`.
### Go pakker
Nu hvor jeg ved Go kører som det skal, er det tid til at lave en pakke. I dette eksempel skal min pakke bare hedde "kitty", derfor opretter jeg mappen kitty i roden af mit projekt som vist her under
```
├── kitty
│   └── kitty.go
└── main.go
```
I mappen kitty opretter jeg en fil med samme navn som mappen, det er ikke et krav, men konventionen indenfor Go og jeg skal om et øjeblik vise hvorfor.
For at det giver mening at lave en pakke, skal der være en funktion i pakken som kan importeres. I mit tilfælde skal min kitty pakke kunne sige en knurrende lyd, pakken hedder jo kitty af en grund. Indholdet af kitty.go.
```
package kitty

import "fmt"

func init() {
    fmt.Println("I am Kitty")
}

func purr() string {
    return "purr purr"
}
```
Nu skal kitty pakken så importeres, hvilket jeg vil gøre i min main.go fil på følgende måde.
```
package main

import (
    "fmt"
    "kitty"
)

func main() {
    fmt.Println("Hello Kitty, I say "+ kitty.purr())
}
```
Nu kan jeg så afvikle mit program i terminalen og får følgende output
```
# command-line-arguments
./main.go:9:40: cannot refer to unexported name kitty.purr
./main.go:9:40: undefined: kitty.purr
```
Oh No! problemer...

![Creditcs mickey_rat91](https://tenor.com/view/kermit-worried-oh-no-anxious-gif-11565777 "Oh No!")
### Store og små bogstaver
Når du arbejder med pakker skal de funktioner du vil eksportere have stort begyndelses bogstav. Det vil sige at functionen purr() ikke kan exporteres mens functionen Pur() kan exporteres.
Så hvis jeg ændrer min kode til nedenstående vil det virke.
```
package kitty

import "fmt"

func init() {
    fmt.Println("I am Kitty")
}

func Purr() string {
    return "purr purr"
}
```
Nu kan jeg bruge Purr() funktionen i min main function som vist her under
```
package main

import (
    "fmt"
    "kitty"
)

func main() {
    fmt.Println("Hello Kitty, I say "+ kitty.Purr())
}
```
Bemærk at jeg prefixer min Purr funktion med kitty, dette er en reference til pakken og filen kitty.go. Jeg kan også bruge et alias når jeg importerer kitty pakken i min main fil som vist her under
```
package main

import (
    "fmt"
    k "kitty"
)

func main() {
    fmt.Println("Hello Kitty, I say "+ k.Purr())
}
```
Det sidste jeg nu mangler er at sikre, at min Go kode er formateret korrekt, hvordan du gør det, kan du læse om mit tidligere indlæg [Go lang introduktion](/docker/2020/11/30/golang-introduktion.html).
### Mappe struktur best practice
Der er en masse på nettet om hvordan man skal navngive og strukturere dit Go projekt, andre stedet har jeg læst at de er op til dig, hvordan du ønsker at gøre det. Der er selvfølgelig en vis fornuft i at følge en struktur som andre der ønsker at anvende din pakke kan genkende.

Det sidste jeg mangler at forklare, er den init() funktion som jeg har gemt i kitty.go.
Det er en af Go's magiske funktioner, den afvikles automatisk når pakken importeres. Det er vigtigt at huske, den funktion kan du gøre af, hvis du skal angive en default værdi i de variabler du sætter i din pakke, eller til anden instatioering af din pakke.
Når du afvikler programmet via terminalen vil du se følgende
```
I am Kitty
Hello Kitty, I say purr purr
```
Her kan du se at init funktionen fra kitty.go afvikles ved import, den vises i terminalen over main funktionen.