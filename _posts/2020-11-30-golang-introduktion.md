---
layout: post
title:  "Go lang introduktion"
date:   2020-11-30 13:35:00 +0200
categories: docker
tags: [Docker]
excerpt_separator: <!--more-->
---
Jeg ved ikke præcist, hvad der tiltaler mig ved programmeringssproget Go, måske er det den lidt funky syntaks som minder mig om Perl og Ruby.
<!--more-->
### Opsætning, gofmt og logging
Hvis du bruger Docker kan du bruge følgende Dockerfile til at komme i gang med

```
FROM golang:latest

RUN apt-get -y update
RUN apt-get -y upgrade
```
Efter du har bygget din docker container kan du bruge nedenstående kommando til at starte den
```
docker run -it -p 80:80 -v $(pwd)/workdir/:/go/src [image id]
```
Hvor -p er porten du skal bruge for at kunne forbi via localhost, -v er den mappe du arbejder i lokalt og vær opmærksom på, at du skal mappe den til go/src ellers kan Go ikke finde sig selv.
### Hello Kitty & gofmt
Følgende er en nyfortolkning af den klassiske Hello World!
```
package main

import (
  "fmt"
)
func main() {
  fmt.Println("Hello Kitty")
}
```
Gem ovenstående som main.go og brug følgende kommando for at afvikle koden
````
go run main.go
```
I konsollen skulle du nu gerne se "**Hello Kitty**".
For at få formateret din Go kode kan du via kommandolinjen bruge
```
gofmt -w */**.go
```
Det indenterer Go koden i henhold til standarden, så slipper du for diskutioner om tabs og spaces, har du set Silicon Valley serien ved du hvad jeg mener.
### Logging og Stderr
Det næste vi skal se på, er hvordan du skriver til loggen så du kan logge kode output mens du arbejder, det er altid noget af det første jeg leder efter når jeg går i gang med et nyt sprog.
Åben main.go filen igen og importer følgende pakke
```
package main

import (
  "log"
  "os"
  "fmt"
)
func main() {
  name := "Kitty" // name er en streng
  fmt.Fprintf(os.Stderr, "Hello %s\n", name)
  fmt.Println("Hello")
  log.Fatalf("Oh no!")
}
```
I dette lille program, importerer du 2 nye pakker log og os, vær opmærksom på, at importerer du en pakke som du ikke bruger, får du en fejl når du vil afvikle programmet. Go er typestærkt så alle variabler skal erklæres med deres korrekte type, int, string, float osv. Men Go har også en smart funktion, lig den der findes i C#, hvor Go selv finder ud af typen når du bruger **:=** som her **name := "Kitty"**.

I programmet bruger jeg 2 forskellige måder at skrive til konsollen på og den sidste får programmet til at stoppe. Når du nu afvikler koden skulle du gerne se følgende
```
Hello Kitty
Hello
2020/11/30 12:50:05 Oh no!
exit status 1
```
### Formatering af kode
En af fordelene ved at bruge gofmt er at du får rettet indenteringen og rækkefølgen pakkerne du bruger importeres ændres så de står i alfabetisk orden. Prøv at afvikle "gofmt -w main.go" og åben filen, så kan du se ændringen
```
package main

import (
	"fmt"
	"log"
	"os"
)

func main() {
	name := "Kitty" // name er en streng
	fmt.Fprintf(os.Stderr, "Hello %s\n", name)
	fmt.Println("Hello")
	log.Fatalf("Oh no!")
}
```
Gofmt er Go's gave til dovne udviklere.